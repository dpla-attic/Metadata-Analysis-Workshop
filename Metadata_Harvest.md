# Metadata Harvests, DPLAFest 2017 DPLA Metadata Analysis Workshop

*2:15-2:45  PM, led by [Christina Harlow, DataOps at Stanford University Libraries](mailto:cmharlow@gmail.com)*

## Goals

- Understand structure of Metadata Harvest & Metadata Breakers scripts
- Walk through the OAI-PMH Metadata Harvest Python script
- Generate some Metadata Harvests
- Use new Bash skills to explore those Harvests/data dumps

**Table of Contents**

* Overview of Metadata Harvest & Metadata Breakers
* Harvest Step 1: Check Python, Pip, VirtualEnv
*

### Overview

Basically, the harvest and analysis Python scripts explored in the rest of this workshop run by first harvesting a full metadata dump of any OAI-PMH feed data, across collections, from the OAI-PMH endpoint. This metadata harvest is stored locally. Then, the analyses occur by running the analysis scripts (with some options available for type of analysis) on that data dump. This generates reports across all collections unless explicitly set in the analysis options.

This requires that the metadata be exposed via an OAI-PMH feed (other APIs, Solr Indices, and data publication methods are supported experimentally - ask Christina later about this) through which the metadata is exposed. Luckily for this DPLA workshop, that's already a requirement for involvement in DPLA.

To speed up this process, we're working on a hosted version of these analysis scripts that copies the data dumps to a database that is then queried (in place of pulling full json data onto your local system). This is still in development.

### Step 1: Check Python, Pip, VirtualEnv Installation/Versions

This should have occured before the workshop, but we're going to run through this quickly know, just in case.

For these harvest and analysis Python scripts to run, we need to check that Python is installed and the version being used.

In your shell used for the previous lesson, type the following command:

```bash
$ python --version
 Python 2.7.13
```

Python 2.7.9 or greater (or Python 3) comes installed with Pip, a package manager for Python. To check Pip, type the following command:

```bash
$ pip --version
 pip 9.0.1 from /usr/local/lib/python2.7/site-packages (python 2.7)
```

Next, in our shell, we want to change into our directory where these repository materials are stored. You should have downloaded and navigated through this in the last lesson. **If not**:

