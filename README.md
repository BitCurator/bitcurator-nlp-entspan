![Logo](https://wiki.bitcurator.net/downloads/BitCurator-400px.png)

# bitcurator-nlp-entspan

[![GitHub issues](https://img.shields.io/github/issues/bitcurator/bitcurator-nlp.svg)](https://github.com/bitcurator/bitcurator-nlp/issues)
[![GitHub forks](https://img.shields.io/github/forks/bitcurator/bitcurator-nlp.svg)](https://github.com/bitcurator/bitcurator-nlp/network)

Entity extraction and span identification for heterogeneous document types. Build instructions and dependencies can be found below or in setup.txt. This project is in development.

## Installing and running the entity extraction and analysis tools.

### Create Conda virtual environment
- Install Conda from
https://www.anaconda.com/download/#linux

To install conda from the command line in Ubuntu, run the following commands (we'll use Release 5.1.0 as an example here):

```shell
# First install curl
sudo apt-get install curl
# Get a specific Anaconda2 release:
curl -O https://repo.continuum.io/archive/Anaconda2-5.1.0-Linux-x86_64.sh
# Run the install script
bash Anaconda2-5.1.0-Linux-x86_64.sh
```

Type "yes" and hit enter to accept the license, then scroll through the text and hit enter to install into your home directory.

You can find a helpful overview of common Conda commands on the cheatsheet: https://conda.io/docs/_downloads/conda-cheatsheet.pdf.


- Create Conda virtual environment:
```shell
#conda create --name < envname > python=2.7  
conda create --name entspan python=2.7  
```

- List the virtual envs created:  
```shell
conda info --envs  
```
- Install conda-forge channel (It has most of the packages we need)  
```shell
conda install --channel conda-forge  python=2.7
```
- To get into conda virtual environment, do:  
```shell
#source activate < name >  
source activate entspan  
```

- Install the required packages:    

```shell
conda install spacy  
conda install textract  
conda install textacy  
conda install psycopg2  
conda install sqlalchemy  
conda install -c conda-forge sqlalchemy-utils
conda install configobj  
```

### Create and populate the database  

Note: If the DB "bcanlp_db" already exists and you want to start afresh, 
first delete it. 

Name of the DB: "bcanlp_db"  

sudo -u postgres dropdb bcanlp_db

To create a db with a user and password:

```shell
sudo -u postgres psql  
postgres=#  
postgres=# create database bcanlp_db;  
CREATE DATABASE  
postgres=# create user bcnlp with password 'bcnlp';  
CREATE ROLE   
postgres=# grant all privileges on database bcanlp_db to bcnlp;  
GRANT  
postgres=# \q  
```

- Login to the db using psql:   
```shell
psql -h localhost -U bcnlp bcanlp_db  
(passwd: bcnlp)
```
To list tables: \dt

- Input directory/file  

Store the files to be processed in a directory, say, "indir"  

### Populating the DB with tables of entities, POS, etc.  

Run the Python script bcnlp_main.py:  

```shell
python bcnlp_main.py --infile < inputfile >   
ex: python bcnlp_main.py --infile indir   
```    
    
- Note:  

When you run bcnlp_main.py, if you see the following Spacy error:  
"Warning: no model found for 'en' Only loading the 'en' tokenizer."  
run the following command in your env:  
```shell
python -m spacy.en.download all 
```
    
- Check if the DB is populated  

psql -h localhost -U bcnlp bcanlp_db  
(passwd: bcnlp)  
    Some useful commands:  
    To list tables: \dt  
    To delete a table: drop table <table_name>  
    To see items in a table: select * from <table_name>  
    
### Run the curses interface and navigate through the menu:  

```shell
python bcnlp_curses.py  
```

You can do the following in this interface:
- List all the documents with number of terms, nouns, verbs and prepositions
- Export lists of entities, POS or each document listed.
- Similarity measures:
    - Get a list of common entities in selected set of 2 documents. A new
      table is created in the database.
    - Get the Similarity measure for two documents (Cosine, Euclidian or Manhattan)

### Run createspan to create the entity spans and bar graphs: 
```shell
python bcnlp_cretespan.py [--bg] --infile <directory>   
ex: python bcnlp_cretespan.py --infile indir  
    python bcnlp_createspan.py --bg --infile mango_cake.txt   
 ```
    
- It will create a file <file>.span for each file in indir.  
- To clear the span, same script is run with --clearspan flag.
- If --bg flag is specified, it will generate a set of bar graphs in the directory 
bgdir.  

## License(s)

The BitCurator logo, BitCurator project documentation, and other non-software products of the BitCurator team are subject to the the Creative Commons Attribution 4.0 Generic license (CC By 4.0).

Unless otherwise indicated, software items in this repository are distributed under the terms of the GNU General Public License, Version 3. See the text file "COPYING" for further details about the terms of this license.

In addition to software produced by the BitCurator team, BitCurator packages and modifies open source software produced by other developers. Licenses and attributions are retained here where applicable.
