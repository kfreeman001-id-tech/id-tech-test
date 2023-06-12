# Mini-Homework #2

## Due date: Friday, Dec. 2, 2021

## This mini homework has 30 points

## Objectives:

Execute queries on [Vertica](https://www.vertica.com/)!

## Assignment tools

Docker on EC2 or your local machine with Vertica.  See the [section](https://courses.cs.washington.edu/courses/csed516/21au/sections/section7.pdf) for details on installation.

# Assignment Dataset

## Lobster Dataset

The data we will use for Vertica is a toy dataset for website http://lobste.rs, which is a Hacker News clone.  We'll create relations for this dataset, ingest data, and then run queries.

### Schema (included in the python script below)

Create relations for this dataset as follows (included in the python script below, no need to run it via VSQL):

```sql
CREATE SCHEMA lobsters;

CREATE TABLE lobsters.tags ( id integer NOT NULL, tag varchar(64));

CREATE TABLE lobsters.taggings (id integer NOT NULL, story_id integer NOT NULL, tag_id integer NOT NULL);

CREATE TABLE lobsters.hiddens (id integer NOT NULL, user_id integer NOT NULL, story_id integer NOT NULL);

CREATE TABLE lobsters.stories (
  id integer NOT NULL, 
  created_at TIMESTAMP, 
  description varchar(4095), 
  hotness float, 
  markeddown_description varchar(4095), 
  short_id varchar(255), 
  title varchar(1023), 
  upvotes integer, 
  downvotes integer, 
  url varchar(255), 
  user_id integer);
```

### Homework data

The data for these relations is located in `starter-code/mini2-vertica`

* hidden.csv
* tags.csv
* taggings.csv
* stories.csv

### Creating Schema/Tables and Ingesting data into the database

Finally, use the following Python script to load data into your Vertica database. Remember to put the data files (.csv files) in the same directory as your Python script before you run the script.

```py
import vertica_python as vp

args = {
    'host': '127.0.0.1',
    'port': 5433,
    'user': 'dbadmin',
    'password': '',
    'database': 'docker',
    # 10 minutes timeout on queries
    'read_timeout': 600,
    # default throw error on invalid UTF-8 results
    'unicode_error': 'strict',
    # SSL is disabled by default
    'ssl': False,
    # connection timeout is not enabled by default
    'connection_timeout': 5
}

conn = vp.connect(**args)
cur = conn.cursor()
cur.execute("""
CREATE SCHEMA lobsters;

CREATE TABLE lobsters.tags ( id integer NOT NULL, tag varchar(64));

CREATE TABLE lobsters.taggings (id integer NOT NULL, story_id integer NOT NULL, tag_id integer NOT NULL);

CREATE TABLE lobsters.hiddens (id integer NOT NULL, user_id integer NOT NULL, story_id integer NOT NULL);

CREATE TABLE lobsters.stories (
  id integer NOT NULL, 
  created_at TIMESTAMP, 
  description varchar(4095), 
  hotness float, 
  markeddown_description varchar(4095), 
  short_id varchar(255), 
  title varchar(1023), 
  upvotes integer, 
  downvotes integer, 
  url varchar(255), 
  user_id integer);
""")
cur.fetchone()
cur.fetchall()

with open("taggings.csv", "rb") as f:
    cur.copy("COPY lobsters.taggings from stdin DELIMITER ','", f)
with open("hidden.csv", "rb") as f:
    cur.copy("COPY lobsters.hiddens from stdin DELIMITER ','", f)
with open("stories.csv", "rb") as f:
    cur.copy("COPY lobsters.stories from stdin DELIMITER ','", f)
```

### Sample Queries

To make sure things are working, try executing the following queries from your VSQL console:
```sql
select count(*) from lobsters.stories where user_id = 1;

select t.tag_id, count(*) from lobsters.stories as s inner join lobsters.taggings as t on s.hotness > 0 and t.story_id = s.id group by t.tag_id order by count(*) desc;

select s.id, s.title, s.upvotes, s.downvotes from lobsters.stories as s inner join lobsters.hiddens as h on created_at > '2017-01-23 00:00:00' and h.user_id <> 1 and h.story_id = s.id inner join lobsters.taggings as t on t.story_id = s.id;

select id, title from lobsters.stories 
where id in (( select story_id from lobsters.taggings where tag_id not in (2, 6, 7) ) union (select story_id from lobsters.hiddens where user_id not in (3, 4, 5) )) and hotness > 0 and created_at > '2017-01-23 00:00:00';
```

# Assignment Details

In this Assignment you will be required to
access a Vertica DBMS either via EC2 or locally.  In both cases we recommend installing Vertica in a Docker container as described in the section notes.

In this homework you will write a few queries on the Lobsters dataset.
For query A and B below, save and submit the SQL and the query result.
You may restrict the columns you list for these two queries. 

For the last bullet, run the queries a couple times and
return mean runtime for each of the queries as well as the discussion of the resulting query times.  For this query there is no need
to submit the results from the queries.

- Describe in one sentence the purpose of each of the queries in the previous section.  Which (if any) of these queries are especially well-suited for a column store? (5 points)(Store in `mini-2-discussion.txt`)
- Query A: List the top-10 stories ranked by 'hotness' (10 points) (Store query in `mini-2-A-query.vsql` and results in `mini-2-A-results.txt`)
- Query B: List top-10 stories with the highest number of up-votes and lowest number of down-votes (sort by [# of upvotes - # of downvotes]). How does this list compare with the results from the previous query? (10 points) (Store query in `mini-2-B-query.vsql`, results in `mini-2-B-results.txt`, and append discussion in `mini-2-discussion.txt`)
- For the `stories` relation run the following queries, note the time to run these queries and discuss your intuition and observations about their runtimes (5 points) (Store runtimes in `mini-2-stories.txt` and append discussion in `mini-2-discussion.txt`):
  - `select * from stories;`
  - `select id from stories;`
  - `select id, title from stories;`

# What to submit

Submit `mini-2-discussion.txt`, `mini-2-A-query.vsql`, `mini-2-A-results.txt`, `mini-2-B-query.vsql`, `mini-2-B-results.txt`, and `mini-2-stories.txt`
**Please put them in the `submission/mini2-vertica` folder. Anything not in `submission/mini2-vertica` will not be graded.**

## How to submit the assignment

In your GitLab
repository, you should see a directory called `hw4/submission/mini2-vertica`. Put your
report in that directory. Remember to `git add, git commit, and git
push`. You can add your report early and keep updating it and
pushing
it as you do more work. We will collect the final version after the
deadline passes. If you need extra time on an assignment, let us
know. This is a graduate course, so we are reasonably flexible with
deadlines but please do not overuse this flexibility. Use extra time
only when you truly need it.
