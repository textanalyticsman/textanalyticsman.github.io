---
date: '2019-01-28 09:42:00'
tags: [SNA, Social Network Analysis, text analytics, text mining, co-occurrence, entity named recognition, entity named classification]
layout: post
cover: /pictures/2019-01-16-GneratingANetworkFromText/01.png
categories: Social_Network_Analysis
title: Generating a network from text written in Spanish - fixing entities
author: textanalyticsman
---
# Introduction

The [previous post](/social_network_analysis/GneratingANetworkFromText/) was about using the [software](https://github.com/textanalyticsman/extractnetworksfromtext) to get extract entities from unstructured text. What is more those entities were classified as persons, locations, organizations and others. However, as this software is still far from being perfect there are some miss-recognitions and miss-classifications that should be fixed manually. Therefore, the following steps are going to explain how to do it.

1. **Click on Analyzer > Review and fix entities**.


	| ![](/pictures/2019-01-28-NetworkFromTextFixingEntities/01.png) | 
	|:--:| 
	| *Option used to fix entities* |

2. **Click on Load available corpora**.

	| ![](/pictures/2019-01-28-NetworkFromTextFixingEntities/02.png) | 
	|:--:| 
	| *Looking for the corpus we are working on* |

3.  **Click on the corpus we want to fix**.

	| ![](/pictures/2019-01-28-NetworkFromTextFixingEntities/03.png) | 
	|:--:| 
	| *Selecting the corpus whose entities will be fixed* |
	
	
	After clicking on the previous corpus you are going to see all the entities that belong to this.
	
	| ![](/pictures/2019-01-28-NetworkFromTextFixingEntities/04.png) | 
	|:--:| 
	| *Entities that belong to the selected corpus* |

4.	**Fixing miss-recognitions**
	
	This is about deleting words that have been recognized as entities by mistake. To delete these wrong entities, just click on the check-box (located on the right hand) to select them and then click on *Delete selected entities*.
	
	| ![](/pictures/2019-01-28-NetworkFromTextFixingEntities/05.png) | 
	|:--:| 
	| *Selecting entities to be deleted* |	
	
	If you are not sure whether this is an entity or not just double click on the entity name to read the sentence where this appears.
	
	| ![](/pictures/2019-01-28-NetworkFromTextFixingEntities/06.png) | 
	|:--:| 
	| *Getting the sentence where this instance appear* |		

4.	**Fixing miss-classifications**	

	Here we are going to fix entities that have been miss-classified. For example, in the following picture a Peruvian party has been classified as a location so fixing this is just about clicking the cell under "Entity type" to choose the proper type, which in this case is "ORG" then you should click on *Update modified entities*.
	
	| ![](/pictures/2019-01-28-NetworkFromTextFixingEntities/07.png) | 
	|:--:| 
	| *Fixing a miss-classified entity* |	
	
# Conclusion

This post has shown how to fix miss-recognitions and miss-classifications and even though this is a cumbersome process, it is still better than doing a pure manual process. The next post is coming soon and is going to be about clustering entities.