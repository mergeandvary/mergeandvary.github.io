---
layout: post
title: "My Journey Into Scraping Twitter and/or Reddit Data"
tags: digitalhumanities dh twitter data scraping
author: Cooper Beilby
image: Melchior_de_Hondecoeter_-_Das_Vogelkonzert.jpg
imagesrc: Melchior de Hondecoeter - Das Vogelkonzert
---

As part of my digital humanities project, I am considering collecting and analysing tweets and/or reddit discussions on the #INeedMasculism #INeedMasculinism #INeedMasculismBecause hashtag(s). I am hoping to get some proof of concept so that I can employ this method or something similar in my research for my MRES next year. As this is a pretty new idea for me, I'm going to be documenting my journey here as a sort of resource journal. 


## What Data to Collect
Data I am likely to want in addition to the actual tweet content is username, time/date, retweets and mentions. I will probably also want to collect some data about each user - profile picture, age, gender and other details available on profiles. I am likely to want similar data for Reddit. Additionally, for Reddit I will also want to collect score, up/down votes and subreddit. 

This data will then go into a spreadsheet - ideally a multidimensional db, but that might have to wait for another time. In the spreadsheet I will have a page of each submission thread and then an individual page for each submission id that aggregates all the comments. I'm going to have to think of some way to filter out quoted comments to avoid double up. I will also need a way to track which comment each user is replying to - although, this might be problematic in itself as users might be replying to multiple comments. Nonetheless, it seems on Reddit that most users are fairly consistent in posting methods.

A quick search of reddit turns up very little for both #INeedMasculinism and #INeedMasculism. However, there are quite a few threads/posts discussing #INeedMasculismBecause. INeedMasculism will be a good tag to do testing with due to the limited results.


