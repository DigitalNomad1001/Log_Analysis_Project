# Backend: Logs Analysis Project**
**Udacity: Full Stack Web Development Nanodegree**

**Licensing:** 
Anyone is free to use this code as you please. 

**Contributing:**
Anyone is free to contribute.

**Project:** 
This project consists of creating a reporting tool that outputs plain text reports from the data in the newsdata.sql database. This data resembles a real-world web application, with fields representing information that a web server would record, such as HTTP status codes and URL paths. The web server and the reporting tool both connect to the same database, allowing information to flow from the web server into the report. The database contains newspaper articles, as well as the web server log for the site. The log has a database row for each time a reader loaded a web page. This information will be used to answer the following questions:

1. What are the most popular three articles of all time?

2. Who are the most popular article authors of all time?

3. On which days did more than 1% of requests lead to errors?


**Install:**

* Python2.7
* PostgreSQL
* Vagrant
* Virtual Box

**Instructions:**

* Clone/download this repo into the /vagrant directory.
* Download the newsdata.sql data file from Udacity. 
* Move zip file contents into cloned /vagrant/LOGS_ANALYSIS_PROJECT directory
* Launch the VM: vagrant$ vagrant up
* SSH into the VM: On Mac/Linux vagrant$ vagrant ssh, Windows use Putty or similar SSH client
* In the VM navigate to the /vagrant/LOGS_ANALYSIS_PROJECT directory
* Load the data into the news database that is already in the VM: $psql -d news -f newsdata.sql
* Run the create view commands in the database views section.
* Run python report script: $ python logs_analysis.py


**Create View Commands:**

```
top_views:
CREATE VIEW top_views AS SELECT
articles.title, articles.id, articles.author,
(SELECT count(log.path)
  FROM log
  WHERE log.path::text LIKE '%'||articles.slug::text) AS views
FROM articles
ORDER BY views DESC;

request_stats:
CREATE VIEW request_stats AS SELECT
requests.day, requests.requests, errors.errors,
ROUND(errors * 100.0 / requests, 1) AS error_percent
FROM(
  (SELECT log.time::date AS day,
    count(*) AS requests
    FROM log
    GROUP BY 1
    ORDER BY requests DESC) requests
  JOIN
  (SELECT log.time::date AS day2,
    count(*) AS errors
    FROM log
    WHERE log.status::text != '200 OK'
    group by 1 order by errors DESC) errors
  ON day = day2);
```