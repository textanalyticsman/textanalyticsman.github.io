---
date: '2019-01-16 09:42:00'
tags: [SNA, Social Network Analysis, text analytics, text mining, co-occurrence, entity named recognition, entity named classification, generating a social network from unstructured text]
layout: post
cover: /pictures/2019-01-16-GneratingANetworkFromText/01.png
categories: Social_Network_Analysis
title: Network text analysis - Extracting and recognizing entities
author: textanalyticsman
---
# Introduction

Extracting a network from unstructured text is not an easy task because it implies reading several pages to identify persons,organizations, places, etc. that appear between these sentences and paragraphs with the goal of creating relationships between them.

| ![](/pictures/2019-01-16-GneratingANetworkFromText/10.png) | 
|:--:| 
| *In this picture you can get an idea about how cumbersome a pure manual process is* |

Furthermore, some of the entities recognized during the process can be exactly the same entity because the usage of aliases, first names or surnames indistinctly, which makes this process even more difficult. Even though, today there are many tools to visualize and to analyse social networks, one of the main problems that people have (when they want to analyse networks described by unstructured text written in Spanish) is the lack of tools to face these problems.

* Recognition of entities, extracting entities from unstructured text.
* Classification of entities, classifying entities such as persons, organizations, places, etc.
* Normalization of entities, which is about recognizing that some entities are the same even they look different between each other. For example, "Lev Davidovich Bronstein" and "Leon Trotsky"
* Generating relationships, which is about using a criteria to link entities recognized in the text.

The software available in this link: [GitHub repository](https://github.com/textanalyticsman/extractnetworksfromtext), is a tool to help in this process and tries to set a workflow, which starts with the ingestion of unstructured text to finish with a network as an output. 

| ![](/pictures/2019-01-16-GneratingANetworkFromText/09.png) | 
|:--:| 
| *In this picture the workflow implemented by this software is shown* |

Thus, I believe that you should have these questions.

* How could you recognize and classify entities automatically? You can do it through two methods Named Entities Recognition (NER) sand Named
Entities Classification (NEC). This two problems are solved thanks to [FreeLing](http://nlp.lsi.upc.edu/freeling/node/1), an engine to process natural language written in C++,
which in this case is called is used through an API for Java.

* How could you relate entities that are the same, but look different? In fact, you can use a clustering algorithm as part of this process and fixing the rest (those are totally different) manually. 

* How could you generate relationships between entities to generate a network? When you are reading a book or newspaper, you can realize different types or relationships such as friendship, kinship, subordination and so on. However, you are using your intelligence and knowledge about a particular language to do it, this is a difficult problem for a machine so in this version, I am using something easier, linking entities that co-occur either in the same sentence or in the same paragraph because most likely there is some sort of relationship between them.

* What about the direction of the network? This software generates an undirected network because we are building a network based on co-occurrence so at this stage this software is not smart enough to tell you the direction of a relationship.

* Is the weight of the relationships calculated? Yes, it is. Taking into account that two entities can co-occur along several pages and the fact this network is undirected (meaning that edge A-B is the same a B-A) this software is able to calculate weights per link that can be visualized using software such as Gephi. For example, in the following figure you can see some edges are thicker than others and this is because these couple of entities co-occur several times.

| ![](/pictures/2019-01-16-GneratingANetworkFromText/01.png) | 
|:--:| 
| *A social network extracted from unstructured text where you can see the weight of the relationships* |

# Extracting and recognizing entities

This post is the first one of a series that will show you how to build a network composed by entities extracted from unstructured text written in Spanish. The problem solved in this post is the recognition and classification of entities, which is solved through two methods Named Entities Recognition(NER) and Named
Entities Classification (NEC). 

How do NEC and NER look like? When we read a text, let us say a novel, we can recognize entities such as persons, organizations, places, etc. so NER is about automating the 
recognition of entities and NEC is the part that will allow the classification of entities recognized by NER. For example, the following picture remarks some entities recognized
in a text taken from this [link](https://larepublica.pe/politica/1397419-informacion-odebrecht-servira-sancionar-corruptos)

| ![](/pictures/2019-01-16-GneratingANetworkFromText/01_01.png) | 
|:--:| 
| *Entities recognized while you read this text* |

This is the fist step of the workflow described above; the network that I am going to create here is based on ten articles from a Peruvian newspaper. Thus, following I am going to show you how to use the SW to ingest data, get entities and classify entities as persons (PER), locations (LOC), organizations (ORG) and other (OTH). Others (OTH) is used when FreeLing (engine used by this software) is not able to classify an entity.  

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
	
3.  **Extracting entities from the corpus**.		

    * Click on *Analyzer>Extract entities*
	
	![](/pictures/2019-01-16-GneratingANetworkFromText/07.png)	
	
    * Click on *Load available corpora* and then the corpus created above *CorpusDemos_17012019* should be selected. Click on *Extract entities from selected corpus* and recognized entities should appear on the table below
	
| ![](/pictures/2019-01-16-GneratingANetworkFromText/08.png) | 
|:--:| 
| *Eventually, you can see the entities extracted from unstructured text and their classification as PER, LOC, ORG and OTH* |
	
# Conclusion
In this post the first part of the workflow, which means the ingestion of data and the extraction and classification of entities, has been shown. In the following post, I will show you how to use the same software to review and to fix entities, which is still hard. However, it is much better that doing the whole process by hand.