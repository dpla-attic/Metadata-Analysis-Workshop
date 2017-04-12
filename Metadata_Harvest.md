# Metadata Harvests, DPLAFest 2017 DPLA Metadata Analysis Workshop

*2:15-2:45  PM, led by [Christina Harlow, DataOps at Stanford University Libraries](mailto:cmharlow@gmail.com)*

## Goals

- Understand structure of Metadata Harvest & Metadata Breakers scripts
- Walk through the OAI-PMH Metadata Harvest Python script
- Generate some Metadata Harvests
- Use new Bash skills to explore those Harvests/data dumps

## Requirements

- Bash Shell
- Python >2.7.x OR 3.x.x
- Copy of the [Workshop GitHub Repository](https://github.com/dpla/Metadata-Analysis-Workshop) on your Laptop (Grab via Git or ask the Workshop leader for a thumb-drive with the files)

## Lesson

**Table of Contents**

* [Overview of Metadata Harvest & Metadata Breakers]()
* [Harvest Step 1: Check Python, Pip, VirtualEnv]()
* [Harvest Step 2: Activate the Python Virtual Environment & Install Script Requirements]()
* [Harvest Step 3: Review of the Metadata Harvest Script]()
* [Harvest Step 4: Harvest Data from an OAI-PMH Feed]()
* [Harvest Step 5: Take a Peak at the Harvested Metadata]()
* [Harvest Step 6: Planning Harvests, Data Dump Management, & Non-OAI Harvest Options]()

### Overview

The Metadata Harvest and Analysis Python scripts explored in the rest of this workshop run by first harvesting a full metadata dump of any OAI-PMH feed data from the OAI-PMH endpoint. This metadata harvest is stored locally. Then, the analyses occur by running the analysis scripts (with some options available for type of analysis) on that data dump. This generates reports across all collections or subsets as explicitly set in the harvest and analysis options.

This requires that the metadata be exposed via an OAI-PMH feed (other APIs, Solr Indices, and data publication methods are supported experimentally - we will touch briefly on other options later) through which the metadata is exposed. Luckily for this DPLA workshop, that's already a common occurrence for DPLA Provider Data (thanks, Repox).

*Nota bene*: To speed up this process, I'm working on a hosted version of these analysis scripts that copies the data dumps to a database that is then queried (in place of pulling full XML data onto your local system). This is still in development. If this ends up being something you would like to see happen or could support, please ping me.

### Step 1: Check Python, Pip, VirtualEnv Installation/Versions

For these harvest and analysis Python scripts to run, we need to check that Python is installed and verify the version being used. This should have occurred before the workshop, but we're going to run through this quickly now.

In your shell used for the previous lesson, type the command `python --version`:

```bash
$ python --version
  Python 2.7.13
```

You're strongly recommended to proceed with Python 2.7 or greater. These scripts have recently been refactored to also work with Python 3 (3.6), but this is relatively new work.

Python 2.7.9 or greater (or Python 3) comes installed with Pip, a package manager for Python. To check Pip, type the command `pip --version`:

```bash
$ pip --version
  pip 9.0.1 from /usr/local/lib/python2.7/site-packages (python 2.7)
```

You'll need Python and Pip. If you don't have either pip, please go to this [Workshop Repository's Main Page](README.md) and use the links there to perform an installation quickly - or partner up with someone near you who has Python and Pip installed.

Next, in our shell, we want to change into our directory where these repository materials are stored. You should have downloaded and navigated through this in the last lesson. **If not**:

Clone this repository (https://github.com/cul-it/sharedshelf-metadata.git) where you would like to keep it (for example, I keep it in a directory called 'Projects'). Note: this requires **the recursive flag** to get all the materials (if you didn't install Git, just ask the workshop leader for the a thumb-drive with the materials):

```bash
$ git clone --recursive https://github.com/dpla/Metadata-Analysis-Workshop.git
  Cloning into 'Metadata-Analysis-Workshop'...
  remote: Counting objects: 78, done.
  remote: Compressing objects: 100% (59/59), done.
  remote: Total 78 (delta 30), reused 56 (delta 15), pack-reused 0
  Unpacking objects: 100% (78/78), done.
  Submodule 'metadataQA' (https://github.com/cmh2166/metadataQA) registered for path 'metadataQA'
  Cloning into '/Users/Christina/Metadata-Analysis-Workshop/metadataQA'...
  Submodule path 'metadataQA': checked out '0e44b393bc11e00d8a3e24959be45e3f229e90c4'
```

With the workshop materials repository on your local machine (you'll need to remember where you downloaded or cloned this directory), let's change into that directory and make sure we are in the right spot using the `cd`, `pwd`, and `ls` commands we just learned:

```bash
$ cd Metadata-Analysis-Workshop/
$ pwd
  /Users/Christina/Metadata-Analysis-Workshop
$ ls .
  Bash.md			Metadata_Harvest.md	harvested-metadata	metadata-python
  Metadata_Breakers.md	README.md		images			metadataQA
```

Python virtual environments enable you to isolate the Python options (version used, libraries used, version of libraries used) to a particular working area or project. This is helpful if you don't want to change any global settings on your computer - which often, you don't.

Now we are going to create a virtualenv with the Python version you're working with (*if you didn't install virtualenv, then skip this step*). Type the command `virtualenv venv`:

```bash
$ virtualenv venv
```
    *Special Note:* If you want to create a virtualenv specific a particular Python version that is not the default, use the following command instead, where '/usr/local/bin/python' points to the response of 'which python' or 'which python3', etc.:

    ```bash
    $ virtualenv -p /usr/local/bin/python venv
    ```

The response of the `virtualenv venv` should look like this:

```bash
$ virtualenv venv
  New python executable in /Users/Christina/Metadata-Analysis-Workshop/venv/bin/python2.7
  Also creating executable in /Users/Christina/Metadata-Analysis-Workshop/venv/bin/python
  Installing setuptools, pip, wheel...done.
```

You should only have to do the above once (you're done for the rest of this workshop), whereas the following should be redone whenever you run the scripts or need to capture updates.

### Step 2: Activate the Python Virtual Environment & Install Script Requirements

Each time you want to run the scripts, you'll want to first activate/start up your virtual environment (if using).

In the same location where you ran the above comments, type and run the virtualenv activation `source venv/bin/activate`:

```bash
$ source venv/bin/activate
  (venv) bash-3.2$
```

Depending on your shell, you will see something indicating your working in the 'venv' virtual environment now, namely, the name of the virtualenv you created in parentheses before the bash command start:

```bash
(venv) $
```

We now want to install all the Python scripts library requirements to this virtual environment. _Note: you should only ever have to run this command once when you start and, after that, only when there are updates to the scripts._ Type `pip install -r metadataQA/requirements.txt` (use tab to autocomplete filenames & confirm you're in the right repository!):

```bash
(venv)$ pip install -r metadataQA/requirements.txt
        Collecting lxml==3.4.4 (from -r metadataQA/requirements.txt (line 1))
        Collecting objectpath==0.5 (from -r metadataQA/requirements.txt (line 2))
          Using cached objectpath-0.5-py2.py3-none-any.whl
        Collecting pymarc==3.0.4 (from -r metadataQA/requirements.txt (line 3))
        Collecting pytz==2015.6 (from -r metadataQA/requirements.txt (line 4))
          Using cached pytz-2015.6-py2.py3-none-any.whl
        Collecting requests==2.8.1 (from -r metadataQA/requirements.txt (line 5))
          Using cached requests-2.8.1-py2.py3-none-any.whl
        Requirement already satisfied: six==1.10.0 in ./venv/lib/python2.7/site-packages (from -r metadataQA/requirements.txt (line 6))
        Collecting wheel==0.24.0 (from -r metadataQA/requirements.txt (line 7))
          Using cached wheel-0.24.0-py2.py3-none-any.whl
        Installing collected packages: lxml, objectpath, pymarc, pytz, requests, wheel
          Found existing installation: wheel 0.29.0
           Uninstalling wheel-0.29.0:
              Successfully uninstalled wheel-0.29.0
        Successfully installed lxml-3.4.4 objectpath-0.5 pymarc-3.0.4 pytz-2015.6 requests-2.8.1 wheel-0.24.0
```
The response will tell you either if something was installed or if it was already installed.

### Step 3: Review of the Metadata Harvest Script

With your virtual environment running, and your Python Harvest script dependencies installed, you're ready to go. Let's take a look first at the Python script we will be running before jumping in - but do not worry, we're not here to learn Python, just take a peek at the tool we will be using.

*If you want to look on your screen, use `open metadataQA/harvest/harvestOAI.py`. This will open the file in your laptop's default text editor. Alternatively, you can look at [the file in GitHub in your web browser](http://www.github.com/dpla/Metadata-Analysis-Workshop/metadataQA/harvest).*

*metadataQA/harvest/harvestOAI.py*
```python
"""Harvest Metadata from an OAI-PMH Feed."""
from __future__ import unicode_literals
import requests
import zlib
import time
import re
import xml.dom.pulldom
import xml.dom.minidom
import codecs
from argparse import ArgumentParser
from builtins import chr

nDataBytes = 0
nRawBytes = 0
oaistart = """<?xml version="1.0" encoding="UTF-8"?><OAI-PMH xmlns="http://www.openarchives.org/OAI/2.0/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://www.openarchives.org/OAI/2.0/ http://www.openarchives.org/OAI/2.0/OAI-PMH.xsd"> <responseDate>2015-10-11T00:35:52Z</responseDate> <ListRecords>\n"""
oaiend = """\n</ListRecords></OAI-PMH>\n"""


def getFile(link, command, sleepTime=0):
    """This generates the OAI-PMH link and retrieves the XML data over HTTP."""
    time.sleep(sleepTime)

    # Set URL with OAI-PMH Command for Retrieval
    remoteAddr = link + '?verb=%s' % command
    print("\t getFile ... %s" % remoteAddr[-90:])

    # Handle HTTP Response (Including Common Errors) from OAI-PMH Endpoint
    try:
        resp = requests.get(remoteAddr)
        if resp.status_code != 200 and resp.status_code != 301:
            resp.raise_for_status()
        elif resp.status_code == 301:
            print("%s redirected to %s ." % remoteAddr, resp.url)
            return(getFile(resp.url, command))
        elif '/xml' not in resp.headers.get('content-type'):
            print("ERROR: content-type=%s" % (resp.headers.get('content-type')))
            exit()
        else:
            remoteData = resp.text
    except requests.HTTPError as exValue:
        status_code = exValue.response.status_code
        if status_code == 503:
            retryWait = int(resp.headers.get("Retry-After", "-1"))
            if retryWait < 0:
                print("OAI-PMH Service %s Unavailable (Status 503)." % link)
                exit()
            else:
                print('Waiting %d seconds' % retryWait)
                return(getFile(link, command, retryWait))
        elif status_code == 404:
            print("404 Not Found Error with OAI-PMH URL: %s" % remoteAddr)
            exit()
        else:
            print(exValue)
            exit()
    return(remoteData.encode('utf8'))


def zipRemoteData(remoteData):
    # Count Bytes for Output Report, Compress Where Able for Efficiency.
    global nRawBytes, nDataBytes
    nRawBytes += len(remoteData)
    try:
        remoteData = zlib.decompressobj().decompress(remoteData)
    except:
        pass
    nDataBytes += len(remoteData)
    return(remoteData)


def checkOAIErrors(remoteData):
    # Check for OAI-PMH Errors in the XML Response
    oaiErr = re.search(b'<error *code=\"([^"]*)">(.*)</error>', remoteData)
    if oaiErr:
        print("OAIERROR: code=%s '%s'" % (oaiErr.group(1), oaiErr.group(2)))
        exit()
    else:
        return(remoteData)


def generateOAIopts(args, verbOpts=''):
    if args.setName:
        verbOpts += '&set=%s' % args.setName
    if args.fromDate:
        verbOpts += '&from=%s' % args.fromDate
    if args.until:
        verbOpts += '&until=%s' % args.until
    if args.mdprefix:
        verbOpts += '&metadataPrefix=%s' % args.mdprefix
    return(verbOpts)


def handleEncodingErrors(inputFile):
    # Handle OAI-PMH XML Encoding Errors
    # from http://boodebr.org/main/python/all-about-python-and-unicode#UNI_XML
    RE_XML_IL = u'([\u0000-\u0008\u000b-\u000c\u000e-\u001f\ufffe-\uffff])' + \
                u'|' + \
                u'([%s-%s][^%s-%s])|([^%s-%s][%s-%s])|([%s-%s]$)|(^[%s-%s])' %\
                (chr(0xd800), chr(0xdbff), chr(0xdc00),
                 chr(0xdfff), chr(0xd800), chr(0xdbff),
                 chr(0xdc00), chr(0xdfff), chr(0xd800),
                 chr(0xdbff), chr(0xdc00), chr(0xdfff))
    outputFile = re.sub(RE_XML_IL, u"?", inputFile.decode('utf-8'))
    return(outputFile)


def writeHarvest(link, data, ofile):
    recordCount = 0
    while data:
        # I need a better way to handle python 2/3 interop here, but not finding it.
        try:
            events = xml.dom.pulldom.parseString(data.encode('utf-8'))
            for (event, node) in events:
                if event == "START_ELEMENT" and node.tagName == 'record':
                    events.expandNode(node)
                    node.writexml(ofile)
                    recordCount += 1
        except TypeError as e:
            events = xml.dom.pulldom.parseString(data)
            for (event, node) in events:
                if event == "START_ELEMENT" and node.tagName == 'record':
                    events.expandNode(node)
                    node.writexml(ofile)
                    recordCount += 1
        more = re.search('<resumptionToken[^>]*>(.*)</resumptionToken>', data)
        if not more:
            break
        else:
            data = getFile(link, "ListRecords&resumptionToken=%s" % more.group(1))
            data = handleEncodingErrors(data)
    return(recordCount)


def main():
    parser = ArgumentParser()
    parser.add_argument("-l", "--link", dest="link", help="OAI-PMH URL",
                        default="https://ecommons.cornell.edu/dspace-oai/request")
    parser.add_argument("-o", "--filename", dest="fname",
                        help="write repository to file", default="harvest.xml")
    parser.add_argument("-f", "--from", dest="fromDate",
                        help="harvest records from this date YYYY-MM-DD")
    parser.add_argument("-u", "--until", dest="until",
                        help="harvest records until this date YYYY-MM-DD")
    parser.add_argument("-m", "--mdprefix", dest="mdprefix", default="oai_dc",
                        help="use the specified metadata format")
    parser.add_argument("-s", "--setName", dest="setName",
                        help="harvest the specified OAI-PMH set")
    args = parser.parse_args()

    # Check OAI-PMH URL is valid
    if not args.link.startswith('http'):
        args.link = 'http://' + args.link

    # Start Harvest Process
    print("Writing records to %s from repository %s" % (args.fname, args.link))

    # Generate the OAI-PMH URL with Provided Arguments
    verbOpts = generateOAIopts(args)
    print("Using url:%s" % args.link + '?verb=ListRecords' + verbOpts)

    # Create Start of XML Output File
    ofile = codecs.lookup('utf-8')[-1](open(args.fname, 'wb'))
    ofile.write(oaistart)

    # Grab & Clean XML Records from OAI Feed
    remoteData = getFile(args.link, 'ListRecords' + verbOpts)
    data = zipRemoteData(remoteData)
    data = checkOAIErrors(data)
    dataClean = handleEncodingErrors(data)

    # Iterate over Records, ResumptionTokens, & Write to File
    recordCount = writeHarvest(args.link, dataClean, ofile)

    # Finish Harvest Writer
    ofile.write(oaiend)
    ofile.close()

    # Print Simple Reports from Harvest
    print("\nRead %d bytes (%.2f compression)" % (nDataBytes, float(nDataBytes) / nRawBytes))
    print("Wrote out %d records" % recordCount)


if __name__ == "__main__":
    main()

```

We will start at the bottom and go through the methods here to understand what this scripts is up to. Basically, we take your base OAI-PMH URL, pull together the indicate variables - what metadata prefix and set (if wanted) to pull from, a start or end date to pull from - then create a full OAI-PMH URL. We use a Python library to issue a HTTP request on that URL and capture the XML response, which we then write to the output file. Then we check for an OAI-PMH resumptionToken, and repeat this process as needed.

What we end up with is a copy of the OAI-PMH XML across resumptionTokens saved as a file on our local machine. 

### Step 4: Harvest Data from an OAI-PMH Feed

You only need to run the Metadata Harvest Python script as often as you want the most recent data. It can take a while and can take up considerable space on your local machine, so consider re-writing the same file with the most recent data (instead of saving multiple data dumps).

Let's first create a `test` directory to store your data to (so it doesn't clash with the sample data captured and doesn't interfere with the GitHub repository management):

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

### Step 5: Take a Peak at the Harvested Metadata



### Step 6: Planning Harvests, Data Dump Management, & Non-OAI Harvest Options