![Logo](https://github.com/BitCurator/bitcurator.github.io/blob/master/logos/BitCurator-Basic-400px.png)

# bitcurator-nlp-entspan

[![GitHub issues](https://img.shields.io/github/issues/bitcurator/bitcurator-nlp.svg)](https://github.com/bitcurator/bitcurator-nlp/issues)
[![GitHub forks](https://img.shields.io/github/forks/bitcurator/bitcurator-nlp.svg)](https://github.com/bitcurator/bitcurator-nlp/network)

Entity extraction and span identification for heterogeneous document types. Build instructions and dependencies can be found below. **This project is in development.**

## Installing and running the entity extraction and analysis tools.

The following instructions are tested only in Ubuntu 18.04LTS.

### Make sure the core system is up to date

```shell
sudo apt-get update
sudo apt-get upgrade
```

### Install some basic requirements to build in a Python virtualenv:

```shell
sudo apt-get install virtualenv virtualenvwrapper python3-pip python3-dev
```

### Install postgres and some textract dependencies

You will need the postgres database to store entity and span data produced by the tool. Run the following commands to install postgres, along with some dependencies required for the textract package.

```shell
sudo apt-get install postgresql postgresql-contrib postgresql-server-dev-10
sudo apt-get install libxml2-dev libxslt1-dev antiword unrtf poppler-utils pstotext tesseract-ocr flac ffmpeg lame libmad0 libsox-fmt-mp3 sox libjpeg-dev swig libpulse-dev libasound2-dev
```

### Set up virtualenv and virtualenvwrapper:

You can skip or modify this step (and the remaining virtualenv steps) if your local setup differs or you don't wish to use virtualenvs.

```shell
mkdir ~/.virtualenvs
```

Add the following to the end of your .bashrc file. You may need to verify the location of virtualenvwrapper on your system:

```shell
# Virtualenv and virtualenvwrapper
export WORKON_HOME="$HOME/.virtualenvs"
source /usr/share/virtualenvwrapper/virtualenvwrapper.sh
```

Type ```shell source ~/.bashrc``` or close and reopen the terminal.

### Make a virtualenv for the bitcurator-nlp-entspan tools

```shell
mkvirtualenv -p /usr/bin/python3 entspan
```

### Install textract, textacy, and some other required pip packages.

Note: Installing textacy via pip will also install the latest release of spaCy.

```shell
pip install textract
pip install textacy
pip install psycopg2-binary
pip install sqlalchemy
pip install sqlalchemy-utils
pip install configobj
```

### Create and populate the database  

Note: If the DB "bcnlp_db" already exists and you want to start afresh,
first delete it.

```shell
# drop the db named: "bcnlp_db"  
sudo -u postgres dropdb bcnlp_db
```

To create a db with a user and password:

```shell
sudo -u postgres psql  
postgres=# create database bcnlp_db;  
CREATE DATABASE  
postgres=# create user bcnlp with password 'bcnlp';  
CREATE ROLE   
postgres=# grant all privileges on database bcnlp_db to bcnlp;  
GRANT  
postgres=# \q  
```

### (Optional) Logging in to the db with the psql command:   
```shell
psql -h localhost -U bcnlp bcnlp_db  
(passwd: bcnlp)
```
To list tables: \dt

### Download the spaCy English language model:

```shell
python -m spacy download en
```
- Note:  

If the language model is not downloaded properly, you will see the following Spacy error:  
"Warning: no model found for 'en' Only loading the 'en' tokenizer."
when running bcnlp_main.py.

### Populating the DB with tables of entities, POS, etc.  

Run the Python script bcnlp_main.py:  

```shell
python bcnlp_main.py --infile < inputfile >   
ex: python bcnlp_main.py --infile indir   
```    

Next, check if the DB is populated  

```shell
psql -h localhost -U bcnlp bcnlp_db  
```
Note: the password in this case is "bcnlp"

Some useful commands:  
```shell
To list tables: \dt  
To delete a table: drop table <table_name>  
To see items in a table: select * from <table_name>  
```    

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
python bcnlp_createspan.py [--bg] --infile <directory>   
ex: python bcnlp_createspan.py --infile indir  
    python bcnlp_createspan.py --bg --infile mango_cake.txt   
 ```

- It will create a file <file>.span for each file in indir.  
- To clear the span, same script is run with --clearspan flag.
- If --bg flag is specified, it will generate a set of bar graphs in the directory
bgdir.  

## Deactivating the python virtual environment

To deactivate the environment you're working in, simply type:

```shell
deactivate
```

You can reactivate the virtualenv again by running the activate command again from any terminal.

## Permanently deleting a python virtual environment

To permanently remove the "entspan" environment and all dependencies (not including the Postgres database), run the following:

```shell
rmvirtualenv entspan
```

## License(s)

The BitCurator logo, BitCurator project documentation, and other non-software products of the BitCurator team are subject to the the Creative Commons Attribution 4.0 Generic license (CC By 4.0).

Unless otherwise indicated, software items in this repository are distributed under the terms of the GNU General Public License, Version 3. See the text file "COPYING" for further details about the terms of this license.

In addition to software produced by the BitCurator team, BitCurator packages and modifies open source software produced by other developers. Licenses and attributions are retained here where applicable.