Clone this repository (https://github.com/cul-it/sharedshelf-metadata.git) where you would like to keep it (for example, I keep it in a directory called 'Projects'). Note: this requires **the recursive flag** to get all the materials:

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

With the workshop materials repository on your local machine (you'll need to remember where you downloaded or cloned this directory):

```bash
$ cd Metadata-Analysis-Workshop/
$ pwd
 /Users/Christina/Metadata-Analysis-Workshop
$ ls .
 Bash.md			Metadata_Harvest.md	harvested-metadata	metadata-python
 Metadata_Breakers.md	README.md		images			metadataQA
```

Python virtual environments enable you to isolate the Python options (version used, libraries used, version of libraries used) to a particular working area or project. This is helpful if you don't want to change any global settings on your computer - which often, you don't.

Now we are going to create a virtualenv with the Python version you're working with (if you didn't install virtualenv, then skip this step):

```bash
$ virtualenv venv
```

If you want to create a virtualenv specific a particular Python version that is not the default, use the following command instead, where '/usr/local/bin/python' points to the response of 'which python' or 'which python3', etc.:

```bash
$ virtualenv -p /usr/local/bin/python venv
```

The response of the above should look like this:

```bash
$ virtualenv venv
 New python executable in /Users/Christina/Metadata-Analysis-Workshop/venv/bin/python2.7
 Also creating executable in /Users/Christina/Metadata-Analysis-Workshop/venv/bin/python
 Installing setuptools, pip, wheel...done.
```

You should only have to do the above once (you're done for the rest of this workshop), whereas the follow should be redone when you run the scripts or need to capture updates.

### Step 2: Activate the Python Virtual Environment & Install Script Requirements

Each time you want to run the scripts, you'll want to first activate/start up your virtual environment (if using).

In the same location where you ran the above comments, type and run the virtualenv activation:

```bash
$ source venv/bin/activate
 (venv) bash-3.2$
```
Depending on your shell / command line client, you will see something indicating your working in the 'venv' virtual environment now, namely, the name of the virtualenv you created in parenthesis before the bash command start:

```bash
(venv) $
```

We now want to install all the Python scripts library requirements to this virtual environment. Note: you should only ever have to run this command once when you start and then whenever there are updates to the scripts:

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

With your virtual environment running, and your dependencies installed, you're ready to go. Let's take a look first at the Python script we will be running:

```python
import urllib2
import zlib
import time
import re
import xml.dom.pulldom
import xml.dom.minidom
import codecs
from argparse import ArgumentParser

nDataBytes, nRawBytes, nRecoveries, maxRecoveries = 0, 0, 0, 3


def getFile(link, command, verbose=1, sleepTime=0):
    global nRecoveries, nDataBytes, nRawBytes
    if sleepTime:
        time.sleep(sleepTime)
    remoteAddr = link + '?verb=%s' % command
    if verbose:
        print("\r", "getFile ...'%s'" % remoteAddr[-90:])
    try:
        remoteData = urllib2.urlopen(remoteAddr).read()
    except urllib2.HTTPError as exValue:
        if exValue.code == 503:
            retryWait = int(exValue.hdrs.get("Retry-After", "-1"))
            if retryWait < 0:
                return None
            print('Waiting %d seconds' % retryWait)
            return getFile(link, command, 0, retryWait)
        print(exValue)
        if nRecoveries < maxRecoveries:
            nRecoveries += 1
            return getFile(link, command, 1, 60)
        return
    nRawBytes += len(remoteData)
    try:
        remoteData = zlib.decompressobj().decompress(remoteData)
    except:
        pass
    nDataBytes += len(remoteData)
    mo = re.search('<error *code=\"([^"]*)">(.*)</error>', remoteData)
    if mo:
        print("OAIERROR: code=%s '%s'" % (mo.group(1), mo.group(2)))
    else:
        return remoteData

if __name__ == "__main__":
    parser = ArgumentParser()
    parser.add_argument("-l", "--link", dest="link", help="OAI-PMH URL",
                        default="https://ecommons.cornell.edu/dspace-oai/request")
    parser.add_argument("-o", "--filename", dest="filename",
                        help="write repository to file", default="output.xml")
    parser.add_argument("-f", "--from", dest="fromDate",
                        help="harvest records from this date yyyy-mm-dd")
    parser.add_argument("-u", "--until", dest="until",
                        help="harvest records until this date yyyy-mm-dd")
    parser.add_argument("-m", "--mdprefix", dest="mdprefix", default="oai_dc",
                        help="use the specified metadata format")
    parser.add_argument("-s", "--setName", dest="setName",
                        help="harvest the specified set")
    args = parser.parse_args()

    if not args.link.startswith('http'):
        args.link = 'http://' + args.link
    print("Writing records to %s from repository %s" % (args.filename, args.link))
    verbOpts = ''
    if args.setName:
        verbOpts += '&set=%s' % args.setName
    if args.fromDate:
        verbOpts += '&from=%s' % args.fromDate
    if args.until:
        verbOpts += '&until=%s' % args.until
    if args.mdprefix:
        verbOpts += '&metadataPrefix=%s' % args.mdprefix

    print("Using url:%s" % args.link + '?ListRecords' + verbOpts)

    ofile = codecs.lookup('utf-8')[-1](file(args.filename, 'wb'))
    ofile.write('<?xml version="1.0" encoding="UTF-8"?><OAI-PMH xmlns="http://www.openarchives.org/OAI/2.0/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://www.openarchives.org/OAI/2.0/ http://www.openarchives.org/OAI/2.0/OAI-PMH.xsd"> <responseDate>2015-10-11T00:35:52Z</responseDate> <ListRecords>\n')
    data = getFile(args.link, 'ListRecords' + verbOpts)

    # from http://boodebr.org/main/python/all-about-python-and-unicode#UNI_XML
    RE_XML_IL = u'([\u0000-\u0008\u000b-\u000c\u000e-\u001f\ufffe-\uffff])' + \
                u'|' + \
                u'([%s-%s][^%s-%s])|([^%s-%s][%s-%s])|([%s-%s]$)|(^[%s-%s])' %\
                (unichr(0xd800), unichr(0xdbff), unichr(0xdc00),
                 unichr(0xdfff), unichr(0xd800), unichr(0xdbff),
                 unichr(0xdc00), unichr(0xdfff), unichr(0xd800),
                 unichr(0xdbff), unichr(0xdc00), unichr(0xdfff))
    dataClean = re.sub(RE_XML_IL, "?", data)

    recordCount = 0
    while dataClean:
        events = xml.dom.pulldom.parseString(dataClean)
        for (event, node) in events:
            if event == "START_ELEMENT" and node.tagName == 'record':
                events.expandNode(node)
                node.writexml(ofile)
                recordCount += 1
        more = re.search('<resumptionToken[^>]*>(.*)</resumptionToken>',
                         dataClean)
        if not more:
            break
        data = getFile(args.link, "ListRecords&resumptionToken=%s" % more.group(1))
        dataClean = re.sub(RE_XML_IL, "?", data)

    ofile.write('\n</ListRecords></OAI-PMH>\n'), ofile.close()
    print("\nRead %d bytes (%.2f compression)" % (nDataBytes, float(nDataBytes) / nRawBytes))
    print("Wrote out %d records" % recordCount)
```


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