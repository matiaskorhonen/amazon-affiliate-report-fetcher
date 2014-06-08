# encoding: UTF-8

require "csv"
require "fileutils"
require "open3"
require "yaml"
require "shellwords"

# https://gist.github.com/jpmckinney/668628
class KeyChain
  def self.method_missing(meth, *args)
    run args.unshift(meth)
  end

  def self.find_internet_password(*args)
    # -g: Display the password for the item found
    output = quiet args.unshift('find-internet-password', '-g')
    output[/^password: "(.*)"$/, 1]
  end

private

  def self.run(*args)
    `security #{args.join(' ')}`
  end

  def self.quiet(*args)
    run args.unshift('2>&1 >/dev/null')
  end
end

def user
  Shellwords.escape(config.fetch("user").strip)
end

def password
  Shellwords.escape(KeyChain.find_internet_password('-s', 'www.amazon.com'))
end

def command(type: :orders, locale: :US)
  "phantomjs --ignore-ssl-errors=yes --local-to-remote-url-access=yes olivaw/olivaw.js #{user} #{password} #{locale} #{type} QuarterToDate csv"
end

def config
  @config ||= YAML.load_file("config.yml")
end

namespace :amazon do
  desc "Delete the old reports"
  task :clear do
    Dir["*.csv"].each do |path|
      FileUtils.remove_entry_secure path
    end
  end

  desc "Get orders reports"
  task :reports do
    %w(CA DE FR UK US ES).each do |locale|
      puts "Starting #{locale} orders request"
      stdin, stdout, stderr, orders_thr   = Open3.popen3(command(type: :orders, locale: locale))
      orders_thr.join

      puts "Starting #{locale} earnings request"
      stdin, stdout, stderr, earnings_thr = Open3.popen3(command(type: :earnings, locale: locale))
      earnings_thr.join
    end
  end

  desc "Make the CSVs Numbers.app compatible"
  task :numbers do
    puts "Reprocessing TSVs to CSVs"
    Dir["*.csv"].each do |path|
      puts path
      text = File.open(path).read

      CSV.open(path, "wb") do |csv|
        CSV.parse(text, { col_sep: "\t", quote_char: "\u0007" }) do |row|
          row = row.to_a
          unless row[0] && (row[0].include?("All traffic prior to") || row[0].include?("Items with no orders"))
            csv << row
          end
        end
      end
    end
  end
end

task default: ["amazon:clear", "amazon:reports", "amazon:numbers"]
