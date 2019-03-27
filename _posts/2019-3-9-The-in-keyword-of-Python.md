---
layout: post
title: The in keyword of Python explained
category: Under The Hood
tags: [Python]
---

When learning Python, one of the first things we do with lists, dictionaries and other iterators[^1] is something like the following:

*(All examples are in Python 3.6.6)*

```python
for i in mylist:
        do_something(i)
```
This loops through each element in `mylist` and calls the `do_something` function on it.
<!--excerpt-->
The other use of the `in` keyword, however, is to check if an element is present in some sort of container (lists, sets, dictionaries, etc).

```python
if 1 in mylist:
        do_something()
```

But what's going on under the hood?

1. How does the iterator know which element to assign to `i` in each iteration of the for loop?
2. How does the container know to check itself for the presence of the given element?

The answer is that the `in` keyword tells the Python runtime to check for the presence of magic functions implemented by the iterator or container. The way Python does this lookup is usually referred to as [duck typing](https://en.wikipedia.org/wiki/Duck_typing). This concept is also present in other so-called "dynamic" languages (Ruby and Javascript come to mind).

# iter(), \_\_iter\_\_() and \_\_next\_\_()

The first two magic functions that all iterators implement are `__iter__()`[^2] and `__next__()`[^3].

For example:

```python
class MyIterator:
    def __init__(self, mylist):
            self.data = mylist
            self.index = 0
    def __iter__(self):
            return self
    def __next__(self):
            if self.index < len(self.data):
                    ret = self.data[self.index]
                    self.index += 1
                    return ret
            else:
                    raise StopIteration

for i in MyIterator([1, 2, 3]):
        print(i)
```
gives
```
1
2
3
```

The `for` keyword calls `iter()`[^4] on MyIterator which invokes the `__iter__()` method. Afterwards on each successive iteration of the loop, it calls `__next__()` which I have written to return the next element of the list.



# \_\_contains\_\_()

To be able to test membership in your container, the `__contains__()`[^5] method must be implemented.

```python
class MyContainer:
    def __init__(self, mylist):
        self.data = mylist
    def __contains__(self, element):
        return element in self.data

my_container = MyContainer([2, 42, 27, 101])
print(42 in my_container)
print(22 in my_container)
```
gives
```
True
False
```

# Both at once!

Putting them together:

```python
class MyIterator:
    def __init__(self, mylist):
            self.data = mylist
            self.index = 0
    def __iter__(self):
            return self
    def __next__(self):
            if self.index < len(self.data):
                    ret = self.data[self.index]
                    self.index += 1
                    return ret
            else:
                    raise StopIteration
    def __contains__(self, element):
    	return element in self.data

my_iterator = MyIterator([1, 2, 4, 5, 6, 7])

for i in my_iterator:
    print(i)

for i in my_iterator:
    print(i)

print(4 in my_iterator)
```

Output:
```bash
$ python3 iterator.py
1
2
4
5
6
7
True
```

You may notice however, that `self.index` never gets reset to 0 within the `next()` method, so the second for loop over `my_iterator` doesn't get executed. 

Also, the `__iter__()` method should reset `self.index` every time it is called because if the for loop gets interrupted, the next for loop to iterate over `MyIterator` starts from where the last loop stopped. However, you may desire this behaviour if you wish the iterator to pick up from where it left off. (Perhaps in an implementation of a message queue where you want consumers to continue even if one or more fail, but that's out of the scope of this article.)

Here we end up with the complete implementation.

```python
class MyIterator:
    def __init__(self, mylist):
        self.data = mylist
        self.index = 0
    def __iter__(self):
        self.index = 0
        return self
    def __next__(self):
        if self.index < len(self.data):
                ret = self.data[self.index]
                self.index += 1
                return ret
        else:
                self.index = 0
                raise StopIteration
    def __contains__(self, element):
    	return element in self.data

my_iterator = MyIterator([1, 2, 4, 5, 6, 7])

try:
    for i in my_iterator:
        if i > 2:
            raise Error
            print(i)
except:
    print("Early termination")

for i in my_iterator:
    print(i)

print(4 in my_iterator)
```

And this gives the expected output:

```bash
$ python3 iterator.py
1
2
Early termination
1
2
4
5
6
7
True
```

Edit: Fixed list naming in example to avoid using the `list` keyword as a variable name

## References:

[^1]: [iterator](https://docs.python.org/3/glossary.html#term-iterator)
[^2]: [\_\_iter\_\_()](https://docs.python.org/3/reference/datamodel.html#object.__iter__)
[^3]: [\_\_next\_\_()](https://docs.python.org/3/library/stdtypes.html#iterator.__next__)
[^4]: [iter()](https://docs.python.org/3/library/functions.html#iter)
[^5]: [\_\_contains\_\_()](https://docs.python.org/3/reference/datamodel.html#object.__contains__)