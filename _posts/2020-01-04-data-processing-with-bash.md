---
layout: post
title: Data Processing with Bash
category: ''
tags: [Bash]

---

Inspired by [this post](https://adamdrake.com/command-line-tools-can-be-235x-faster-than-your-hadoop-cluster.html), I built a pipeline to extract facilities from ~700K hotels, combine all repeated facilities and rank by number of occurences, all in Bash.

TL;DR commands:
```bash
echo "supplier_id,supplier_value,mapping_type" > header.csv

cat hotel-facility-dump | \                 #read from file
rg "facility:" | \                          #find relevant logs
awk -F'facility: ' '{print $2}' | \         #extract content in the form of 'facility name,facility code'
sort | \                                    #sort for the next step
uniq -c | \                                 #get all unique entries with count
sort --numeric-sort --reverse | \           #get entries with count in descending order
head -n 500 | \                             #take top 500 entries
awk -F ' "' '{print 12345",""\42"$2}' > \   #remove count and put back double quote
hotel-processed                             #write to output file

cat header.txt hotel-processed > hotel-facilites.csv
```

<!--excerpt-->

# Problem
When integrating third party suppliers of hotels into Agoda, a crucial step is to import and map content. The content ranges from hotel and room names to images and finally to facilities offered at the hotel and room level. Hotel facilities could include swimming pools, restaurants and valet parking while room facilities could be things like free WiFi access, a microwave, or toiletries.

The problem I solve in this post is to collect all facilities across 700K hotels, clean them up, and send this to another colleague who would then map it to Agoda facility codes. The key observation here is that facilities are repeated across many different hotels and rooms.

# Solution Breakdown

## Creating hotel-facility-dump
Having already written the parser to extract data from the supplier API, I was loathe to write another XML extractor in something like Python. Therefore I extended the parser to extract the facility information, then wrote a dumper that requested all hotels and simply logged hotel facilities as `"facility: " + hotelFacility + ",HF"` and room facilities as `"facility: " + roomFacilities + ",HF"`. I then ran the program and dumped all logs to a file `hotel-facility-dump`.

## Extracting facility information from log
To extract the facility information from the raw logs, I turned to [grep](http://man7.org/linux/man-pages/man1/grep.1.html). However, there is a more performant version of it called [ripgrep](https://blog.burntsushi.net/ripgrep/). I also need to get rid of the extraneous logger output (such as datetime) and the `facility:` marker. I do this with [awk](https://linux.die.net/man/1/awk). `-F` takes a string to be used as a field separator and `$1`, `$2` and so on are the indexed results of the split on the field separator. Since I want everything after `facility: `, I use `'{print $2}'` as the argument to awk.

Command:
```bash
cat hotel-facility-dump | \
rg "facility:" | \
awk -F 'facility: ' '{print $2}'
```

Output:
```
"Swimming pool view",RF
"Sea view",RF
"TV",RF
"Bathroom",RF
"Shower",RF
"Bath",RF
"Hairdryer",RF
"Radio",RF
"Toilet",RF
"WiFi in room (extra fee)",RF
"Daily cleaning service",RF
...(for 3177611 lines)
```

## Getting statistics on occurences of the same facility
At this point we have many occurences of the same facility in the ~3M rows of data.
```
"Free wifi",HF
...
"Free wifi",HF
...
"Free wifi",HF
```
To group by each unique facility string, we can use the [uniq](http://man7.org/linux/man-pages/man1/uniq.1.html) utility. However, `uniq` requires the repeated rows to be adjacent, so we need to [sort](http://man7.org/linux/man-pages/man1/sort.1.html) them first.

Command:
```bash
cat hotel-facility-dump | \
rg "facility:" | \
awk -F 'facility: ' '{print $2}' | \
sort | \
uniq
```

However, the output is not super useful. To get a count of occurences, we can use `uniq -c`. To get the facilities with the highest occurence, we need to sort again, this time in reverse. Since `uniq -c` prepends a count to each row, we need to pass the `--numeric-sort` flag to sort to treat the start of the row as a numeric value. We can then see the ten most common facilities with `head`.

Command:
```bash
cat hotel-facility-dump | \
rg "facility:" | \
awk -F 'facility: ' '{print $2}' | \
sort | \
uniq -c | \
sort --numeric-sort --reverse | \
head
```

Output:
```
63809 "Free WiFi",HF
63374 "Non-Smoking",RF
62479 "Free WiFi",RF
55164 "Private bathroom",RF
53162 "Daily housekeeping",RF
51148 "Air conditioning",RF
48823 "Free toiletries",RF
47686 "24-hour front desk",HF
46231 "Free self parking",HF
45389 "Smoke-free property",HF
```

There are 41779 unique facilities. There are so many because some differ from others by one space (may be a data entry problem), some describe the same facility but with different words, and some contain non-English words. Anyway I take the top 500 most common facilities with `head -n 500`.

## Wrangling into desired csv format
I still need to go from this to the right csv format. For example, my colleague doesn't need to know how many occurences there were for each facility. I do something really hacky with awk by splitting on  "(space double-quote). For example, `63809 "Free WiFi",HF` would be split into `63809` and `Free WiFi",HF`. Now I have to put the double quote back. It turns out that my version of awk uses octal to specify an escaped character. As 42 is the octal code for the double quote character in ASCII, the final awk command is 

`awk -F ' "' '{print 12345",""\42"$2}'`.

Final commands:
```bash
echo "supplier_id,supplier_value,mapping_type" > header.csv

cat hotel-facility-dump | \
rg "facility:" | \
awk -F'facility: ' '{print $2}' | \
sort | \
uniq -c | \
sort --numeric-sort --reverse | \
head -n 500 | \
awk -F ' "' '{print 12345",""\42"$2}' > \
hotel-processed

cat header.txt hotel-processed > hotel-facilites.csv
```

Output(hotel-facilites.csv):
```
supplier_id,supplier_value,mapping_type
12345,"Free WiFi",HF
12345,"Non-Smoking",RF
12345,"Free WiFi",RF
12345,"Private bathroom",RF
12345,"Daily housekeeping",RF
...
```

# Performance
```bash
time cat hotel-facility-dump | rg "facility:" | awk -F'facility: ' '{print $2}' | sort | uniq -c | sort --numeric-sort --reverse | head -n 500 | awk -F ' "' '{print 12345",""\42"$2}' > hotel-processed
cat hotel-facility-dump  0.04s user 0.41s system 2% cpu 15.121 total
rg "facility:"  1.76s user 0.24s system 13% cpu 15.122 total
awk -F 'facility: ' '{print $2}'  15.04s user 0.04s system 99% cpu 15.123 total
sort  4.47s user 0.21s system 24% cpu 19.289 total
uniq -c  0.70s user 0.01s system 3% cpu 19.287 total
sort --numeric-sort --reverse  0.07s user 0.01s system 0% cpu 19.339 total
head -n 500  0.00s user 0.00s system 0% cpu 19.337 total
awk -F ' "' '{print 12345",""\42"$2}' > hotel-processed  0.00s user 0.01s system 0% cpu 19.338 total
```

About 19 seconds total. Good enough.