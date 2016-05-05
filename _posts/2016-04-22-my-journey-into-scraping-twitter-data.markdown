---
layout: post
title: "My Journey Into Scraping Twitter and/or Reddit Data"
tags: digitalhumanities dh twitter data scraping
author: Cooper Beilby
image: Melchior_de_Hondecoeter_-_Das_Vogelkonzert.jpg
imagesrc: Melchior de Hondecoeter - Das Vogelkonzert
---

As part of my digital humanities project, I am considering collecting and analysing tweets and/or reddit discussions on the #INeedMasculism #INeedMasculinism #INeedMasculismBecause hashtag(s). I am hoping to get some proof of concept so that I can employ this method or something similar in my research for my MRES next year. As this is a pretty new idea for me, I'm going to be documenting my journey here as a sort of resource journal. 

At first I had been struggling to find some information on how I would go about collecting data. I found some information about topsy.com, a service run by Apple that collected tweets and allowed end users to search that data in a variety of ways. Unfortunately, the service has now been shutdown. 

## What Data to Collect

Data I am likely to want in addition to the actual tweet content is username, time/date, retweets and pings (I'm not sure what twitter calls it when you tag someone?). I will probably also want to collect some data about each user - profile picture, age, gender and other details available on profiles. I am likely to want similar data for Reddit. A quick search of reddit turns up very little for both #INeedMasculinism and #INeedMasculism. However, there are quite a few threads/posts discussing #INeedMasculinismBecause

## Starting Out With Twitter

According to the article on [Knightlab](http://knightlab.northwestern.edu/2014/03/15/a-beginners-guide-to-collecting-twitter-data-and-a-bit-of-web-scraping/), the twitter API imposes some limits on how many calls can be made within a certain window. The Knightlab article suggests a few good tips about setting up a cron job to fetch data every 15mins and ensuring a key cycle between keys to stay within limits. I might need to set up a NeCTAR virtual machine with a cron job to do this for me and not have to keep a local machine running. From this [StackOverflow](http://stackoverflow.com/questions/2714471/twitter-api-display-all-tweets-with-a-certain-hashtag) forum thread, it seems like the twitter API might not be too difficult to use as it appears to just involve JSON-RPC requests. It might actually be fairly easy to just write a quick python script to collect the data and place it into a database. This would certainly simplify running on a virtual NeCTAR machine. However, if something already exists I would rather use that.

## Scraping Reddit Data

When I started looking into scraping Reddit data, I saw in [this post](https://www.reddit.com/r/TheoryOfReddit/comments/2hg53b/q_how_can_i_collect_raw_data_from_reddit/) a suggestion that the simplest way is to use PRAW. PRAW is a python module that interfaces with the [Reddit API](https://www.reddit.com/dev/api). 

## A Collection of Links

[http://knightlab.northwestern.edu/2014/03/15/a-beginners-guide-to-collecting-twitter-data-and-a-bit-of-web-scraping/](http://knightlab.northwestern.edu/2014/03/15/a-beginners-guide-to-collecting-twitter-data-and-a-bit-of-web-scraping/)

[https://www.researchgate.net/post/What_is_best_way_to_collect_data_from_Twitter](https://www.researchgate.net/post/What_is_best_way_to_collect_data_from_Twitter)

[http://www.amazon.com/Mining-the-Social-Web-ebook/dp/B004LRPBBG/ref=sr_1_1?ie=UTF8&qid=1369814127&sr=8-1&keywords=mining+the+social+web](http://www.amazon.com/Mining-the-Social-Web-ebook/dp/B004LRPBBG/ref=sr_1_1?ie=UTF8&qid=1369814127&sr=8-1&keywords=mining+the+social+web)

[http://chorusanalytics.co.uk/](http://chorusanalytics.co.uk/)

[https://www.entrepreneur.com/article/242830](https://www.entrepreneur.com/article/242830)

[http://stackoverflow.com/questions/2714471/twitter-api-display-all-tweets-with-a-certain-hashtag](http://stackoverflow.com/questions/2714471/twitter-api-display-all-tweets-with-a-certain-hashtag)

[https://www.reddit.com/r/TheoryOfReddit/comments/2hg53b/q_how_can_i_collect_raw_data_from_reddit/](https://www.reddit.com/r/TheoryOfReddit/comments/2hg53b/q_how_can_i_collect_raw_data_from_reddit/)

[https://www.reddit.com/r/redditdev](https://www.reddit.com/r/redditdev)

[https://www.reddit.com/dev/api](https://www.reddit.com/dev/api) 

[http://stackoverflow.com/questions/34714237/searching-and-extracting-reddit-data-from-python-using-praw](http://stackoverflow.com/questions/34714237/searching-and-extracting-reddit-data-from-python-using-praw)

[http://pythonforengineers.com/build-a-reddit-bot-part-1/](http://pythonforengineers.com/build-a-reddit-bot-part-1/)