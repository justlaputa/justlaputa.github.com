---
layout: post
title: "Switch to octpress"
date: 2012-10-14 00:09
comments: true
categories: [hack]
---

Trying to switch blog generator from pelican to octpress.

And I have created a little ruby script to convert pelican markdown post to octpress post. Which actually converts the post head like:

<!--more-->

In pelican:

    Date: 2012-09-02    
    Title: Using Awesome in Ubuntu 12.04  
    Category: Dev  
    Tags: ubuntu, awesome, wm, linux  
    Slug: awesome-on-ubuntu
    Lang: en  
    Author: laputa  

to in Octpress:

    ---
    layout: post
    title: "Using Awesome in Ubuntu 12.04"
    date: 2012-09-02
    comments: true
    categories: Dev
    tags: [ubuntu, awesome, wm, linux]
    ---

and also copy the file to YYYY-MM-DD-Title.markdown as Jekyll requires.

Here is the script, a little ugly, but can be used:

`convert.rb:`

    #!/usr/bin/env ruby
    
    require 'fileutils'
    
    tmpFile = open("tmp-file", "w")
    tmpFile.puts "---"
    tmpFile.puts "layout: post"
    
    open(ARGV[0]) { |file|
      inHead = true
      inContent = false
      date = "2012-10-14"
      title = "tmp-file"
      categories = "dev"
      tags = ""
      file.readlines.each{ |line|
        if line.start_with?("Date:")
          date = line.split(":")[1].strip
        elsif line.start_with?("Title:")
          title = line.split(":")[1].strip
        elsif line.start_with?("Category:")
          categories = line.split(":")[1].strip
        elsif line.start_with?("Tags:")
          tags = line.split(":")[1].strip
        elsif line.match(/^[A-Z][a-z]+: [^:]+$/)
        else
          inHead = false
        end
    
        if ! inHead && ! inContent
          tmpFile.puts "title: \"#{title}\""
          tmpFile.puts "date: #{date}"
          tmpFile.puts "comments: true"
          tmpFile.puts "categories: #{categories}"
          tmpFile.puts "tags: [#{tags}]"
          tmpFile.puts "---"
    
          inContent = true
        end
    
        if ! inHead && inContent
          tmpFile.puts line
        end
      }
    
      tmpFile.close
    
      FileUtils.mv "tmp-file", "#{date}-#{title.downcase.split.join("-")}.markdown"
    }

I use this command to convert all my pelican `*.md` files to octpress `*.markdown` files:

    find . -name "*.md" | xargs -n 1 ./convert.rb