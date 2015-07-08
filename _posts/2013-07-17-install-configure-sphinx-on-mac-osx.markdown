---
author: quyennt
comments: true
date: 2013-07-17 19:47:04+00:00
excerpt: 'People always search for what they need whenever they open a browser. If
  you''re running a website, then your website must provide people what they want
  when they jump into it, as quick as possible. But how? As a web developer, there''s
  must be a moment you think about which search engine to implement for your website. '
layout: post
slug: install-configure-sphinx-on-mac-osx
title: Install and Configure Sphinx on MAC OSX
wordpress_id: 86
categories:
- System
tags:
- mac-osx
- open source
- search engine
- sphinx
---

People always search for what they need whenever they open a browser. If you're running a website, then your website must provide people what they want when they jump into it, as quick as possible. But how? As a web developer, there's must be a moment you think about which search engine to implement for your website. You will be standing among three options:

<!-- more -->

![logo](http://quyennt.com/wp-content/uploads/2013/07/logo.png)



	
  * You may build your own search engine, basing on your database.

	
  * You may integrate Google search

	
  * Or you may choose to use standalone search engine like Sphinx or Apache Solr.


Each option has its own pros and cons: If you build you own search engine. The implementation would be quick and simple. You just need a good indexing of your database and write some SQL queries. But the supported features for searching is very poor. As your database size grows, say, some millions of rows, the time of querying may become unacceptable. Google Search is definitely quick, but it is not customizable, nor filterable. It may turn up your site for a wide-range search that make the results sometimes inaccurate.

So, to build a professional, quick, customizable search engine,  you may want to use standalone search engine like Sphinx or Apache Solr. It could take a while to implement, but its benefit is much worth it. That's why in this post I'd like to share with you my experience with Sphinx Search. Throughout this post, I hope you can quickly implement this great software into your websites. That's why in this blog I'd like to share with you my experience with Sphinx Search.

**Introduction**

Sphinx is an open source full-text search engine. It is free, and it is extremely quick. I tried my test on a database of 125,000 records and 5 indexed columns, the index takes about 600 MB of disk space (I'm running Mac OSX with 2.4 GHz Intel Core I5 and 4 GB RAM). A search of a 15-words phrase took about 0.59 sec for Sphinx to yield a result for any of those words. For a 4-words phrase, it took just 0.13 sec.

The result is very detailed. For each search, beside a list of items that match, you can also get the weight of each item found, the query time and many more. Below is an example returned by SphinxQL:

    
    +--------+--------+
    | id     | weight |
    +--------+--------+
    |  12004 |  13511 |
    |  62832 |  12510 |
    ….
    |  30841 |  12509 |
    +--------+--------+
    20 rows in set (0.13 sec)
    
    +---------------+--------+
    | Variable_name | Value  |
    +---------------+--------+
    | total         | 1000   |
    | total_found   | 13724  |
    | time          | 0.118  |
    | keyword[0]    | THU    |
    | docs[0]       | 76664  |
    | hits[0]       | 316831 |
    | keyword[1]    | TUONG  |
    | docs[1]       | 45926  |
    | hits[1]       | 120193 |
    +---------------+--------+


**Some of Sphinx's great features**



	
  * indexing text column (of course)

	
  * It is fully customized, and distributed. Therefore you can optimize your search engine in many ways to meet your hardware.

	
  * While it is not possible to create index on a view in MySQL, you can create a view joining multiple tables and Sphinx can do the indexing on that view.

	
  * Using realtime index, you can store your index data in RAM that can make searches faster.

	
  * You can associate attributes to your index for further filter search results

	
  * It support morphology and unicode search


**Installation**

Sphinx is written in C++, builds with the GNU compilers, supports 64-bit on capable platforms, and runs on Linux, UNIX®, Microsoft® Windows®, and Mac OS X. Installing Sphinx is simple:

    
    $ wget http://sphinxsearch.com/downloads/accept.php?file=sphinx-2.0.8-release.tar.gz
    $ tar -xzf sphinx-2.0.8-release.tar.gz
    $ cd sphinx-2.0.8-release
    $ ./configure --prefix=/usr/local/sphinx --with-mysql=/path/to/mysql/files
    $ make
    $ sudo make install


If your're using MAMP, since MAMP is missing some mysql's library and include files that sphinx requires, then you have to upgrade your MAMP by coping the required files to the `MAMP/Library/` directory.



	
  1. Go to your `MAMP/Library` directory and create mysql directory:

	
  2. Go to [http://downloads.mysql.com/archives.php](http://downloads.mysql.com/archives.php) and download the appropriated one. You could go to phpMyAdmin to check your MAMP's mysql version.

	
  3. Untar it and copy the `include/` and `lib/` to the `MAMP/Library/mysql` you just created before.

	
  4. In terminal, assuming your MAMP is located in `/Applications/`, then your configure should be like below:



    
    $ ./configure --prefix=/usr/local/sphinx --with-mysql-includes=/Applications/MAMP/Library/mysql/include --with-mysql-libs=/Applications/MAMP/Library/mysql/lib


**Components**

Sphinx has three components: an indexer, a search engine daemon, and a command-line search utility. All of them locate in `/usr/local/sphinx/bin/`



	
  * The indexer is an index generator. It queries your database, indexes text column and ties each index entry to the row's primary key. Data can be load into indexes via data source which will be defined in `sphinx.conf`. Sources can fetch data directly from MySQL, PostgreSQL, ODBC compliant database (MS SQL, Oracle, etc), or a pipe in a custom XML format

	
  * The search engine daemon is called `searchd`. It allows applications to access Sphinx via three methods: via native API (SphinxAPI), via Sphinx's implementation MySQL network protocol (SphinxQL), or via MySQL server with a pluggable storage engine (SphinxSE). I haven't try SphinxSE yet, but using native API or SphinxQL is very easy, especially SphinxQL that provides a MySQL environment to work with.

	
  * The search utility allow you to do searching from the command line without writing code. It might be useful for debugging your Sphinx configuration. Unfortunately, since there's an issue with this utility in the version 2.0.8 (the one I am using). I'd never had the chance to use it. However, you can use the built-in SphinxQL instead. As I just mentioned above, since SphinxQL supports MySQL binary network protocol and can be accessed with regular MySQL API, it provides the same environment as MySQL server command line tool.



    
    $ mysql -P9306 --protocol=tcp --prompt='sphinxQL> '
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 1
    Server version: 2.0.8 (r3831)
    
    Type 'help;' or '\h' for help. Type '\c' to clear the buffer.
    
    mysql> SELECT * FROM test1 WHERE MATCH('test')
        -> ORDER BY group_id ASC OPTION ranker=bm25;
    +------+--------+----------+------------+
    | id   | weight | group_id | date_added |
    +------+--------+----------+------------+
    |    4 |   1442 |        2 | 1231721236 |
    |    2 |   2421 |      123 | 1231721236 |
    |    1 |   2421 |      456 | 1231721236 |
    +------+--------+----------+------------+ 
    3 rows in set (0.00 sec)


**Sphinx Configuration**

To give you a clear picture of how to implement Sphinx, I will describe step by step the implementation of my case study: using Sphinx to search the content of articles. To simplify the case, there're just three tables:

    
    CREATE TABLE Article (
      id int(10) unsigned NOT NULL auto_increment,
      title varchar(500) NOT NULL,
      headline varchar(500) NOT NULL,
      content longtext NOT NULL,
      created_time int(11) NOT NULL,  
      author_name varchar(255),
      PRIMARY KEY (id) 
    ) ENGINE=InnoDB;
    
    CREATE TABLE Category (
      id int(10) unsigned NOT NULL auto_increment,
      name varchar(255) NOT NULL,
      description varchar(500),  
      PRIMARY KEY (id) 
    ) ENGINE=InnoDB;
    
    CREATE TABLE Article_Cat (
      article_id int(10) unsigned NOT NULL,
      cat_id int(10) unsigned NOT NULL,  
      PRIMARY KEY (article_id, cat_id) 
    ) ENGINE=InnoDB;


With the tables defined above, you can imagine that searching a phrase within the article like below are very costly:

    
    SELECT * 
    FROM Article 
    WHERE title like '%some phrase%' OR headline like '%some phrase%' OR content like '%some phrase%';
    
    or
    
    SELECT a.* 
    FROM Article a, Category c, Article_Cat ac 
    WHERE a.id=ac.article_id AND ac.cat_id=c.id AND c.name like '%some category%' 
          AND (a.title like '%some phrase%' OR a.headline like '%some phrase%' OR a.content like '%some phrase%');


The  JOIN and the LIKE are very expensive, especially when _Article_ is large, say, millions of rows. Not to mention that each article could have a long content. Even when using MyISAM engine which supports full-text indexing, the query time is still long. I tried a test by myself with 125,000 articles, a full-text index of three columns (title, headline and content), and it took about 35 secs to search a phrase of 4 words. (I use the same computer mentioned in Introduction section). Besides, what if I want to query the articles that contain any of the words I search?

To apply Sphinx, you must define at least one _source_ and one _index_ in `sphinx.conf`:



	
  * A _source_ identifies a database to indexes, provides connection, authentication information, and defines the query to fetch data from database. This is the text data where you will do your search.** The query's select statement must include an unique unsigned non-zero integer number. Usually it's the document primary key that should be selected.** Optionally, a _source_ can also define one or more columns as a filter or to sort on the result.

	
  * An _index_ is built as a data structure optimized for queries from the text data that you defined in your _source_.


Before defining a _source_, we must define from your database a select statement from where Sphinx will fetch data. In this case, we may want to search the articles among some specific categories. So we have to join 3 tables. we can create a view like below:

    
    CREATE VIEW view_article_cat AS
    SELECT a.id, a.title, a.headline, a.content, t.id as cat_id
    FROM Article a, Category c, Article_Cat ac
    WHERE a.id=ac.article_id AND ac.cat_id=c.id


And below is an example of `sphinx.conf`:

    
    source article 
    {    
        # The type of database from where Sphinx will fetch data
        type                            = mysql         # Authenticate information
        sql_host                        = localhost 
        sql_user                        = root
        sql_pass                        = 123456
        sql_db                          = articledb
        sql_sock                        =  /var/run/mysqld/mysqld.sock     
        sql_port                        = 3306
    
        # indexer query: provide data from the database, include all the fields you want to index
        # or use as filters
        # document_id MUST be the very first field
        # document_id MUST be positive (non-zero, non-negative) and unique
        # document_id MUST fit into 32 bits
        sql_query     = SELECT id, title, headline, content, cat_id \            
                        FROM view_article_cat;    
    
        # define attributes you want to use as filters    
        sql_attr_uint = cat_id     
    
        # document info query    
        # ONLY used by search utility to display document information
        # MUST be able to fetch document info by its id, therefore
        # MUST contain '$id' macro 
        #
        sql_query_info          = SELECT * FROM view_article_cat WHERE id=$id
    }
    
    index article
    {     
        # Specify the data source to index     
        source     = article          
    
        # Specify the location where indexing will store its data     
        path       = /usr/local/sphinx/var/data/article               
    
        # Define the morphology, so that 'cats' would be understood as 'cat'         morphology = stem_en
    }
    
    # Configuration for indexer
    indexer
    {
         # Specify the RAM usage limit for the indexer. Default is 32M, maximum is 2047M
         mem_limit          = 128M
    }
    
    # Configuration for searchd daemon
    searchd
    {
         # Specify the host:port that searchd will listen on. 
         # Here it will listen on all network interfaces through port 9312
         listen               = 9312
    
         # We can also specify a protocol handler like 'mysql41' 
         # which means MySQL protocol used by SphinxQL
         listen               = 9306:mysql41
    
         # Specify log and some others config
         log               = /usr/local/sphinx/var/log/searchd.log
         query_log          = /usr/local/sphinx/var/log/query.log
         read_timeout          = 5
         max_children          = 30
         pid_file          = /usr/local/sphinx/var/log/searchd.pid
         max_matches          = 1000
         seamless_rotate          = 1
         preopen_indexes          = 1
         unlink_old          = 1
         workers               = threads # for RT to work
         binlog_path          = /usr/local/sphinx/var/data
    }


**Indexing and Querying**

Until now we have been through the hard part, configuration. Of course there's still a lot more that we can do with the `sphinx.conf`, but for now it's more than enough to get us to the fun part: indexing, querying and see the result!

We run the command below to start indexing:

    
    $ sudo /usr/local/sphinx/bin/indexer --config /usr/local/sphinx/etc/sphinx.conf --all
    
    Sphinx 2.0.8-release (r3831)Copyright (c) 2001-2012, Andrew Aksyonoff
    Copyright (c) 2008-2012, Sphinx Technologies Inc (http://sphinxsearch.com)
    
    using config file '/usr/local/sphinx/etc/sphinx.conf'...
    indexing index 'article'...
    collected 124761 docs, 606.3 MB
    sorted 99.7 Mhits, 100.0% done
    total 124761 docs, 606251212 bytes
    total 77.557 sec, 7816842 bytes/sec, 1608.63 docs/sec
    total 22 reads, 4.827 sec, 11595.0 kb/call avg, 219.4 msec/call avg


**Note:** The -all argument makes all the indexes be built in case there are more than one index defined in `sphinx.conf`. You want to build only one index, just specify its name:

    
    $ sudo /usr/local/sphinx/bin/indexer --config /usr/local/sphinx/etc/sphinx.conf article


We can now querying the index to get what we want by using the search utility. Note that you don't need `searchd` running to use search.

    
    $ sudo /usr/local/sphinx/bin/search --config /usr/local/sphinx/etc/sphinx.conf [key words you want to search]
    ..
    index 'ttm': search error: .


Oops, if you like me, you will get the error above in 2.0.8 release. This is a common issue but there's still no fix until now. But don't worry, this error only occurs in search utility which is used just for testing.

To actually use Sphinx in your code, you have to use the provided Sphinx native API, or SphinxQL. And of course, the `searchd` daemon must be running by the command:

    
    $ sudo searchd -c /usr/local/sphinx/etc/sphinx.conf


The Sphinx APIs are located in `/usr/local/sphinx/api/` directory. Below is an example using php code & Sphinx API:

    
    <?php
      include('/usr/local/sphinx/api/sphinxapi.php');
    
      $cl = new SphinxClient();
      $cl->SetServer( "localhost", 9312 ); // Connect to localhost, port 9312 which searchd is listen
      $cl->SetMatchMode( SPH_MATCH_ANY  ); // Match any of the query word
      //$cl->SetFilter( 'cat_id', array( 3 ) );
    
      $result = $cl->Query( 'thu tuong chinh phu', 'article' );
    
      if ( $result === false ) {
          echo "Query failed: " . $cl->GetLastError() . ".\n";
      }
      else {
          if ( $cl->GetLastWarning() ) {
              echo "WARNING: " . $cl->GetLastWarning() . "\n";
          }            
          print_r( 'total_found => '. $result['total_found'] .' articles.' );   
          print_r( "\ntime =>". $result['time'] );
          print_r( "\nmatches => ");
          print_r($result['matches']);
    ?>


Assuming the filename that contain the code is test.php. Running the command below to see the result:

    
    $ php test.php
    total_found => 98032 articles.
    time =>0.061
    matches => Array
    (
        [12004] => Array
            (
                [weight] => 157
                [attrs] => Array
                    (
                    )
            )
        [30841] => Array
            (
                [weight] => 156
                [attrs] => Array
                    (
                    )
            )
        [62832] => Array
            (
                [weight] => 156
                [attrs] => Array
                    (
                    )
            )
    …
    [words] => Array
            (
                [THU] => Array
                    (
                        [docs] => 76664
                        [hits] => 316831
                    )
                [TUONG] => Array
                    (
                        [docs] => 45926
                        [hits] => 120193
                    )
                [CHINH] => Array
                    (
                        [docs] => 49294
                        [hits] => 150534
                    )
                [PHU] => Array
                    (
                        [docs] => 42225
                        [hits] => 116785
                    )
            )


As you can see, the test searches for each of the given words and returns a result of 98032 articles in 0.061 sec. The articles are also sorted by their weight.

So, I've been taking you through the basic implementation of Sphinx and I hope that helped. Of course, Sphinx has many more features to take advantage of and there's still much more to do to apply it into your real world project. For example:



	
  * How indexes are rebuilt when there some updates on your database? You will have to know how index is built and stored on disk.

	
  * How to customize the configuration to make the query even faster? You will have to know about memory usage and many other tips to optimize Sphinx.

	
  * How it supports unicode searching?

	
  * What about SphinxQL and SphinxSE?

	
  * .....


Don't worry, from this basic point, you will answer yourself all of those questions faster than you think. The [Sphinx documentation](http://sphinxsearch.com/docs/manual-2.0.8.html) is a good place to continue.
