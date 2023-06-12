# Mini-Homework #1

## Due date: Friday, Dec. 2, 2021

## This mini homework has 30 points

## Objectives:

Execute queries on [Materialize](https://materialize.com/docs/lts/get-started/)!


# Assignment Dataset

## Wikipedia Stream

We will use real-time edit streams from wikipedia, as we did during the section.

# What to submit

Submit `wikirecent.txt`, `wikirevision.txt`, `mini1-Q3a.sql`, `mini1-Q3b.sql`, `mini1-Q4a.sql`, `mini1-Q4b.sql`. Place them in the `submission/mini1-materialize` folder.

## How to submit the assignment

In your GitLab repository, you should see a directory called
`hw4/submission/mini1-materialize`. Put your code in that
directory. Remember to `git add, git commit, and git push`. You can
add your work early and keep updating it and pushing it as you do more
work. We will collect the final version only after the deadline
passes. 

## Assignment Details

Go to the [demo link](https://materialize.com/docs/lts/get-started/) for a web interface for materialize. Follow the steps in the session demo to install materialize and connect to [PubNub](https://www.pubnub.com/tutorials/real-time-data-streaming-nodejs-python/).

1) (2 pt) Similar to the class demo, run the commands on the IDE and save the results to a file. Submit this file for the URL https://stream.wikimedia.org/v2/stream/recentchange performed in class with the file named `wikirecent.txt`. 

2) (3 pts) Replace the URL with https://stream.wikimedia.org/v2/stream/revision-create and save the response in a file named `wikirevision.txt`. Submit the file `wikirevision.txt`.

3) (5 pts) Follow the instructions to create a source from wikirecent. 

    a) (2 pts) Do the same for wikirevision. Add the create source SQL code in file `mini1-Q3a.sql`. 

    b) (3 pts) In order to connect the two data streams, we need to find a common identifier (request-id). Change the query for creating the recentchanges view to include this field. Create another materialized view for wikirevision. Make sure to read the documentation for the [jsonb function](https://materialize.com/docs/sql/functions/#json). Save your view commands in `mini1-Q3b.sql`.

4) (20 pts) Write queries to return the top 10 users who make the most revisions for the “enwiki” site that are not bots. [Hint: look at the wikirecent stream to see how to tell if a user is a bot, and which wiki site (enwiki, itwiki etc.) the user edits; the wikirevision stream contains only revisions.] Write the query/queries in two ways: 

    a) (10 pts) Compute the result from scratch every time (save this in `mini1-Q4a.sql`)

    b) (10 pts) Maintain the result incrementally with materialized views (save this in `mini1-Q4b.sql`). [Hint: the former requires just a regular SQL query, whereas the latter requires the `CREATE MATERIALIZED VIEW` command.]

