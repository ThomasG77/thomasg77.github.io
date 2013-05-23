---
published: true
layout: post
title: "Multi-languages one liner web servers!"
date: 2013-05-24T00:40:34.000Z
tags: web server tools
categories: server

---

# One liner web server

You have a lot of uses cases where you need to have a web server, like when you use ajax calls. We will review some way to do it with different programming languages.

## Ruby

    ruby -rwebrick -e"WEBrick::HTTPServer.new(:DocumentRoot => '.', :Port => 0).start"

## Python

### Version 2
    python -m SimpleHTTPServer

### Version 3
    python3 -m http.server

## Node

Not built-in so install doing
    npm install http-server -g
and launch
    http-server

## PHP (>= 5.4)
    php -S localhost:8080

## Perl
Not built-in so install doing

    perl -MCPAN -e 'install HTTP::Server::Brick'
and launch

    perl -MHTTP::Server::Brick -e '$s=HTTP::Server::Brick->new(port=>8081); $s->mount("/"=>{path=>"/home/thomas/code"}); $s->start'
    
Feel free to submit others examples with a Github push request going to [this post in my blog repository](https://github.com/ThomasG77/thomasg77.github.io/tree/master/_posts/2013-05-24-your-filename.md)
I will add credits if you do so !!
    
