---
date: '2019-01-16 09:42:00'
tags: SNA
layout: post
cover: /pictures/2019-01-16-GneratingANetworkFromText/01.png
categories: Social_Network_Analysis
title: Generating a network from text written in Spanish
author: textanalyticsman
---

In this post I'm going to show you how to build a network based on co-occurrence
using as input some articles from a Peruvian newspaper. The software used in
this example is just a desktop application and it is available
[here](https://github.com/textanalyticsman/extractnetworksfromtext). Here, you are going
to load some unstructured data into the database.

1.  **Creating some articles on the database**.

    This step assumes you have installed the software available
    [here](https://github.com/textanalyticsman/extractnetworksfromtext) and then
    in order to load data into the MySQL database you should do this.

    * Create a CSV file with four fields Date, Title, Summary and Content as you
    can see in the following figure.

    ![](/pictures/2019-01-16-GneratingANetworkFromText/02.png)

	* Use Notepad++ convert the CVS file into a UTF-8 file.

    ![](/pictures/2019-01-16-GneratingANetworkFromText/03.png)
	
	* Let us say you have saved your CSV file as C:\Data\MyFile.csv; open MySQL Workbench to run this script.
    ```sql
    LOAD DATA LOCAL INFILE 'C:\\Data\\MyFile.csv'
    INTO TABLE octoparseimport     
    FIELDS TERMINATED BY ','
    OPTIONALLY ENCLOSED BY '"'
    LINES TERMINATED BY '\r\n'
    IGNORE 1 LINES
    (Date,Title,Summary,Content);
    end
    ``` 
	After this step the data has been loaded into a table called **octoparseimport**; this table has the following fields.
	
        * Date, the date when the journalistic article was written.
	    * Title, the article's title.
	    * Summary, some short information about the article.
	    * Content, the whole article
		
2.  **Using the software to create a corpus**.		

	In this case we have to click on Corpus>Create corpus
	
	![](/pictures/2019-01-16-GneratingANetworkFromText/04.png)
	
	The previous option will show a list of available documents you can choose to build a corpus. In this demo just 10 of them are selected, the corpus is named as *CorpusDemos_17012019* and then click on *Generate corpus*.
	
	![](/pictures/2019-01-16-GneratingANetworkFromText/05.png)
	
	If everything works fine, this dialog should appear.

	![](/pictures/2019-01-16-GneratingANetworkFromText/06.png){:height="200px" width="200px"}	
	
2.  **Extracting entities from the corpus**.		

    * Click on *Analyzer>Extract entities*
	
	![](/pictures/2019-01-16-GneratingANetworkFromText/07.png)	
	
    * Click on *Load available corpora* and then the corpus created above *CorpusDemos_17012019* should be selected. Click on *Extract entities from selected corpus* and recognized entities should appear on the table below
	
	![](/pictures/2019-01-16-GneratingANetworkFromText/08.png)		
	
*This is work in progress I will continue soon...*	