# coding: utf-8

require 'digest/sha1'

namespace :html do
  # ディレクトリ構成
  # hoge/
  # ├ index.html
  # └ files/ ┬ #{sakuhin1}/ ┬ sakuhin1-story1.html
  #          │              └ sakuhin1-story2.html
  #          ├ #{sakuhin2}/ ┬ sakuhin2-story1.html
  #          │              └ sakuhin2-story2.html
  #          └ #{sakuhin3}/ ┬ sakuhin3-term1/ ┬ sakuhin2-term1-story1.html
  #                         │                 └ sakuhin2-term2-story2.html
  #                         └ sakuhin4-term2/ ┬ sakuhin2-term1-story1.html
  #                                           └ sakuhin2-term2-story2.html

HTML_PREFIX = "<!DOCTYPE html><html><head><meta charset=\"utf-8\"><link href=\"style.css\" rel=\"stylesheet\" type=\"text/css\"><title>アニメ</title><script type=\"text/javascript\" src=\"main.js\"></script></head><body>"
HTML_SUFFIX = "</body></html>"
FILE_EXTENSION = "*.mp4"
START_DIRECTORY = "files/"
FILE_TITLE = "Videos"

desc "Index を作成する"
task :create_index do
  open("index.html", "w") { |log|
    log.puts HTML_PREFIX
    log.puts start(START_DIRECTORY)
    log.puts HTML_SUFFIX
  }
end

# スタート
def start(dir)
  result = ""
  cd dir do
    result = explorer()
  end
  return result
end

# ディレクトリ探索
def explorer()
  index_html = ""
  Dir.glob("*/").each { |dir|
    index_html += "<div><a href=\"#{Dir.pwd.encode("utf-8")}/#{dir}index.html\">#{dir}</a><br>"
    cd dir do
      indexer()
      index_html += explorer()
    end
    index_html += "</div>"
  }
  return index_html
end

# インデックス作成
def indexer()
  open("index.html", "w") { |log|
    log.puts HTML_PREFIX.gsub(/\"style.css\"/, "\"" + File.dirname(__FILE__) + "/" + "style.css\"").gsub(/\"main.js\"/, "\"" + File.dirname(__FILE__) + "/" + "main.js\"")
    title = Dir.pwd.split("/").last.encode("utf-8")
    log.puts "<h1>#{title}</h1>"
    log.puts "<h2>Index</h2>"
    log.puts "<div id='index'><ul>"
    Dir.glob(FILE_EXTENSION).each_with_index { |video, i|
      log.puts "<li><a href=\"##{i}\">#{video}</a></li>"
    }
    log.puts "</ul></div>"
    log.puts "<h2>" + FILE_TITLE + "</h2>"
    Dir.glob(FILE_EXTENSION).each_with_index { |video, i|
      log.puts "<div><h3 id=\"#{i}\">#{video}</h3><video src=\"#{video}\" controls>#{video}</video></div>"
    }
    log.puts "<a href=\"#{File.dirname(__FILE__)}/index.html\">戻る</a>"
    log.puts HTML_SUFFIX
  }
end

end

SPACE_STRING = " "
TRUE_STRING = "TRUE"
FALSE_STRING = "FALSE"
CSC_STRING = " : "

desc "ファイルを sha1 チェックして重複の管理を行う"
task :sha1_create do
open("sha1.txt", "w") { |log|
  Dir.glob(FILE_EXTENSION).each do |file|
    filename = File.basename(file)
    log.puts filename + SPACE_STRING + Digest::SHA1.hexdigest(File.binread(filename));
  end
}
end

desc "作成した sha1 リストから重複を探す"
task :sha1_check do

hash = Hash.new()
array = Array.new

open("sha1.txt", "r:utf-8") { |log|
  while line = log.gets
    print line
    s = line.split(' ')
    if s.length == 2 then
      if hash[s[1]] != nil then
        array[array.length] = s
        # print TRUE_STRING + CSC_STRING + line
      else
        hash[s[1]] = s[0]
        # print FALSE_STRING + CSC_STRING + line
      end
    end
  end
}
open("delete.txt", "w") { |result|
  for line in array do
    for v in line do
      result.print v
      result.print " "
    end
    result.puts ""
  end
}
end
