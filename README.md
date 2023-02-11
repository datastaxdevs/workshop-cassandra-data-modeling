# 🎓🎓 Apache Cassandra® Data Modelling

Welcome to the **Apache Cassandra® Data Modelling** workshop! In this two-hour workshop, we show the methodology to build an effective data model with the distributed `NoSQL database Apache Cassandra™`.

Using **Astra DB**, the cloud based _Cassandra-as-a-Service_ platform delivered by DataStax, we will cover the process for every developer who wants to build an application: list the use cases and build an effective data model.

![](images/splash.png)

It doesn't matter if you join our workshop live or you prefer to do at your own pace, we have you covered. In this repository, you'll find everything you need for this workshop:

> [🔖 Accessing HANDS-ON](#-start-hands-on)

## 📋 Table of content

<img src="images/illustrations.png?raw=true" align="right" width="300px"/>

1. [Objectives](#1-objectives)
2. [Frequently Asked Questions](#2-frequently-asked-questions)
3. [Materials for the Session](#3-materials-for-the-session)
4. [Create Your Astra DB Instance](#4-create-your-astra-db-instance)
5. [Tables with Single-Row and Multi-Row Partitions](#5-tables-with-single-row-and-multi-row-partitions)
6. [Dynamic Bucketing](#6-dynamic-bucketing)
7. [Working with Data Types](#7-working-with-data-types)
8. [KDM Data Modeling Tool](#8-kdm-data-modeling-tool)
9. [Sensor Data Modeling](#9-sensor-data-modeling)
10. [Homework](#10-homework)
11. [What's NEXT ](#11-whats-next-)
<p><br/>

## 1. Objectives

1️⃣ **Understand how data is distributed and organized in Apache Cassandra®**

2️⃣ **Learn how primary, partition, and clustering keys are defined in Apache Cassandra®**

3️⃣ **Become familiar with CQL data types in Apache Cassandra®**

4️⃣ **Learn about the data modeling methodology for Apache Cassandra®**

🚀 **Have fun with an interactive session**

## 2. Frequently Asked Questions

<p/>
<details>
<summary><b> 1️⃣ Can I run this workshop on my computer?</b></summary>
<hr>
<p>There is nothing preventing you from running the workshop on your own machine. If you do so, you will need the following:
<ol>
<li><b>git</b> installed on your local system
</ol>
</p>
In this readme, we try to provide instructions for local development as well - but keep in mind that the main focus is development on Gitpod, hence <strong>we can't guarantee live support</strong> about local development in order to keep on track with the schedule. However, we will do our best to give you the info you need to succeed.
</details>
<p/>
<details>
<summary><b> 2️⃣ What other prerequisites are required?</b></summary>
<hr>
<ul>
<li>You will need enough "real estate" on screen, we will ask you to open a few windows and it would not fit on mobiles (tablets should be OK)
<li>You will need an Astra account: don't worry, we'll work through that in the following
<li>As "Intermediate level" we expect you to know what java and Spring are.
</ul>
</p>
</details>
<p/>
<details>
<summary><b> 3️⃣ Do I need to pay for anything for this workshop?</b></summary>
<hr>
<b>No.</b> All tools and services we provide here are FREE. FREE not only during the session but also after.
</details>
<p/>
<details>
<summary><b> 4️⃣ Will I get a certificate if I attend this workshop?</b></summary>
<hr>
Attending the session is not enough. You need to complete the homework detailed below and you will get a nice badge that you can share on LinkedIn or anywhere else.
</details>
<p/>

## 3. Materials for the Session

It doesn't matter if you join our workshop live or you prefer to work at your own pace,
we have you covered. In this repository, you'll find everything you need for this workshop:

- [Slide deck](/slides/slides.pdf)
- [Discord chat](https://dtsx.io/discord)
- [Questions and Answers](https://community.datastax.com/)

----

# 🏁 Start Hands-on

## 4. Create Your Astra DB Instance

_**`ASTRA DB`** is the simplest way to run Cassandra with zero operations at all - just push the button and get your cluster. No credit card required, 40M read/write operations and about 80GB storage monthly for free - sufficient to run small production workloads. If you end your credits the databases will pause, no charge_

Leveraging [Database creation guide](https://awesome-astra.github.io/docs/pages/astra/create-instance/#c-procedure) create a database. *Right-Click the button* with *Open in a new TAB.*

<a href="https://astra.dev/yt-7-27"><img src="images/create_astra_db_button.png?raw=true" /></a>

|Field|Value|
|---|---|
|**Database Name**| `workshops`|
|**Keyspace Name**| `sensor_data`|
|**Regions**| Select `GOOGLE CLOUD`, then an Area close to you, then a region with no LOCKER 🔒 icons, those are the region you can use for free.   |

> **ℹ️ Note:** If you already have a database `workshops`, simply add a keyspace `sensor_data` using the `Add Keyspace` button on the bottom right hand corner of db dashboard page.

While the database is being created, you will also get a **Security token**:
save it somewhere safe, as it will be needed to later in other workshops (In particular the string starting with `AstraCS:...`.)

> **⚠️ Important**
> ```
> The instructor will show you on screen how to create a token
> but will have to destroy to token immediately for security reasons.
> ```

The status will change from `Pending` to `Active` when the database is ready, this will only take 2-3 minutes. You will also receive an email when it is ready.

[🏠 Back to Table of Contents](#-table-of-content)

## 5. Tables with Single-Row and Multi-Row Partitions

A [GitHub](https://github.com) account may be required to run this hands-on lab in Gitpod.

### ✅ Part 1: [Tables with Single-Row Partitions](https://gitpod.io/#https://github.com/DataStax-Academy/workshop-cassandra-data-modeling-tables-single-row-partitions/)

### ✅ Part 2: [Tables with Multi-Row Partitions](https://gitpod.io/#https://github.com/DataStax-Academy/workshop-cassandra-data-modeling-tables-multi-row-partitions/)

[🏠 Back to Table of Contents](#-table-of-content)

## 6. Dynamic Bucketing

<details>
  <summary> <h3>📌 Homework 1</h3></summary>

Consider the table that supports query `Find all sensors in a specified network`:
```sql
CREATE TABLE sensors_by_network_2 (
  network TEXT,
  sensor TEXT,
  PRIMARY KEY ((network), sensor)
);
```

Assume that a network may have none to millions of sensors. With dynamic bucketing, we can introduce artificial buckets to store sensors. A network with a few sensors may only need one bucket. A network with many sensors may need many buckets. Once buckets belonging to a particular network get filled with sensors, we can dynamically assign new buckets to store new sensors of this network.

📘 **Implement dynamic bucketing in Astra DB**
```sql
-- Table to manage buckets
CREATE TABLE buckets_by_network (
  network TEXT,
  bucket TIMEUUID,
  PRIMARY KEY ((network), bucket)
) WITH CLUSTERING ORDER BY (bucket DESC);

-- Table to store sensors
CREATE TABLE sensors_by_bucket (
  bucket TIMEUUID,
  sensor TEXT,
  PRIMARY KEY ((bucket), sensor)
);


-- Sample data
INSERT INTO buckets_by_network (network, bucket) VALUES ('forest-net', 49171ffe-0d12-11ed-861d-0242ac120002);
INSERT INTO buckets_by_network (network, bucket) VALUES ('forest-net', 74a13ede-0d12-11ed-861d-0242ac120002);

INSERT INTO sensors_by_bucket (bucket, sensor) VALUES (49171ffe-0d12-11ed-861d-0242ac120002, 's1001');
INSERT INTO sensors_by_bucket (bucket, sensor) VALUES (49171ffe-0d12-11ed-861d-0242ac120002, 's1002');

INSERT INTO sensors_by_bucket (bucket, sensor) VALUES (74a13ede-0d12-11ed-861d-0242ac120002, 's1003');
```

📘 **Add a new sensor to a network**

1. Get the latest bucket.
```sql
SELECT bucket FROM buckets_by_network WHERE network = 'forest-net' LIMIT 1;
```

2. Check the number of sensors in the bucket.
```sql
SELECT COUNT(*) AS sensors
FROM sensors_by_bucket WHERE bucket = 74a13ede-0d12-11ed-861d-0242ac120002;
```

3. Depending on the sensors-per-bucket threshold, insert a new sensor into the existing bucket, or create a new bucket and insert into the new bucket.
```sql
INSERT INTO sensors_by_bucket (bucket, sensor) VALUES (74a13ede-0d12-11ed-861d-0242ac120002, 's1004');
```

📘 **Retrieve sensors in a specified network**

1. Retrieve the buckets
```sql
SELECT bucket FROM buckets_by_network WHERE network = 'forest-net';
```

2. Retrieve the sensors
```sql
SELECT sensor
FROM sensors_by_bucket
WHERE bucket IN (74a13ede-0d12-11ed-861d-0242ac120002, 49171ffe-0d12-11ed-861d-0242ac120002);
```

</details>

[🏠 Back to Table of Contents](#-table-of-content)

## 7. Working with Data Types

<details>
  <summary> <h3>📌 Homework 2</h3></summary>

### ✅ Step 7a. `List` Collections

📘 **Command to execute**

```sql
// Definition
CREATE TABLE IF NOT EXISTS table_with_list (
  uid      uuid,
  items    list<text>,
  PRIMARY KEY (uid)
);

// Insert
INSERT INTO table_with_list(uid,items)
VALUES (c7133017-6409-4d7a-9479-07a5c1e79306, ['a', 'b', 'c']);

// Replace
UPDATE table_with_list SET items = ['d', 'e']
WHERE uid = c7133017-6409-4d7a-9479-07a5c1e79306;

// Show result
SELECT * FROM table_with_list ;

// Append to list
UPDATE table_with_list SET items = items + ['f']
WHERE uid = c7133017-6409-4d7a-9479-07a5c1e79306;

// Replace an element (not available in Astra because read before write)
UPDATE table_with_list SET items[0] = ['g']
WHERE uid = c7133017-6409-4d7a-9479-07a5c1e79306;
```

### ✅ Step 7b. `Set` Collections

📘 **Command to execute**

```sql
// Definition
CREATE TABLE IF NOT EXISTS table_with_set (
  uid      uuid,
  animals  set<text>,
  PRIMARY KEY (uid)
);

// Insert
INSERT INTO table_with_set(uid,animals)
VALUES (87fad746-4adf-4107-9858-df8643564186, {'spider', 'cat', 'dog'});

// Replace
UPDATE table_with_set SET animals = {'pangolin', 'bat'}
WHERE uid = 87fad746-4adf-4107-9858-df8643564186;

// Show result
SELECT * FROM table_with_set;

// Append to Set
UPDATE table_with_set SET animals = animals + {'sheep'}
WHERE uid = 87fad746-4adf-4107-9858-df8643564186;
```

### ✅ Step 7c. `Map` Collections

📘 **Command to execute**


```sql
// Definition
CREATE TABLE IF NOT EXISTS table_with_map (
  uid         text,
  dictionary  map<text, text>,
  PRIMARY KEY (uid)
);

// Insert
INSERT INTO table_with_map(uid, dictionary)
VALUES ('fr_en', {'fromage':'cheese', 'vin':'wine', 'pain':'bread'});

// Replace
UPDATE table_with_map SET dictionary = {'saucisse': 'sausage'}
WHERE uid = 'fr_en';

// Show result
SELECT * FROM table_with_map;

// Append to Map
UPDATE table_with_map SET dictionary = dictionary + {'frites':'fries'}
WHERE uid = 'fr_en';
```

### ✅ Step 7d. User-Defined Types

📘 **Command to execute**

```sql
// Definition
CREATE TYPE IF NOT EXISTS udt_address (
  street text,
  city text,
  state text,
);

// Use the UDT in a table
CREATE TABLE IF NOT EXISTS table_with_udt (
  uid      text,
  address   udt_address,
  PRIMARY KEY (uid)
);

// INSERT (not quote on field names like street)
INSERT INTO table_with_udt(uid, address)
VALUES ('superman', {street:'daily planet',city:'metropolis',state:'CA'});

// Replace
UPDATE table_with_udt
SET address = {street:'pingouin alley',city:'antarctica',state:'melting'}
WHERE uid = 'superman';

// Replace a single field
UPDATE table_with_udt
SET address.state = 'melt'
WHERE uid = 'superman';
```

### ✅ Step 7e. Counters

📘 **Command to execute**

```sql
// Definition
CREATE TABLE IF NOT EXISTS table_with_counters (
  handle        text,
  following     counter,
  followers     counter,
  notifications counter,
  PRIMARY KEY (handle)
);

// You have a new follower
UPDATE table_with_counters SET followers = followers + 1
WHERE  handle = 'clunven';

// Some counters are... null
SELECT * from table_with_counters;

// Set to 0... but set is not valid
UPDATE table_with_counters
SET following = following + 0, notifications = notifications + 0
WHERE handle = 'clunven';

// Following someone
UPDATE table_with_counters SET following = following + 1
WHERE handle = 'clunven';

// You have a new message
UPDATE table_with_counters SET notifications = notifications + 1
WHERE handle = 'clunven';

```

</details>

[🏠 Back to Table of Contents](#-table-of-content)

## 8. KDM Data Modeling Tool

<details>
  <summary> <h3>🍿 Demo</h3></summary>

### ✅ Download [the project XML file](https://raw.githubusercontent.com/datastaxdevs/workshop-cassandra-data-modeling/main/materials/kdm_sensor_data.xml).

### ✅ Open [the KDM tool](http://kdm.kashliev.com/).

### ✅ Import the project by selecting `Import Project` from the menu and specifying file `kdm_sensor_data.xml`.

![](images/kdm_01.png)

![](images/kdm_02.png)

### ✅ Explore the five data modeling steps supported by KDM. Note that the conceptual data model in Step 1 and queries in Step 2 are already defined.

![](images/kdm_03.png)

</details>

[🏠 Back to Table of Contents](#-table-of-content)

## 9. Sensor Data Modeling

A [GitHub](https://github.com) account may be required to run this hands-on lab in Gitpod.

### ✅ [Sensor Data Modeling](https://gitpod.io/#https://github.com/DataStax-Academy/workshop-cassandra-data-modeling-sensor-data/)

[🏠 Back to Table of Contents](#-table-of-content)


## 10. Homework

1. Complete [Working with Data Types](#7-working-with-data-types). Take a screenshot of the CQL Console showing the rows in tables
`table_with_udt` and `table_with_counters` before _and_ after executing the DELETE statements.

2. Complete the mini-course [Time Series Data Modeling](https://www.datastax.com/learn/data-modeling-by-example/time-series-model). Take a screenshot of the final screen of the practice lab, with the console output at the right.

3. [Submit your homework](https://forms.gle/Z69y4MM3SpEDg7nt5) and be awarded a nice verifiable badge!

[🏠 Back to Table of Contents](#-table-of-content)

## 11. What's NEXT ?

We've just scratched the surface of what you can do using Astra DB, built on Apache Cassandra.

Go take a look at [DataStax for Developers](https://www.datastax.com/dev) to see what else is possible.
There's plenty to dig into!

Congratulations: you made to the end of today's workshop.

![Badge](images/badge_data_modeling.png)

**... and see you at our next workshop!**

> Sincerely yours, The DataStax Developers
