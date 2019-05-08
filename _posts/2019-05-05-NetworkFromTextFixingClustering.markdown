---
date: '2019-05-05 16:28:00'
tags: [SNA, Social Network Analysis, text analytics, text mining, co-occurrence, entity named recognition, entity named classification,clustering, generating a social network from unstructured text, network text analysis]
layout: post
cover: /pictures/2019-01-16-GneratingANetworkFromText/01.png
categories: Social_Network_Analysis
title: Network text analysis - Fixing the clustering process
author: textanalyticsman
---
# Introduction

The [previous post](/social_network_analysis/NetworkFromTextClusteringEntities/) was about using the [software](https://github.com/textanalyticsman/extractnetworksfromtext) to generate clusters composed by similar instances. As the clustering process is not perfect, a module to fix its errors  has been created. The module offers two options to fix clustering problems, the generation of new clusters and the movement of instances from a source cluster into a destination cluster. 


# Generate new clusters by hand

In order to generate new clusters by hand, you should follow these steps.

1.  **Click on Analyzer >> Review and fix clustering >> Generate new clusters by hand**. This option will open a screen that will be explained in the next point.

	| ![](/pictures/2019-05-05-NetworkFromTextFixingClustering/01.png) | 
	|:--:| 
	| *Option used to generate new clusters using the current set of instances that have been miss-classified* |

1.  **This screen is used to generate clusters manually.** After clicking on "Load available corpora", you should select the corpus and the available cluster(s) configuration (a combination of MinPts and Epsilon) to see the list of clusters that have been identified for this corpus. If you click on any of the available clusters you will see its members under the section Entities that belong to the selected cluster. 

	| ![](/pictures/2019-05-05-NetworkFromTextFixingClustering/02.png) | 
	|:--:| 
	| *Screen used to generate new clusters manually* |
	
1.  **Checking each cluster.** To start creating new clusters taking out those entities that by mistake have been assigned to a wrong cluster, each one of the available clusters should be verified. At this moment, I have not done any statistical analysis about the performance of this clustering process. However, based on my trials with this [software](https://github.com/textanalyticsman/extractnetworksfromtext), I can say that its performance looks acceptable. Thus, based on the empirical usage of this tool, I can say the best source to create new clusters is analysing the noise set, which means all those entities that have not been included as part of a proper cluster.

	These are examples of proper clusters identified by this [software](https://github.com/textanalyticsman/extractnetworksfromtext)

	| ![](/pictures/2019-05-05-NetworkFromTextFixingClustering/03.png) | 
	|:--:| 
	| *A cluster where the entity César Hinostroza Pariachi is identified in its different ways. For example, César Hinostroza Pariachi, Hinostroza Pariachi, Hinostroza, César Hinostroza. However, it is obvious that someone with the same or with a similar name can be miss-identified. Thus, the user's knowledge about the corpus should be used to fix these cases* |

	| ![](/pictures/2019-05-05-NetworkFromTextFixingClustering/04.png) | 
	|:--:| 
	| *A cluster where the entity Walter Ríos Montalvo is identified in its different ways. For example, Walter Ríos Montalvo, Walter Ríos, Walter and Ríos.* |
	
	This is an example of Noise, which means entities that are not part of any cluster

	| ![](/pictures/2019-05-05-NetworkFromTextFixingClustering/05.png) | 
	|:--:| 
	| *When you click on Noise, you can see all the entities that do not belong to any cluster* |	
	
1. **Creating a new cluster.** In this example, I have used the Noise set to create a cluster after recognizing two or more entities (two in this example), which are the same, I have selected them and I have clicked on "Generate a new cluster"

	| ![](/pictures/2019-05-05-NetworkFromTextFixingClustering/06.png) | 
	|:--:| 
	| *Generating a new cluster with two entities taken from the Noise set* |	
	
	| ![](/pictures/2019-05-05-NetworkFromTextFixingClustering/07.png) | 
	|:--:| 
	| *A confirmation dialog that confirms a new cluster has been created* |		

	This process should continue until generating all the missing clusters.
	
# Moving entities between clusters
	
In order to move entities from a source cluster into a destination cluster, you should follow these steps.

1. **Click on Analyzer >> Review and fix clustering >> Move entities between clusters**

	| ![](/pictures/2019-05-05-NetworkFromTextFixingClustering/08.png) | 
	|:--:| 
	| *Option used to move entities from a source cluster into a destination cluster* |
	
1. **This screen is used to move entities between clusters.** After clicking on "Load available corpora", you should select the corpus and the available cluster(s) configuration (a combination of MinPts and Epsilon) to see the list of clusters that have been identified for this corpus. On this screen you will two lists of clusters (the first list is the source and the second list is the destination) located on the left. If you click on any of the listed clusters, you will see its members on the right hand. Importantly, the Noise set is not a cluster; however, in order to minimize the GUI's complexity, the Noise set is tagged as another cluster.

	| ![](/pictures/2019-05-05-NetworkFromTextFixingClustering/09.png) | 
	|:--:| 
	| *Screen used to move entities from a source cluster into a destination cluster. In this example an entity from the Noise set CAVASSA RONCALLA has been identified as a member of a destination cluster* |
	
1. **Moving entities.** In this case we want to move an entity from the Noise set into a real cluster. Moving an entity means that a source cluster and a destination cluster should be selected and then an entity(s) from the source cluster should be checked to execute the movement using the button "Move selected entities"

	| ![](/pictures/2019-05-05-NetworkFromTextFixingClustering/10.png) | 
	|:--:| 
	| *Moving an entity from the Noise set into a destination cluster* |
	
	| ![](/pictures/2019-05-05-NetworkFromTextFixingClustering/11.png) | 
	|:--:| 
	| *How clusters look after moving entities between them* |	
	
	This process should continue until fixing all the miss-classified entities.
	
# Conclusion
At this point I have not found a way to reduce these manual tasks and hopefully computer scientists are getting more insights about this to get a fully automated process. On the other hand, it is awesome that for some processes such as recognizing whether a named entity belong to a cluster or not, the human knowledge is still necessary. Perhaps I am wrong, but I believe this kind of approach can generate more opportunities for people that will be able to work together with these tools to give the human touch that cannot be automated. In fact, tools like this can generate even more opportunities because of the cadence to generate more challenges, which at least at this moment, cannot be solved by computers. 
