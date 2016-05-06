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

Data I am likely to want in addition to the actual tweet content is username, time/date, retweets and pings (I'm not sure what twitter calls it when you tag someone?). I will probably also want to collect some data about each user - profile picture, age, gender and other details available on profiles. I am likely to want similar data for Reddit. A quick search of reddit turns up very little for both #INeedMasculinism and #INeedMasculism. However, there are quite a few threads/posts discussing #INeedMasculismBecause

## Starting Out With Twitter

According to the article on [Knightlab](http://knightlab.northwestern.edu/2014/03/15/a-beginners-guide-to-collecting-twitter-data-and-a-bit-of-web-scraping/), the twitter API imposes some limits on how many calls can be made within a certain window. The Knightlab article suggests a few good tips about setting up a cron job to fetch data every 15mins and ensuring a key cycle between keys to stay within limits. I might need to set up a NeCTAR virtual machine with a cron job to do this for me and not have to keep a local machine running. From this [StackOverflow](http://stackoverflow.com/questions/2714471/twitter-api-display-all-tweets-with-a-certain-hashtag) forum thread, it seems like the twitter API might not be too difficult to use as it appears to just involve JSON-RPC requests. It might actually be fairly easy to just write a quick python script to collect the data and place it into a database. This would certainly simplify running on a virtual NeCTAR machine. However, if something already exists I would rather use that.

## Scraping Reddit Data

When I started looking into scraping Reddit data, I saw in [this post](https://www.reddit.com/r/TheoryOfReddit/comments/2hg53b/q_how_can_i_collect_raw_data_from_reddit/) a suggestion that the simplest way is to use PRAW. PRAW is a python module that interfaces with the [Reddit API](https://www.reddit.com/dev/api). 

About PRAW: [http://praw.readthedocs.io/en/stable/](http://praw.readthedocs.io/en/stable/)

It doesn't seem too difficult. I think getting my hands dirty combined with my *very* limited knowledge of python I might actually be able to get somewhere with the Reddit API and PRAW.

This was my first test run - getting the top 5 hot threads on the opensource subreddit.

{% highlight python %}
import praw

r = praw.Reddit(user_agent='praw testing')
submissions = r.get_subreddit('opensource').get_hot(limit=5)
for submission in submissions:
    print str(submission)
{% endhighlight %}

And the output:
{% highlight php %}
49 :: Memes, etc.
32 :: 59 percent of tech hiring managers say they'll increase their open sour...
8 :: Why is Open Source built with closed tools?
9 :: Meet Kermit: A friendly web scraper written in coffeescript. Fun to use ...
14 :: Open source near ubiquitous in IoT, report finds
{% endhighlight %}

Success!


## Searching for Comments on a Topic
{% highlight python %}
submissions = r.search('INeedMasculism', subreddit=None, sort=None, syntax=None, period=None)
{% endhighlight %}

This gives a list of all threads using the search term. However, now I need to get the submission id from each in order to parse the comments
We get that with
`submission.id`

For example:
{% highlight python %}
import praw
r = praw.Reddit(user_agent='praw testing')
submissions = r.search('INeedMasculism', subreddit=None, sort=None, syntax=None, period=None)
for submission in submissions:
    print str(submission.id)
{% endhighlight %}  

Actually, on further investigation I find that I can just get it with
`submission.comments`

Now I have the comments objects.
If I iterate along the comments objects with a for loop I can get the various variables available for each comment with something along the lines of `print(vars(comment))`

I'm running it on INeedMasculism rather than any of the other variations as this only shows up one thread. For final run throughs I will run on all hashtag / terms by placing them in a tuple and iterating through them with a for loop

This is what I have so far:
{% highlight python %}
import praw

r = praw.Reddit(user_agent='praw testing')
submissions = r.search('INeedMasculism', subreddit=None, sort=None, syntax=None, period=None)
for submission in submissions:
    print 'Thread:    ' + str(submission.id) + str(submission)
    for comment in submission.comments:
        print ''
        print 'Author:    ' + str(comment.author)
        print 'Likes:     ' + str(comment.likes)
        print 'Score:     ' + str(comment.score) + ' (Ups: ' + str(comment.ups) + ' / Downs: ' + str(comment.downs) + ')'
        print 'Comment:   ' + str(comment.body)
        # print(vars(comment))
{% endhighlight %}

Basically what I'm doing line by line.

1. I import the PRAW python module
2. I set my user agent string to something descriptive (when I flesh out the script this will change as Reddit asks nicely that we be descriptive with what we are doing with the API)
3. Search for submissions that use my search term
4. Iterate a for loop over each submission
5. Print the name of the thread and its id to the terminal
6. Iterate a for loop over each comment in that particular submission
7. Print to terminal the author, how many likes they have, the score includign up and down votes, and the coment body
8. Finally I have commented out the way that I can get the various vars available so that I can search for other info that I might want to include

This yields the following results:
{% highlight php %}
Thread:    26plsq6 :: Anyone remember #INeedMasculism?

Author:    cjt09
Likes:     None
Score:     7 (Ups: 7 / Downs: 0)
Comment:   Something that a lot of people don't realize is that there *is* currently a lack of spaces in societies where people can discuss men's issues. What's worse, is when people do try to create these spaces, they're stigmatized and--dare I say it--oppressed. People should be able to express themselves, and [NeuroticIntrovert has a great post that goes into much greater detail here](http://np.reddit.com/r/changemyview/comments/1jt1u5/cmv_i_think_that_mens_rights_issues_are_the/cbi2m7a), where he steps through how this became such an issue today.

We should really be promoting an environment that allows *everyone* to express their own views and perspectives. #YesAllWomen is a great movement that carries enough social capital that a lot of women can express the frustrations and injustices that they experience every day. #YesAllMen is important for the same reason--we can't work together on a solution unless we know where each other is coming from.

Author:    snwbrdbum14
Likes:     None
Score:     7 (Ups: 7 / Downs: 0)
Comment:   You can't put something in perspective for someone who lacks perspective altogether
{% endhighlight %}

Next week I will be looking at ways that I can insert this info into a database. Feeling on track!

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

[http://praw.readthedocs.io/en/stable/](http://praw.readthedocs.io/en/stable/)