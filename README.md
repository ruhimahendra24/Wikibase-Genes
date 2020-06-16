
Load data into wikibase

Files to go along with this tutorial: https://medium.com/@thisismattmiller/wikibase-for-research-infrastructure-part-1-d3f640dfad34

Creating a local wikibase instance and loading genetic information from DroughtDB:

To become familiar with wikibase wikidata and genetic data bases, I uploaded genetic information that is available from Drought Stress Gene Database onto my local wikibase. This document encompasses all the steps I took to accomplish this task. 

Step 1: install wikibase
The first step in this process would be to install wikibase locally onto your device. I have accomplished this on both ubuntu and macOS. I made tweaks to several tutorials that are available online. I have indicated all the steps I took to install the wikibase however a lot of the steps are what I learned from available online tutorials. 

The main two tutorials I followed are: 
https://semlab.io/howto/wikibase_basic
https://github.com/wmde/wikibase-docker/blob/master/README-compose.md

The first step would be to install docker onto your device and make sure you increase the memory limit of your machine to more than 4GB
I then proceeded to download the docker-compose.yml file example which I found here: https://github.com/wmde/wikibase-docker/blob/master/README-compose.md. The command to download the file is: wget https://raw.githubusercontent.com/wmde/wikibase-docker/master/docker-compose.yml

I edited the yml file to anywhere it said “localhost”and changed it to the ip address of my machine (line 47). I also changed the password and email for the environment (line 40,41). 
I also removed the quick statements section of the yml file as quick statements is not required to load the genetic data onto the local wikibase. (line 24, 133-152, 158)
I then followed the tutorial I linked above and entered the following commands on my terminal in the folder where the yml file is saved: 
docker-compose pull
docker-compose up
 Once the terminal window keeps repeating the lines “Got no real changes…”, “Sleeping for 10 secs”. Follow Matt Miller’s tutorial video starting at 9:34 up to 11:46. Make sure when you sign in your local wikibase to use the same credentials as those in your yml file. 
Once data is added to your wikibase, close the terminal window 
Type the command “docker-compose ps” to ensure that all the environments were installed 

Step 2: setting up bots

To use python scripts to load our data onto our local wikibase, we will have to set up bots on our wikibase. I followed the “bootstrapping data” section in this tutorial: https://medium.com/@thisismattmiller/wikibase-for-research-infrastructure-part-1-d3f640dfad34. 
Make sure to store your bot passwords as we are going to need it later. 

Step 3: Accessing the genetic data from DroughtDB 

To access the data from  Drought Stress Gene Database go to the “Database” page and select export genes on the bottom left. This should provide you with a csv file containing the data. 

Step 4: modifying files with our local wikibase information

For the remaining steps, I used Jocelyn’s repository to aid me in organizing my data and modifying the python scripts created by Matt Miller. 

I have all my csv files and scripts that I used in this repository: https://github.com/ruhimahendra24/Wikibase-Genes

We need to make sure that we add the bot password that we received in the previous step into the “password” file
We need to update the information for our local wikibase in the “local.py” file 
We also need to update our localhost information in line 36 of “user_config.py”


Step 5: Creating CSV files and loading the data onto wikibase

To be able to run the python scripts, make sure that you have python3 and that you have the following libraries installed: 
WikiDataIntegrator
pywikibot

The first csv file is add_properties.csv: 
The csv file contains 4 properties that I want to add to my local wikibase. The properties must include a label, the data type and a description. 
I want to be able to link every gene or organism to it’s “instance of” (gene or organism) and therefore the datatype for the property “instance of” is a wikibase-item because “organism” and “gene” are going to be items in my local wikibase. 
“Organism found in” will link the gene items to the organisms items which is why it is also a datatype of wikibase-item. 
“Biological Function” and “Phenotype” are both strings as that information will be provided in the data item of the gene.
Once we have created the csv file, to load the properties onto our wikibase we can enter this command in our terminal: add_properties.py add_properties.csv

add_core_items.csv:
This csv file contains two items: “Gene” and “Organism”. The items must include a label and a description. These are considered to be “classification items” as all the other items that will be added to the wikibase will be either Genes or Organisms. 
Once this csv file has been created, we can load the core items onto our wikibase by entering this command in our terminal: add_items.py add_core_items.csv

add_organisms.csv: 
This csv file contains all the organisms that are from the DroughtDB database that we want to add to our local wikibase as items. The items must include a label and the wikiID of the “classification item” we want to link it to. In this case, we are linking them to the item “Organism” and in my local wikibase, the wikidata item is Q2. You can check for the wikiID in add_core_items_updated.csv once you run the add_items.py with add_core_items.csv. 
Once this csv file has been created, we can load the organism items onto our wikibase by entering this command in our terminal: add_items.py add_organisms.csv


add_genes.csv: 
This CSV file contains all the genes that are from the DroughtDB database that we want to add to our local wikibase as items. The items must include a label, a wikiID of the “classification item” we want to link it to. In this case, we are linking them to the item “Gene”. The item must also include the wikiID of the organisms we want to link the gene to. We can find the wikiID of the organism in add_organisms_updated.csv after we run add_items.py with add_organisms.csv. We also want to include the Biological Function and Phenotype which can be found in the exported csv file from DroughtDB. 
Once this csv file has been created, we can load the gene items onto our wikibase by entering this command in our terminal: add_items.py add_genes.csv

We should now be able to see all of the items and properties on our local wikibase! 
