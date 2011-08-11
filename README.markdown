# Introduction
This is an example application that streams data generate from a csv template file. You can try the application by downloading a dynamically generated csv file from [the heroku hosted version](http://testcsvstreamer.herokuapp.com/tests/massive/example.csv). 

This application is meant to be a simple working example of [csv_streamer](http://www.github/fawce/csv_builder), which is a fork of csv_builder. You can see the response run over a long period of time in the network tab of Chrome's developer console. csv_streamer adds support for streaming the output of a template as it is generated, to avoid timeouts for generation of very large csv files.


# Heroku Deployment
A big part of the motivation for this project was to ensure that csv_streamer would work on Heroku. Streaming data on the fly is a marquee feature of Rails 3.1, but streaming depends on your web server as much as your application code. The key object in rails is the ActionController's document_body object. The document_body ultimately writes data to the web server's response io stream. Rails uses Rack, which I learned along the way, is a standard interface to webservers. The best thing about Rack, is it allows you to very easily switch your web server. Rails works with WEBrick, Mongrel, and Unicorn. Of these, I have verified that Mongrel and Unicorn implement streaming IO, while WEBrick always caches responses before sending the data. This means that even though the code in the template handler is streaming data, WEBrick will still cache it all and send it to the client in one shot.

Unicorn was originally derived from Mongrel, but has recently eclipsed it in popularity because of its process management design. While mongrel on heroku is probably possible, Unicorn on Heroku is far more common. I relied heavily on [this wonderful unicorn on heroku howto from Michael van Rooijen's blog](http://michaelvanrooijen.com/articles/2011/06/01-more-concurrency-on-a-single-heroku-dyno-with-the-new-celadon-cedar-stack/) in my configuration of this project. This test project includes the config/unicorn.rb and Procfile Michael describes in his blog, so to publish this application to heroku, you simply need to use the _Cedar_ application stack. The relevant git and heroku commands, again from Michael, are:

> git init

> git add . && git commit -m "concurrecy"

> heroku create --stack cedar

> git push heroku master

# Enjoy!