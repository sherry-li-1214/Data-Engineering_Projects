      
  <div id="readme" class="Box-body readme blob js-code-block-container px-5">
    <article class="markdown-body entry-content" itemprop="text"><h1><a id="user-content-project-3-create-a-data-warehouse-with-aws-redshift" class="anchor" aria-hidden="true" href="#project-3-create-a-data-warehouse-with-aws-redshift"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>Project 3: Create a Data Warehouse with AWS Redshift</h1>
<h2><a id="user-content-summary" class="anchor" aria-hidden="true" href="#summary"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>Summary</h2>
<ul>
<li><a href="#Introduction">Introduction</a></li>
<li><a href="#Data-Warehouse-Schema-Definition">Data Warehouse Schema Definition</a></li>
<li><a href="#ETL-process">ETL process</a></li>
<li><a href="#Project-structure">Project structure</a></li>
</ul>
<hr>
<h3><a id="user-content-introduction" class="anchor" aria-hidden="true" href="#introduction"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>Introduction</h3>
<p>A music streaming startup, Sparkify, has grown their user base and song database and want
to move their processes and data onto the cloud. Their data resides in S3, in a directory
of JSON logs on user activity on the app, as well as a directory with JSON metadata
on the songs in their app.</p>
<p>As their data engineer, you are tasked with building an ETL pipeline that extracts
their data from S3, stages them in Redshift, and transforms data into a set of
dimensional tables for their analytics team to continue finding insights in what songs
their users are listening to. You'll be able to test your database and ETL pipeline
by running queries given to you by the analytics team from Sparkify and compare your
results with their expected results</p>
<p>In this project we are going to use two Amazon Web Services resources:</p>
<ul>
<li><a href="https://aws.amazon.com/en/s3/" rel="nofollow">S3</a></li>
<li><a href="https://aws.amazon.com/en/redshift/" rel="nofollow">AWS Redshift</a></li>
</ul>
<p>The data sources to ingest into data warehouse are provided by two public S3 buckets:</p>
<ol>
<li>Songs bucket (s3://udacity-dend/song_data), contains info about songs and artists.
All files are in the same directory.</li>
<li>Event bucket (s3://udacity-dend/log_data), contains info about actions done by users, what song are listening, ...
We have differents directories so we need a descriptor file (also a JSON) in order to extract
data from the folders by path. We used a descriptor file (s3://udacity-dend/log_json_path.json) because we
don't have a common prefix on folders</li>
</ol>
<p>The objects contained in both buckets are JSON files. The song bucket has all
the files under the same directory but <br> the event ones don't,
so we need a descriptor file (also a JSON) in order to extract
data from the folders by path. We used a descriptor file because we don't
have a common prefix on folders</p>
<p>We need to ingest this data into AWS Redshift using COPY command. This command get JSON files
from buckets and copy them into staging tables inside AWS Redshift.</p>
<p><b>Log Dataset structure:</b>
<a target="_blank" rel="noopener noreferrer" href="/fgonzalezmir/dataengineer-nanodegree/blob/master/02-cloud-datawarehouses/project03-datawarehouse/images/log_dataset.jpg"><img src="/fgonzalezmir/dataengineer-nanodegree/raw/master/02-cloud-datawarehouses/project03-datawarehouse/images/log_dataset.jpg" alt="Log Dataset" style="max-width:100%;"></a></p>
<p><b>Song dataset structure:</b></p>
<pre><code>{"num_songs": 1, "artist_id": "ARJIE2Y1187B994AB7", "artist_latitude": null, "artist_longitude": null
, "artist_location": "", "artist_name": "Line Renaud", "song_id": "SOUPIRU12A6D4FA1E1", 
"title": "Der Kleine Dompfaff", "duration": 152.92036, "year": 0}
</code></pre>
<hr>
<h3><a id="user-content-data-warehouse-schema-definition" class="anchor" aria-hidden="true" href="#data-warehouse-schema-definition"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>Data Warehouse Schema Definition</h3>
<p>This is the schema of the database</p>
<h4><a id="user-content-staging-tables" class="anchor" aria-hidden="true" href="#staging-tables"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>Staging tables</h4>
<h5><a id="user-content-table-staging_songs" class="anchor" aria-hidden="true" href="#table-staging_songs"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>TABLE staging_songs</h5>
<table>
<thead>
<tr>
<th>COLUMN</th>
<th>TYPE</th>
<th>FEATURES</th>
</tr>
</thead>
<tbody>
<tr>
<td>num_songs</td>
<td>int</td>
<td></td>
</tr>
<tr>
<td>artist_id</td>
<td>varchar</td>
<td></td>
</tr>
<tr>
<td>artist_latitude</td>
<td>decimal</td>
<td></td>
</tr>
<tr>
<td>artist_longitude</td>
<td>decimal</td>
<td></td>
</tr>
<tr>
<td>artist_location</td>
<td>varchar</td>
<td></td>
</tr>
<tr>
<td>artist_name</td>
<td>varchar</td>
<td></td>
</tr>
<tr>
<td>song_id</td>
<td>varchar</td>
<td></td>
</tr>
<tr>
<td>title</td>
<td>varchar</td>
<td></td>
</tr>
<tr>
<td>duration</td>
<td>decimal</td>
<td></td>
</tr>
<tr>
<td>year</td>
<td>int</td>
<td></td>
</tr>
</tbody>
</table>
<h5><a id="user-content-table-staging_events" class="anchor" aria-hidden="true" href="#table-staging_events"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>TABLE staging_events</h5>
<table>
<thead>
<tr>
<th>COLUMN</th>
<th>TYPE</th>
<th>FEATURES</th>
</tr>
</thead>
<tbody>
<tr>
<td>artist</td>
<td>varchar</td>
<td></td>
</tr>
<tr>
<td>auth</td>
<td>varchar</td>
<td></td>
</tr>
<tr>
<td>firstName</td>
<td>varchar</td>
<td></td>
</tr>
<tr>
<td>gender</td>
<td>varchar</td>
<td></td>
</tr>
<tr>
<td>itemInSession</td>
<td>int</td>
<td></td>
</tr>
<tr>
<td>lastName</td>
<td>varchar</td>
<td></td>
</tr>
<tr>
<td>length</td>
<td>decimal</td>
<td></td>
</tr>
<tr>
<td>level</td>
<td>varchar</td>
<td></td>
</tr>
<tr>
<td>location</td>
<td>varchar</td>
<td></td>
</tr>
<tr>
<td>method</td>
<td>varchar</td>
<td></td>
</tr>
<tr>
<td>page</td>
<td>varchar</td>
<td></td>
</tr>
<tr>
<td>registration</td>
<td>varchar</td>
<td></td>
</tr>
<tr>
<td>sessionId</td>
<td>int</td>
<td></td>
</tr>
<tr>
<td>song</td>
<td>varchar</td>
<td></td>
</tr>
<tr>
<td>status</td>
<td>int</td>
<td></td>
</tr>
<tr>
<td>ts</td>
<td>timestamp</td>
<td></td>
</tr>
<tr>
<td>userAgent</td>
<td>varchar</td>
<td></td>
</tr>
<tr>
<td>userId</td>
<td>int</td>
<td></td>
</tr>
</tbody>
</table>
<h4><a id="user-content-dimension-tables" class="anchor" aria-hidden="true" href="#dimension-tables"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>Dimension tables</h4>
<h5><a id="user-content-table-users" class="anchor" aria-hidden="true" href="#table-users"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>TABLE users</h5>
<table>
<thead>
<tr>
<th>COLUMN</th>
<th>TYPE</th>
<th>FEATURES</th>
</tr>
</thead>
<tbody>
<tr>
<td>user_id</td>
<td>int</td>
<td>distkey, PRIMARY KEY</td>
</tr>
<tr>
<td>first_name</td>
<td>varchar</td>
<td></td>
</tr>
<tr>
<td>last_name</td>
<td>varchar</td>
<td></td>
</tr>
<tr>
<td>gender</td>
<td>varchar</td>
<td></td>
</tr>
<tr>
<td>level</td>
<td>varchar</td>
<td></td>
</tr>
</tbody>
</table>
<h5><a id="user-content-table-songs" class="anchor" aria-hidden="true" href="#table-songs"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>TABLE songs</h5>
<table>
<thead>
<tr>
<th>COLUMN</th>
<th>TYPE</th>
<th>FEATURES</th>
</tr>
</thead>
<tbody>
<tr>
<td>song_id</td>
<td>varchar</td>
<td>sortkey, PRIMARY KEY</td>
</tr>
<tr>
<td>title</td>
<td>varchar</td>
<td>NOT NULL</td>
</tr>
<tr>
<td>artist_id</td>
<td>varchar</td>
<td>NOT NULL</td>
</tr>
<tr>
<td>duration</td>
<td>decimal</td>
<td></td>
</tr>
</tbody>
</table>
<h5><a id="user-content-table-artists" class="anchor" aria-hidden="true" href="#table-artists"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>TABLE artists</h5>
<table>
<thead>
<tr>
<th>COLUMN</th>
<th>TYPE</th>
<th>FEATURES</th>
</tr>
</thead>
<tbody>
<tr>
<td>artist_id</td>
<td>varchar</td>
<td>sortkey, PRIMARY KEY</td>
</tr>
<tr>
<td>name</td>
<td>varchar</td>
<td>NOT NULL</td>
</tr>
<tr>
<td>location</td>
<td>varchar</td>
<td></td>
</tr>
<tr>
<td>latitude</td>
<td>decimal</td>
<td></td>
</tr>
<tr>
<td>logitude</td>
<td>decimal</td>
<td></td>
</tr>
</tbody>
</table>
<h5><a id="user-content-table-time" class="anchor" aria-hidden="true" href="#table-time"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>TABLE time</h5>
<table>
<thead>
<tr>
<th>COLUMN</th>
<th>TYPE</th>
<th>FEATURES</th>
</tr>
</thead>
<tbody>
<tr>
<td>start_time</td>
<td>timestamp</td>
<td>sortkey, PRIMARY KEY</td>
</tr>
<tr>
<td>hour</td>
<td>int</td>
<td></td>
</tr>
<tr>
<td>day</td>
<td>int</td>
<td></td>
</tr>
<tr>
<td>week</td>
<td>int</td>
<td></td>
</tr>
<tr>
<td>month</td>
<td>int</td>
<td></td>
</tr>
<tr>
<td>year</td>
<td>int</td>
<td></td>
</tr>
<tr>
<td>weekday</td>
<td>int</td>
<td></td>
</tr>
</tbody>
</table>
<h4><a id="user-content-fact-table" class="anchor" aria-hidden="true" href="#fact-table"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>Fact table</h4>
<h5><a id="user-content-table-songplays" class="anchor" aria-hidden="true" href="#table-songplays"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>TABLE songplays</h5>
<table>
<thead>
<tr>
<th>COLUMN</th>
<th>TYPE</th>
<th>FEATURES</th>
</tr>
</thead>
<tbody>
<tr>
<td>songplay_id</td>
<td>int</td>
<td>IDENTITY (0,1), PRIMARY KEY</td>
</tr>
<tr>
<td>start_time</td>
<td>timestamp</td>
<td>REFERENCES  time(start_time)    sortkey</td>
</tr>
<tr>
<td>user_id</td>
<td>int</td>
<td>REFERENCES  users(user_id) distkey</td>
</tr>
<tr>
<td>level</td>
<td>varchar</td>
<td></td>
</tr>
<tr>
<td>song_id</td>
<td>varchar</td>
<td>REFERENCES  songs(song_id)</td>
</tr>
<tr>
<td>artist_id</td>
<td>varchar</td>
<td>REFERENCES  artists(artist_id)</td>
</tr>
<tr>
<td>session_id</td>
<td>int</td>
<td>NOT NULL</td>
</tr>
<tr>
<td>location</td>
<td>varchar</td>
<td></td>
</tr>
<tr>
<td>user_agent</td>
<td>varchar</td>
<td></td>
</tr>
</tbody>
</table>
<hr>
<h3><a id="user-content-etl-process" class="anchor" aria-hidden="true" href="#etl-process"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>ETL process</h3>
<p>All the transformations logic (ETL) is done in SQL inside Redshift.</p>
<p>There are 2 main steps:</p>
<ol>
<li>Ingest data from s3 public buckets into staging tables:</li>
<li>Insert record into a star schema from staging tables</li>
</ol>
<h4><a id="user-content-insert-data-into-staging-tables" class="anchor" aria-hidden="true" href="#insert-data-into-staging-tables"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>Insert data into staging tables</h4>
<p><b>Insert into staging events:</b></p>
<pre><code> staging_events_copy = ("""
    COPY staging_events 
        FROM {} 
        iam_role {} 
        region {}
        FORMAT AS JSON {} 
        timeformat 'epochmillisecs'
        TRUNCATECOLUMNS BLANKSASNULL EMPTYASNULL;
""").format(LOG_DATA, ARN, REGION, LOG_JSON_PATH)
</code></pre>
<p><b>Insert into staging songs:</b></p>
<pre><code>staging_songs_copy = ("""
    COPY staging_songs 
        FROM {}
        iam_role {}
        region {}
        FORMAT AS JSON 'auto' 
        TRUNCATECOLUMNS BLANKSASNULL EMPTYASNULL;
""").format(SONG_DATA, ARN, REGION)
</code></pre>
<h4><a id="user-content-insert-data-into-star-schema-from-staging-tables" class="anchor" aria-hidden="true" href="#insert-data-into-star-schema-from-staging-tables"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>Insert data into star schema from staging tables</h4>
<p><b>Insert Dimensions:</b></p>
<pre><code>
user_table_insert = ("""
    INSERT INTO users (user_id, first_name, last_name, gender, level)
        SELECT DISTINCT se.userId, 
                        se.firstName, 
                        se.lastName, 
                        se.gender, 
                        se.level
        FROM staging_events se
        WHERE se.userId IS NOT NULL;
""")


song_table_insert = ("""
    INSERT INTO songs (song_id, title, artist_id, year, duration) 
        SELECT DISTINCT ss.song_id, 
                        ss.title, 
                        ss.artist_id, 
                        ss.year, 
                        ss.duration
        FROM staging_songs ss
        WHERE ss.song_id IS NOT NULL;
""")

artist_table_insert = ("""
    INSERT INTO artists (artist_id, name, location, latitude, logitude)
        SELECT DISTINCT ss.artist_id, 
                        ss.artist_name, 
                        ss.artist_location,
                        ss.artist_latitude,
                        ss.artist_longitude
        FROM staging_songs ss
        WHERE ss.artist_id IS NOT NULL;
""")

time_table_insert = ("""
    INSERT INTO time (start_time, hour, day, week, month, year, weekday)
        SELECT DISTINCT  se.ts,
                        EXTRACT(hour from se.ts),
                        EXTRACT(day from se.ts),
                        EXTRACT(week from se.ts),
                        EXTRACT(month from se.ts),
                        EXTRACT(year from se.ts),
                        EXTRACT(weekday from se.ts)
        FROM staging_events se
        WHERE se.page = 'NextSong';
""")
</code></pre>
<p><b>Insert Facts table:</b></p>
<pre><code>songplay_table_insert = ("""
    INSERT INTO songplays (start_time, user_id, level, song_id, artist_id, session_id, location, user_agent) 
        SELECT DISTINCT se.ts, 
                        se.userId, 
                        se.level, 
                        ss.song_id, 
                        ss.artist_id, 
                        se.sessionId, 
                        se.location, 
                        se.userAgent
        FROM staging_events se 
        INNER JOIN staging_songs ss 
            ON se.song = ss.title AND se.artist = ss.artist_name
        WHERE se.page = 'NextSong';
""")
</code></pre>
<hr>
<h4><a id="user-content-project-structure" class="anchor" aria-hidden="true" href="#project-structure"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>Project structure</h4>
<p>The structure is:</p>
<ul>
<li><b> create_tables.py </b> - This script will drop old tables (if exist) ad re-create new tables</li>
<li><b> etl.py </b> - This script orchestrate ETL.</li>
<li><b> sql_queries.py </b> - This is the ETL. All the transformatios in SQL are done here.</li>
<li><b> /img </b> - Directory with images that are used in this markdown document</li>
</ul>
<p>We need an extra file with the credentials an information about AWS resources named <b>dhw.cfg</b></p>
</article>
  </div>


  
 
