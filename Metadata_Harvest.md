# Metadata Harvests, DPLAFest 2017 DPLA Metadata Analysis Workshop

*2:15-2:45  PM, led by [Christina Harlow, DataOps at Stanford University Libraries](mailto:cmharlow@gmail.com)*

## Goals

- Understand structure of Metadata Harvest & Metadata Breakers scripts
- Walk through the OAI-PMH Metadata Harvest Python script
- Generate some Metadata Harvests
- Use new Bash skills to explore those Harvests/data dumps

## Requirements

- Bash Shell
- Python >=2.7.x OR >=3.5.x
- Copy of the [Workshop GitHub Repository](https://github.com/dpla/Metadata-Analysis-Workshop) on your Laptop (Grab via Git or ask the Workshop leader for a thumb-drive with the files)

## Lesson

**Table of Contents**

* [Overview of Metadata Harvest & Metadata Breakers](#overview)
* [Harvest Step 1: Check Python, Pip, VirtualEnv](#step-1-check-python-pip-virtualenv-installationversions)
* [Harvest Step 2: Activate the Python Virtual Environment & Install Script Requirements](#step-2-activate-the-python-virtual-environment--install-script-requirements)
* [Harvest Step 3: Review of the Metadata Harvest Script](#step-3-review-of-the-metadata-harvest-script)
* [Harvest Step 4: Harvest Data from an OAI-PMH Feed](#step-4-harvest-data-from-an-oai-pmh-feed)
* [Harvest Step 5: Take a Peek at the Harvested Metadata](#step-5-take-a-peek-at-the-harvested-metadata)
* [Harvest Step 6: Planning Harvests, Data Dump Management, & Non-OAI Harvest Options](#step-6-planning-harvests-data-dump-management--non-oai-harvest-options)

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
(venv)$
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

Nothing in this script - or the Analysis script - is particularly difficult, but you'll see that they offer us just the little bit of duct tape we need to do some metadata analysis we can't generally do in our repositories.

### Step 4: Harvest Data from an OAI-PMH Feed

You only need to run the Metadata Harvest Python script as often as you need the most recent data. Be aware: this script can take a while to complete, and it can take up considerable space on your local machine to store these data dumps, so consider re-writing the same file with the most recent data (instead of saving multiple data dumps). What does that look like? Let's jump into that now.

Let's first create a `test` directory to store your data to (so it doesn't clash with the sample data captured and doesn't interfere with the GitHub repository management):

```bash
(venv)$ pwd
      /Users/Christina/Projects/Metadata-Analysis-Workshop/
(venv)$ mkdir test
(venv)$ ls
      Bash.md			README.md		metadataQA
      Metadata_Breakers.md	harvested-metadata	test
      Metadata_Harvest.md	images
```

Now we are going to use the harvestOAI.py script we just reviewed to pull the following set from an OAI-PMH feed provider (remember to use the _tab autocomplete_ bash trick here to lessen your typing!). First, let's use the help switch or flag to see what the script requests:

```bash
(venv)$ python metadataQA/harvest/harvestOAI.py --help
      usage: harvestOAI.py [-h] [-l LINK] [-o FNAME] [-f FROMDATE] [-u UNTIL]
                     [-m MDPREFIX] [-s SETNAME]

      optional arguments:
        -h, --help            show this help message and exit
        -l LINK, --link LINK  OAI-PMH URL
        -o FNAME, --filename FNAME write repository to file
        -f FROMDATE, --from FROMDATE harvest records from this date YYYY-MM-DD
        -u UNTIL, --until UNTIL harvest records until this date YYYY-MM-DD
        -m MDPREFIX, --mdprefix MDPREFIX use the specified metadata format
        -s SETNAME, --setName SETNAME harvest the specified OAI-PMH set
```

Using this, we can construct a OAI-PMH Harvest that grabs the Qualified Dublin Core (`oai_qdc`) feed for the Jack Bradley Photojournalism Collection from Bradley University (`bra_jack`) and saves it to `test/bra_jack.oai.qdc.xml`:

```bash
(venv)$ python metadataQA/harvest/harvestOAI.py -l 'http://collections.carli.illinois.edu/oai/oai.php' -m 'oai_qdc' -s 'bra_jack' -o 'test/bra_jack.oai.qdc.xml'
      Writing records to test/bra_jack.oai.qdc.xml from repository http://collections.carli.illinois.edu/oai/oai.php
      Using url:http://collections.carli.illinois.edu/oai/oai.php?verb=ListRecords&set=bra_jack&metadataPrefix=oai_qdc
      	 getFile ... ctions.carli.illinois.edu/oai/oai.php?verb=ListRecords&set=bra_jack&metadataPrefix=oai_qdc
      	 getFile ... i.php?verb=ListRecords&resumptionToken=bra_jack:200:bra_jack:0000-00-00:9999-99-99:oai_qdc
      	 getFile ... i.php?verb=ListRecords&resumptionToken=bra_jack:400:bra_jack:0000-00-00:9999-99-99:oai_qdc
      	 getFile ... i.php?verb=ListRecords&resumptionToken=bra_jack:601:bra_jack:0000-00-00:9999-99-99:oai_qdc
      	 getFile ... i.php?verb=ListRecords&resumptionToken=bra_jack:801:bra_jack:0000-00-00:9999-99-99:oai_qdc
      	 getFile ... .php?verb=ListRecords&resumptionToken=bra_jack:1001:bra_jack:0000-00-00:9999-99-99:oai_qdc
      	 getFile ... .php?verb=ListRecords&resumptionToken=bra_jack:1201:bra_jack:0000-00-00:9999-99-99:oai_qdc
      	 getFile ... .php?verb=ListRecords&resumptionToken=bra_jack:1401:bra_jack:0000-00-00:9999-99-99:oai_qdc
      	 getFile ... .php?verb=ListRecords&resumptionToken=bra_jack:1601:bra_jack:0000-00-00:9999-99-99:oai_qdc
      	 getFile ... .php?verb=ListRecords&resumptionToken=bra_jack:1801:bra_jack:0000-00-00:9999-99-99:oai_qdc
      	 getFile ... .php?verb=ListRecords&resumptionToken=bra_jack:2001:bra_jack:0000-00-00:9999-99-99:oai_qdc
      	 getFile ... .php?verb=ListRecords&resumptionToken=bra_jack:2201:bra_jack:0000-00-00:9999-99-99:oai_qdc

      Read 397343 bytes (1.00 compression)
      Wrote out 2223 records
```

This may take a few minutes. Depending on our internet connection and the size of the dataset, this script can take up to 5 minutes to run. But you need to wait until this is complete before moving to Analysis on that harvested dataset.

#### Independent Exercise:

If you got through the above relatively quickly, try harvesting another OAI-PMH dataset to your local computer - you can use one of the datasets specified in our [harvested-metadata README.md](harvested-metadata/README.md) or your own OAI-PMH feed. Remember to save the -o or output to your `test` directory.

### Step 5: Take a Peek at the Harvested Metadata

At this step, we should all have some harvest OAI-PMH Metadata on our local computers - either through using the Harvest python script above or, if network connectivity isn't what it should be, through havign a copy of this Workshop's Repository on your computer (check out the [harvested-metadata](harvested-metadata/) subdirectory for pre-generated OAI-PMH data dumps.)

Depending on time, we can use our brand new bash skills to take a peek at these files. They will be too large to open in most traditional editors or GUI (Graphical User Interface) tools, but bash can help with thisself.

For example, the `head` command can let us see a certain number of lines of the file:

```bash
(venv)$ head -10 harvested-metadata/springfield.oai.qdc.xml
      <?xml version="1.0" encoding="UTF-8"?><OAI-PMH xmlns="http://www.openarchives.org/OAI/2.0/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://www.openarchives.org/OAI/2.0/ http://www.openarchives.org/OAI/2.0/OAI-PMH.xsd"> <responseDate>2015-10-11T00:35:52Z</responseDate> <ListRecords>
      <record><header status="deleted"><identifier>oai:cdm16122.contentdm.oclc.org:p15370coll2/0</identifier><datestamp>2012-02-08</datestamp><setSpec>p15370coll2</setSpec></header>
      </record><record><header><identifier>oai:cdm16122.contentdm.oclc.org:p15370coll2/1</identifier><datestamp>2014-01-08</datestamp><setSpec>p15370coll2</setSpec></header>
      <metadata>
      <oai_qdc:qualifieddc xmlns:dc="http://purl.org/dc/elements/1.1/" xmlns:dcterms="http://purl.org/dc/terms/" xmlns:oai_qdc="http://worldcat.org/xmlschemas/qdc-1.0/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://worldcat.org/xmlschemas/qdc-1.0/ http://worldcat.org/xmlschemas/qdc/1.0/qdc-1.0.xsd http://purl.org/net/oclcterms http://worldcat.org/xmlschemas/oclcterms/1.4/oclcterms-1.4.xsd">
      <dc:title>Amos Alonzo Stagg, ca. 1891</dc:title>
      <dc:description>A photograph of Amos Alonzo Stagg, ca. 1891, while a student at the International Young Men's Christian Association Training School, now known as Springfield College. Stagg graduated  in 1891  and  served as an assistant physical education instructor at Springfield College from 1890-1892. He started the football program at Springfield College and played in one of the first public basketball game, being the only faculty member to score a &quot;basket ball goal&quot; in their 5 to 1 loss to the students. His football teams at Springfield College were known as &quot;Stagg's Eleven&quot; or the &quot;Stubby Christians&quot;. During the two years he coached and played football at Springfield College, the &quot;Stubby Christians&quot; went 10-11-1 and played in one of the first indoor football games in Madison Square Garden against the Yale Consolidated team on December 12, 1890. In a career spanning more than 50 years, Stagg came to be known as the &quot;Grand Old Man of Football&quot;. He coached football at the University of Chicago (Chicago, Ill.) from 1892-1932 and at the College of  the Pacific from 1933 until his retirement in 1946. Over his career he won 314 games. Amos Alonzo Stagg died in 1965 at the age of 102.</dc:description>
      <dc:subject>Springfield College--Students; Springfield College--Alumni and alumnae; International Young Men's Christian Association Training School (Springfield, Mass.); Springfield College;</dc:subject>
      <dc:subject>Stagg, Amos Alonzo, 1862-1965; Football; Coaching;</dc:subject>
      <dc:publisher>Springfield College</dc:publisher>
```

Alternatively, `tail` can let us see the last few lines of a file:

```bash
(venv)$ tail -10 harvested-metadata/springfield.oai.qdc.xml
      <dcterms:extent>22 Pages</dcterms:extent>
      <dc:language>en-US</dc:language>
      <dc:relation>The Springfield Student</dc:relation>
      <dc:relation>52</dc:relation>
      <dc:relation>16</dc:relation>
      <dc:rights>Text and images are owned, held, or licensed by Springfield College and are available for personal, non-commercial, and educational use, provided that ownership is properly cited. A credit line is required and should read: Courtesy of Springfield College, Babson Library, Archives and Special Collections. Any commercial use without written permission from Springfield College is strictly prohibited. Other individuals or entities other than, and in addition to, Springfield College may also own copyrights and other propriety rights. The publishing, exhibiting, or broadcasting party assumes all responsibility for clearing reproduction rights and for any infringement of United States copyright law.</dc:rights>
      <dc:identifier>http://cdm16122.contentdm.oclc.org/cdm/ref/collection/p16122coll6/id/10117</dc:identifier></oai_qdc:qualifieddc>
      </metadata>
      </record>
      </ListRecords></OAI-PMH>
```

`cat` opens up the entirety of the file for review in your shell, but with files of this size, this tends to be less helpful (you can always type `Ctrl+C` to exit from this screen):

```bash
(venv)$ cat harvested-metadata/springfield.oai.qdc.xml
... (XML zooming past)
```
(type `Ctrl+C` to exit from this screen if you're stuck)

Finally (for this workshop), there is the bash command `less`, which allows you to page through the results of a file in a shell editor (use your arrow keys or your mouse to scroll, and hit 'q' to exit):

```bash
(venv)$ less harvested-metadata/springfield.oai.qdc.xml
    <?xml version="1.0" encoding="UTF-8"?><OAI-PMH xmlns="http://www.openarchives.org/OAI/2.0/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://www.openarchives.org/OAI/2.0/ http://www.openarchives.org/OAI/2.0/OAI-PMH.xsd"> <responseDate>2015-10-11T00:35:52Z</responseDate> <ListRecords>
    <record><header status="deleted"><identifier>oai:cdm16122.contentdm.oclc.org:p15370coll2/0</identifier><datestamp>2012-02-08</datestamp><setSpec>p15370coll2</setSpec></header>
    </record><record><header><identifier>oai:cdm16122.contentdm.oclc.org:p15370coll2/1</identifier><datestamp>2014-01-08</datestamp><setSpec>p15370coll2</setSpec></header>
    <metadata>
    <oai_qdc:qualifieddc xmlns:dc="http://purl.org/dc/elements/1.1/" xmlns:dcterms="http://purl.org/dc/terms/" xmlns:oai_qdc="http://worldcat.org/xmlschemas/qdc-1.0/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://worldcat.org/xmlschemas/qdc-1.0/ http://worldcat.org/xmlschemas/qdc/1.0/qdc-1.0.xsd http://purl.org/net/oclcterms http://worldcat.org/xmlschemas/oclcterms/1.4/oclcterms-1.4.xsd">
    <dc:title>Amos Alonzo Stagg, ca. 1891</dc:title>
    <dc:description>A photograph of Amos Alonzo Stagg, ca. 1891, while a student at the International Young Men's Christian Association Training School, now known as Springfield College. Stagg graduated  in 1891  and  served as an assistant physical education instructor at Springfield College from 1890-1892. He started the football program at Springfield College and played in one of the first public basketball game, being the only faculty member to score a &quot;basket ball goal&quot; in their 5 to 1 loss to the students. His football teams at Springfield College were known as &quot;Stagg's Eleven&quot; or the &quot;Stubby Christians&quot;. During the two years he coached and played football at Springfield College, the &quot;Stubby Christians&quot; went 10-11-1 and played in one of the first indoor football games in Madison Square Garden against the Yale Consolidated team on December 12, 1890. In a career spanning more than 50 years, Stagg came to be known as the &quot;Grand Old Man of Football&quot;. He coached football at the University of Chicago (Chicago, Ill.) from 1892-1932 and at the College of  the Pacific from 1933 until his retirement in 1946. Over his career he won 314 games. Amos Alonzo Stagg died in 1965 at the age of 102.</dc:description>
    <dc:subject>Springfield College--Students; Springfield College--Alumni and alumnae; International Young Men's Christian Association Training School (Springfield, Mass.); Springfield College;</dc:subject>
    <dc:subject>Stagg, Amos Alonzo, 1862-1965; Football; Coaching;</dc:subject>
    <dc:publisher>Springfield College</dc:publisher>
    <dcterms:created>1891</dcterms:created>
    <dc:identifier>ms507-01-01-02-001</dc:identifier>
    <dc:identifier>SC21444</dc:identifier>
    <dc:format>Image/jpg</dc:format>
    <dc:type>Image</dc:type>
    <dc:format>Image/tiff</dc:format>
    <dcterms:isPartOf>Amos Alonzo Stagg Papers</dcterms:isPartOf>
    harvested-metadata/springfield.oai.qdc.xml
```

There are plenty of other Bash commands that can let you interact with these big files, but we pulled them primarily to serve to our Python Analysis script - let's take a final minute to think about how we can best manage this process.

### Step 6: Planning Harvests, Data Dump Management, & Non-OAI Harvest Options

As stated above, these Harvest scripts can take a while to execute, and the outputs can be rather large. So you don't want to pull them all the time. Some options include:

1. Pulling at a regular interval (once a month, perhaps) for analysis purposes, but with the recognition that the analysis will be a little bit behind the current state of the live dataset.
2. Look to compressing (using Zip or Tar) these files and serving somewhere like GitHub (if the compressed file is smaller than 100MB). This allows you quicker access to shared data dumps across computers, and it means you can leave a computer/server/other somewhere just pulling those data dumps in regular intervals.
3. Be wary of keeping multiple Harvest data dumps of the same dataset on one laptop - i.e., don't run (unless you really mean to) something like `harvestOAI.py -l 'http://MyOAIfeed' -o 'data/DataSet1.oai.dc.xml' -m 'oai_dc'` then a few days later `harvestOAI.py -l 'http://MyOAIfeed' -o 'data/DataSet1_copy2.oai.dc.xml' -m 'oai_dc'` etc. Instead, look to use something like Git if you need data dump versioning. This sort of process, not thought out, could take a lot of space on your computer.

Are there other approaches you can think of to managing these Harvested datasets?

Finally, the metadataQA library is not limited to OAI-PMH, though that is the most stable script in the Harvest subdirectory. There is the capability to harvest (for analysis) data from [the DPLA API](metadataQA/harvest/harvestDPLA.py) (we will see an example of this later), from the [SharedShelf (Artstor) API](metadataQA/harvest/harvestSharedShelf.py), and test scripts exist for harvesting from Solr as well as Fedora 4. On deck for expansion is harvesting from a ResourceSync source as well as Z39.50 for MARC stores. If you're interested in any of these, please drop me a line and let's stay in touch.