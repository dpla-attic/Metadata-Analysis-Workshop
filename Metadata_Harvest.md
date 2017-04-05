# Metadata Harvests, DPLAFest 2017 DPLA Metadata Analysis Workshop

*2:15-2:45  PM, led by [Christina Harlow, DataOps at Stanford University Libraries](mailto:cmharlow@gmail.com)*

## Goals

- Understand structure of Metadata Harvest & Metadata Breakers scripts
- Walk through the OAI-PMH Metadata Harvest Python script
- Generate some Metadata Harvests
- Use new Bash skills to explore those Harvests/data dumps

**Table of Contents**

* Overview of Metadata Harvest & Metadata Breakers

### Overview

Basically, the harvest and analysis Python scripts explored in the rest of this workshop run by first harvesting a full metadata dump of any OAI-PMH feed data, across collections, from the OAI-PMH endpoint. This metadata harvest is stored locally. Then, the analyses occur by running the analysis scripts (with some options available for type of analysis) on that data dump. This generates reports across all collections unless explicitly set in the analysis options.

This requires that the metadata be exposed via an OAI-PMH feed (other APIs, Solr Indices, and data publication methods are supported experimentally - ask Christina later about this) through which the metadata is exposed. Luckily for this DPLA workshop, that's already a requirement for involvement in DPLA.

To speed up this process, we're working on a hosted version of these analysis scripts that copies the data dumps to a database that is then queried (in place of pulling full json data onto your local system). This is still in development.

### Step 1: Check Python, Pip, VirtualEnv Installation/Versions

Check Python is installed and what version(s) are available.

```bash
$ python --version
```
Should return something like:

```bash
$ Python 2.7.12
```

Clone this repository (https://github.com/cul-it/sharedshelf-metadata.git) where you would like to keep it (for example, I keep it in a directory called 'Projects'), then in your shell / command line tool, change into the directory for this repository, then create a virtualenv with the Python version you prefer:

```bash
$ git clone https://github.com/cul-it/sharedshelf-metadata.git
 ( output should show materials being copied/cloned to your local computer )
$ cd ~/Projects/sharedshelf-metadata
$ virtualenv venv
```
If you want to specific a particular Python version that is not the default, use the following command instead:

```bash
$ cd ~/Projects/sharedshelf-metadata
$ virtualenv -p /usr/local/bin/python venv
```
Where '/usr/local/bin/python' points to the response of 'which python' or 'which python3', etc.

The response of the above should look like this:

```bash
$ virtualenv venv
Using base prefix '/usr/local/Cellar/python3/3.5.1/Frameworks/Python.framework/Versions/3.5'
New python executable in venv/bin/python3.5
Also creating executable in venv/bin/python
Installing setuptools, pip, wheel...done.
```

You should only have to do the above once, whereas the follow should be redone when you run the scripts or need to capture updates.

### Activate the Python Virtual Environment & Install Script Requirements

Python virtual environments enable you to isolate the Python options (version used, libraries used, version of libraries used) to a particular working area or project. This is helpful if you don't want to change any global settings on your computer - which often, you don't.

Each time you want to run the scripts, you'll want to first activate/start up your virtual environment. Within the top level of the sharedshelf-metadata directory on your computer, run the following:

```bash
$ source venv/bin/activate
```
Depending on your shell / command line client, you will see something indicating your working in the 'venv' virtual environment now:

```bash
(venv) $
```

Install all the Python scripts library requirements to this virtual environment. Note: you should only ever have to run this command once when you start and then whenever there are updates to the scripts:

```bash
(venv) $ pip install -r requirements.txt
Requirement already satisfied: requests==2.12.5 in ./venv/lib/python3.5/site-packages (from -r requirements.txt (line 1))
```
The response will tell you either if something was installed or if it was already installed.

### Harvest the data from the SharedShelf API

With your virtual environment running, you're ready to go.

You only need to run the harvest script as often as you want the most recent data. It can take a while and can take up considerable space on your local machine, so consider re-writing the same file with the most recent data (instead of saving multiple data dumps).

From the top-level of the sharedshelf-metadata directory on your computer, run:

```bash
(venv) $ python scripts/artstorharvest.py -e cmh329@cornell.edu -p yourPassword -o data/output.json
```

Fill this is with your email, your SharedShelf password, and the place where you'd like to store the data dump locally (here, it is "data/output.json"). The response should be like the following:

```bash
(venv) $ python scripts/artstorharvest.py -e cmh329@cornell.edu -p yourPassword -o data/output.json
Writing records to data/output.json from SharedShelf.
Retrieving project Obama Visual Iconography
Retrieving project Political Americana
Retrieving project Vicos
Retrieving project Billie Jean Isbell
...
```

What this script does is first query for all the unique collections in our SharedShelf instance, then iterate over each collection to grab the data. The data grabbed for each collection describes each digital object therein and, where possible, matches the collection-specific metadata field code to the metadata field text name. Where there isn't a text name for the metadata field in the collection, the field code is returned.

This can take up to 10 minutes to run. Wait until it is complete before moving to analysis.