## Reddit Scraping
When I started looking into scraping Reddit data, I saw in [this post](https://www.reddit.com/r/TheoryOfReddit/comments/2hg53b/q_how_can_i_collect_raw_data_from_reddit/) a suggestion that the simplest way is to use PRAW. PRAW is a python module that interfaces with the [Reddit API](https://www.reddit.com/dev/api). The great thing about PRAW is that it is designed to stay within the guidelines of the Reddit API, so all I need to do is supply a useragent string in line with the guidelines and after that I don't have to worry about any issues with authentication or getting booted from the API for abuse.

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

Next week I will be looking at ways that I can insert this info into a database. Feeling on track! Hitting those deliverables!


## Putting it in a Database (i.e. Excel <*rolleyes*>)
The simpliest method I found for creating a spreadsheet for the data was to use the xlwt python module
{% highlight bash %}
sudo pip install xlwt
{% endhighlight %}

Using Excel spreadsheets is probably not the best method, but it is certainly the simplest for now. I'm not doing anything advanced (formulas formating links) - I just need something that I can dump the data into and a CSV file won't suffice as many of the comments will likely contain various characters such as commas and I'm not really interested in going to the effort of stripping them out, plus I'm not exactly sure of the methods I will be using to analyze the data yet, so just want to make sure I'm collecting the data in the rawest form possible. I can just use libre office to convert to a different format if necessary.


I used the basic examples here: [http://www.saltycrane.com/blog/2010/02/using-python-write-excel-openoffice-calc-spreadsheet-ubuntu-linux/](http://www.saltycrane.com/blog/2010/02/using-python-write-excel-openoffice-calc-spreadsheet-ubuntu-linux/)

{% highlight python %}
import xlwt

DATA = (("The Essential Calvin and Hobbes", 1988,),
        ("The Authoritative Calvin and Hobbes", 1990,),
        ("The Indispensable Calvin and Hobbes", 1992,),
        ("Attack of the Deranged Mutant Killer Monster Snow Goons", 1992,),
        ("The Days Are Just Packed", 1993,),
        ("Homicidal Psycho Jungle Cat", 1994,),
        ("There's Treasure Everywhere", 1996,),
        ("It's a Magical World", 1996,),)

wb = xlwt.Workbook()
ws = wb.add_sheet("My Sheet")
for i, row in enumerate(DATA):
    for j, col in enumerate(row):
        ws.write(i, j, col)
ws.col(0).width = 256 * max([len(row[0]) for row in DATA])
wb.save("myworkbook.xls")
{% endhighlight %}

Fairly straightforward: each row of data goes in a tuple in the order of the columns, then each row tuple goes inside a larger tuple. Then you just iterate over the tuple with a for loop and write the data with ws.write into the coordinates. Enumerate just lets you have an additional variable that holds the position within the for loop as an integer - i is holding the y coordinate (rows) and j is the x coordinate (columns).

The ws.col(0).width is an attempt to autosize the width of columns, but I'm not really interested in doing that so I scrapped it as it was causing problems when there were empty datasets. 

So what I did was just use for loops and add the submissions to a tuple.
{% highlight python %}
for searchterm in SEARCH_TERMS:
    print 'Searching for term: ' + str(searchterm)
    submissions = r.search(searchterm, subreddit=None, sort=None, syntax=None, period=None)
    for submission in submissions:
        # Make sure the submission isn't a bot copying another submission
        title = str(submission.title)
        if title.startswith('[COPY]'):
            print 'Submission COPY - Skipping...'
        else:
            submission_db = ()

            # Make a separate db page for submission details only
            try:
                submission_db = submission_db + (str(submission.id),)
                submission_db = submission_db + (str(submission.title),)
                submission_db = submission_db + (str(submission.author),)
                submission_db = submission_db + (str(submission.url),)
                submission_db = submission_db + (str(datetime.datetime.fromtimestamp(int(submission.created_utc)).strftime('%Y-%m-%d %H:%M:%S')),)
                submission_db = submission_db + (str(submission.subreddit),)
                submission_db = submission_db + (str(submission.subreddit_id),)
                submission_db = submission_db + (str(submission.score),)
                submission_db = submission_db + (str(submission.selftext),)
                submissions_db = submissions_db + (submission_db,)
                print 'Added Submission ID: ' + str(submission.id)
            except:
                print 'ERROR: Adding info to submission_db'

if 'submissions' in str(sys.argv) or len(sys.argv) == 1:
    print 'WRITING SUBMISSIONS'
    submissions_ws = wb.add_sheet("Submissions")
    for i, row in enumerate(submissions_db):
        for j, col in enumerate(row):
            submissions_ws.write(i, j, col)

wb.save("myworkbook.xls")
{% endhighlight %}

I added some categories at the top of the spreadsheet in the first row before I run the search for submissions.
{% highlight python %}
SUBMISSIONS_DB_CAT      = ('ID', 'TITLE', 'AUTHOR', 'URL', 'TIMESTAMP', 'SUBREDDIT', 'SUBREDDIT ID', 'SCORE', 'COMMENT')
submissions_db = ()
submissions_db = submissions_db + (SUBMISSIONS_DB_CAT,)
{% endhighlight %}


## Debugging
Initially everything was working fine. I had expanded my code out to also collect comments for each submission and place them in individual pages of the spreadsheet according to submission ID. The code also aggregated all the comments for all submissions into a separate page. I also noticed that the submission also acted as the first comment, so I needed to add those details in the first row of each comments page. Now that everything seemed to be working fine, I decided to expand my code out to be able to collect multiple search terms. This was when I started running into ASCII UnicodeErrors. Googling the issue I found I needed to set encoding/decoding to utf-8. However, all the solutions I found I would need to do this for every individual string and I really couldn't be bothered. There must be a quicker way! After some lengthy googling I stumbled upon this solution in a stackoverflow comment thread:

{% highlight python %}
import sys
reload(sys)
sys.setdefaultencoding('utf-8')

wb = xlwt.Workbook(encoding='utf-8')
{% endhighlight %}

It worked, but xlwt started throwing out errors. I found I also needed to set the encoding for the Workbook and all was well again!

After scraping some data, I noticed a few duplicate submissions in the POLITICS subreddit. It seems that a few bots were making copies of threads and dumping them into the subreddit. Copy threads are marked with [COPY] in the title, so I wrote a simple if statement routine to check submissions for this string in the title and skip them. 

As I was going through the data, I started noticing something strange: all the up vote scores were equal to the total score. Moreover, many of the up votes were negative which simply cannot be possible. I then noticed the downvotes were all zero. After some searching in the Redditdev subreddit, I discovered that Reddit had recently made a (contentious) decision to remove all access to up/down vote scores and also upvote ratio data. This had been introduced to stop bots from attempting to artificially inflate/deflate some scores. Without access to this through the API, bots are unable to check whether or not they are having an effect on scores. Disappointing for me as a zero score with 100 votes is very different to a zero score with no votes in terms of impact on other users. Still, if the scores are being altered by bots, then they aren't really going to be a very good heuristic anyway!

Another thing I noticed from putting the data into a spreadsheet was that when I compared back to the original threads I noticed that I seemed to be missing a number of comments. I confirmed this by adding a comment counter and comparing the number of comments between threads and what I was collecting. I then realised I was only getting the first level of comments, not the comments that replied to other comments. Each comment object also has a replies object! I solved this by writing a function to grab the comments data and then check for a replies object and recursively run that function on the replies object - so essentially the script would walk down the comments tree. The other option was to use a flatten comments option in PRAW. However, I wanted to make sure I retained the order of comments. The other issue I came up against was that some comment replies are hidden behind a "More Comments" object. Currently I'm just checking for this and skipping them as I haven't come up against many, but this will ultimately need a solution. From what I've read, there are some limitations on how walking down the more comments objects - we will see!

## PostGreSQL
Now I'm on to adding to a real database rather than a spreadsheet. I rewrote my code a little bit to use dictionaries rather than tuples

{% highlight python %}
def addCommentRegression(submission,comment):
    global submission_db_dict
    global comment_db_dict
    # Make sure it is a comment rather than morecomments object
    if isinstance(comment, praw.objects.Comment):
        try:
            comment_dict = {}
            comment_dict['SubmissionID']    = submission.id
            comment_dict['ParentID']        = comment.parent_id
            comment_dict['Author']          = comment.author
            comment_dict['Created']         = datetime.datetime.fromtimestamp(int(comment.created_utc)).strftime('%Y-%m-%d %H:%M:%S')
            comment_dict['Score']           = comment.score
            comment_dict['Removal_Reason']  = comment.removal_reason
            comment_dict['Report_Reasons']  = comment.report_reasons
            comment_dict['Edited']          = comment.edited
            comment_dict['Controversial']   = comment.controversiality
            comment_dict['Body']            = comment.body

            addAuthor(comment.author)
            comment_db_dict[comment.id] = comment_dict
            print 'Added comment ID: ' + str(comment.id)
        except Exception as e: print(e)

        # Regression for comments
        if comment.replies:
            for reply in comment.replies:
                addCommentRegression(submission,reply)
    else:
        print 'More Comments OBJECT'
{% endhighlight %}

I'm using the tutorial here: [http://zetcode.com/db/postgresqlpythontutorial/](http://zetcode.com/db/postgresqlpythontutorial/)

Essentially I am collecting a submissions table, a comments table and an authors table. I will also need to add a subreddit table, after I look into collecting data on subreddits

{% highlight python %}
con = None
try:   
    con = psycopg2.connect(database=POSTGRES_DB, user=POSTGRES_USER) 
    cur = con.cursor()

    # ADD SUBMISSIONS   
    table = ()
    for key, value in submission_db_dict.iteritems():
        entry = (str(key), 
                 str(value['Author']), 
                 str(value['Created']), 
                 int(value['Score']), 
                 str(value['Selftext']), 
                 str(value['SubredditID']), 
                 str(value['Title'])
                 )
        table = table + (entry,)
    cur.execute("DROP TABLE IF EXISTS Submissions")
    cur.execute("CREATE TABLE Submissions(SubmissionID TEXT PRIMARY KEY, Author TEXT, Created TEXT, Score INT, Selftext TEXT, SubredditID TEXT, Title TEXT)")
    query = "INSERT INTO Submissions (SubmissionID, Author, Created, Score, Selftext, SubredditID, Title) VALUES (%s, %s, %s, %s, %s, %s, %s)"
    cur.executemany(query, table)
    con.commit()

except psycopg2.DatabaseError, e:
    if con:
        con.rollback()
    print 'Error %s' % e    
    sys.exit(1)
    
finally:
    if con:
        con.close()
{% endhighlight %}

It seems to be all working correctly as far as I can tell.

## Debugging Foriegn Keys and Author 404s
I encountered a database error when referencing parent_ids back to the comments table. I quickly realised that some parent_ids referenced the submissions rather than comments. Basically they were split over two tables. The way forward (after discussing with Brian - one of my DH teachers) was to use a post supertype table that holds all common data and then have both comments and submissions tables primary key also be a foreign key that references this posts table. After doing this I quickly discovered a mismatch between some additional information added to parent id keys so they wouldn't properly match - I solved this by adding an addition variable passed through in my addComments function that would pass through the previous comment/submission id from the last position in the tree.

I also had a problem with some authors throwing out HTTP errors - through debugging I discovered which authors were throwing these errors out. I then looked these authors api urls up and noticed that these returned 404 errors. Through some deft google searching I found discussion that noted that these occur when users are shadow banned. I had to add some special error handlign for these issues.

## An Aside on Twitter Scraping for Future Reference
At first I had been struggling to find some information on how I would go about collecting data. I found some information about topsy.com, a service run by Apple that collected tweets and allowed end users to search that data in a variety of ways. Unfortunately, the service has now been shutdown. Nonetheless, pressing on further I was able to find some information. I've been collecting the links I've found in the section at the bottom of this post.

According to the article on [Knightlab](http://knightlab.northwestern.edu/2014/03/15/a-beginners-guide-to-collecting-twitter-data-and-a-bit-of-web-scraping/), the twitter API imposes some limits on how many calls can be made within a certain window. The Knightlab article suggests a few good tips about setting up a cron job to fetch data every 15mins and ensuring a key cycle between keys to stay within limits. I might need to set up a NeCTAR virtual machine with a cron job to do this for me and not have to keep a local machine running. From this [StackOverflow](http://stackoverflow.com/questions/2714471/twitter-api-display-all-tweets-with-a-certain-hashtag) forum thread, it seems like the twitter API might not be too difficult to use as it appears to just involve JSON-RPC requests. It might actually be fairly easy to just write a quick python script to collect the data and place it into a database. This would certainly simplify running on a virtual NeCTAR machine. However, if something already exists I would rather use that.


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