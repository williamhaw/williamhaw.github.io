abort('Please run this using `bundle exec rake`') unless ENV["BUNDLE_BIN_PATH"]
require 'html-proofer'

task :html_proofer do
    build_dir = File.join(File.dirname( __FILE__ ), '_site')
    unless File.directory?('test/_site')
      `jekyll build -d #{build_dir} -V`
    end
    opts = {
      url_ignore: [/localhost/],
      empty_alt_ignore: true,
      check_html: true,
      only_4xx: true,
      check_favicon: true,
      file_ignore: [/assets\/katex\/index.html/],
      typhoeus: {
        ssl_verifyhost: 0,
        ssl_verifypeer: false,
        timeout: 30
      }
    }
    HTMLProofer.check_directory(build_dir, opts).run
  end