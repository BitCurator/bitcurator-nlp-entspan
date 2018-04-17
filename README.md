# bitcurator-nlp-entspan

Preliminary code samples for entity extraction and span identification. Build instructions and dependencies can be found in BUILD-NOTES.md. This project is in development.

## Documentation

The latest documentation can be found on the BitCurator Access wiki page at https://wiki.bitcurator.net/index.php?title=BitCurator_NLP.

## License(s)

The BitCurator logo, BitCurator project documentation, and other non-software products of the BitCurator team are subject to the the Creative Commons Attribution 4.0 Generic license (CC By 4.0).

Unless otherwise indicated, software items in this repository are distributed under the terms of the GNU General Public License, Version 3. See the text file "COPYING" for further details about the terms of this license.

In addition to software produced by the BitCurator team, BitCurator packages and modifies open source software produced by other developers. Licenses and attributions are retained here where applicable.

# Steps to install and run Entspan scripts.

#Create Conda virtual environment
- Install Conga from
https://www.anaconda.com/download/#linux

(Conda cheatsheet: https://conda.io/docs/_downloads/conda-cheatsheet.pdf)

- Install conda-forge channel (It has most of the packages we need)  
conda install --channel conda-forge  

- Create Conda virtual environment:  
#conda create --name < envname > python=2.7  
conda create --name entspan python=2.7  

- List the virtual envs created:  
conda info --envs  

- To get into conda virtual environment, do:  
#source activate < name >  
source activate entspan  

- Install the required packages:    

conda install spacy  
conda install textract  
conda install textacy  
conda install psycopg2  
conda install sqlalchemy  
conda install sqlalchemy_utils  
conda install configobj  

- Create the DB  

Name of the DB: "bcanlp_db"  
To create a db with a user and password:  
sudo -u postgres psql  
postgres=#  
postgres=# create database bcanlp_db;  
CREATE DATABASE  
postgres=# create user bcnlp with password 'bcnlp';  
CREATE ROLE   
postgres=# grant all privileges on database bcanlp_db to bcnlp;  
GRANT  
postgres=# \q  

- Login to the db using psql:   

psql -h localhost -U bcnlp bcanlp_db  
(passwd: bcnlp)

To list tables: \dt

- Input directory/file  

Store the files to be processed in a directory, say, "indir"  

- Populating the DB with tables of entities, POS, etc.  

Run the Python script bcnlp_main.py:  

python bcnlp_main.py --infile < inputfile >   
ex: python bcnlp_main.py --infile indir   
    
- Note:  

When you run bcnlp_main.py, if you see the following Spacy error:  
"Warning: no model found for 'en' Only loading the 'en' tokenizer."  
run the following command in your env:  
python -m spacy.en.download all 
    
- Check if the DB is populated  

psql -h localhost -U bcnlp bcanlp_db  
(passwd: bcnlp)  
    Some useful commands:  
    To list tables: \dt  
    To delete a table: drop table <table_name>  
    To see items in a table: select * from <table_name>  
    
- Run the curses interface and navigate through the menu:  

python bcnlp_curses.py  

- Run createspan to create the entity spans: 

python bcnlp_cretespan.py [--bg] --infile <directory>   
ex: python bcnlp_cretespan.py --infile indir  
    python bcnlp_createspan.py --bg --infile mango_cake.txt   
    
It will create a file <file>.span for each file in indir.  
    If --bg flag is specified, it will generate a set of bar graphs in the directory bgdir.  

