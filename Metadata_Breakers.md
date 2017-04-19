# Metadata Harvests, DPLAFest 2017 DPLA Metadata Analysis Workshop

*3-4:30 PM PM, led by [Christina Harlow, DataOps at Stanford University Libraries](mailto:cmharlow@gmail.com)*

## Goals

- Walk through the OAI-PMH Dublin Core and MODS Analysis Scripts
- Generate DC and MODS full dataset field usage reports
- Generate Field-specific Reports:
  - Field presence
  - Field unique values with counts
  - Record identifiers for above
- Use new Bash skills to expand the Analysis Scripts output & create simple metadata reports

## Requirements

- Bash Shell
- Python 2.x.x >= 2.7.10 (Python 3.x.x not supported for these scripts)
- Copy of the [Workshop GitHub Repository](https://github.com/dpla/Metadata-Analysis-Workshop) on your Laptop (Grab via Git or ask the Workshop leader for a thumb-drive with the files)

## Lesson

**Table of Contents**

* [Overview of Metadata Harvest & Metadata Breakers](#overview)
* [Analysis Step 1: Activate the Python Virtual Environment & Install Script Requirements](#step-1-activate-the-python-virtual-environment--install-script-requirements)
* [Analysis Step 2: Review of the Metadata Breaker/Analysis Scripts](#step-2-review-of-the-metadata-breakeranalysis-scripts)
* [Analysis Step 3: Generate a Field Usage Report for a Dataset](#analysis-step-3-generate-a-field-usage-report-for-a-dataset)
* [Analysis Step 4: Generate Field-specific Reports]()
* [Analysis Step 5: Writing These Reports to File]()
* [Examples & Practice]

### Overview

_This is taken from the [Metadata Harvest](Metadata_Harvest.md) portion of the workshop. It is replicated here just for ease of following along via this document._

The Metadata Harvest and Analysis Python scripts explored in the rest of this workshop run by first harvesting a full metadata dump of any OAI-PMH feed data from the OAI-PMH endpoint. This metadata harvest is stored locally. Then, the analyses occur by running the analysis scripts (with some options available for type of analysis) on that data dump. This generates reports across all collections or subsets as explicitly set in the harvest and analysis options.

This requires that the metadata be exposed via an OAI-PMH feed (other APIs, Solr Indices, and data publication methods are supported experimentally - we will touch briefly on other options later). Luckily for this DPLA workshop, that's already a common occurrence for DPLA Provider Data (thanks, Repox).

*Nota bene*: To speed up this process, I'm working on a hosted version of these analysis scripts that copies the data dumps to a database that is then queried (in place of pulling full XML data onto your local system). This is still in development. If this ends up being something you would like to see happen or could support, please ping me.

### Step 1: Activate the Python Virtual Environment & Install Script Requirements

_This is taken from the [Metadata Harvest](Metadata_Harvest.md) portion of the workshop. It is replicated here just for ease of following along via this document._

Each time you want to run the scripts, you'll want to first activate/start up your virtual environment (if using).

In the top level directory of this workshop's materials on your computer, type and run the virtualenv activation `source venv/bin/activate`:

```bash
$ pwd
  /Users/Christina/Projects/Metadata-Analysis-Workshop
$ source venv/bin/activate
  (venv) bash-3.2$
```

Depending on your shell, you will see something indicating your working in the 'venv' virtual environment now, namely, the name of the virtualenv you created in parentheses before the bash command start:

```bash
(venv)$
```

We now want to install all the Python scripts library requirements to this virtual environment.

_Note: you should only ever have to run this command once when you start and, after that, only when there are updates to the scripts._

Type `pip install -r metadataQA/requirements.txt` (use tab to autocomplete filenames & confirm you're in the right repository!):

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

If you're having issues with this step, go back to the [Metadata Harvest section on Python setup for this workshop](Metadata_Harvest.md#step-1-check-python-pip-virtualenv-installationversions).

### Step 2: Review of the Metadata "Breaker"/Analysis Scripts

Let's take a look first at two of these Python scripts we will be running before jumping in. We are primarily interested in the OAI-DC Analysis option (`metadataQA/analysis/oaidc_analysis.py`) and the MODS Analysis option (`metadataQA/analysis/mods_analysis.py`)

*If you want to look on your screen, use `open metadataQA/analysis/oaidc_analysis.py`. This will open the OAI-DC Analysis file in your laptop's default text editor. Alternatively, you can look at [the file in GitHub in your web browser](https://github.com/cmh2166/metadataQA/tree/master/analysis).*

<details>

*metadataQA/analysis/oaidc_analysis.py*
```python
import hashlib
import sys
import pprint
from argparse import ArgumentParser
from xml.etree import ElementTree as etree
import six

OAI_NS = "{http://www.openarchives.org/OAI/2.0/}"
DC_NS = "{http://purl.org/dc/elements/1.1/}"


class RepoInvestigatorException(Exception):
    """This is our base exception class for this script."""
    def __init__(self, value):
        self.value = value

    def __str__(self):
        return "%s" % (self.value,)


class Record:
    """Base class for a Dublin Core (qdc or dc) metadata record in an OAI-PMH
       Repository file."""
    def __init__(self, elem, args):
        """Create the Record class instance."""
        self.elem = elem
        self.args = args

    def get_record_id(self):
        """Find an identifier for the record, checking the OAI header."""
        try:
            header = self.elem.find("%sheader" % OAI_NS)
            record_id = header.find("%sidentifier" % OAI_NS).text
            return record_id
        except:
            raise RepoInvestigatorException("Record does not have a valid Record Identifier. Check the structure of the Harvested XML (does it have a OAI header? Are the namespaces correct?) and the XML path used here to get to the identifier.")

    def get_record_status(self):
        """Get only 'active' status OAI-PMH records."""
        return self.elem.find("%sheader" % OAI_NS).get("status", "active")

    def get_elements(self):
        """Get all the values for a given DC element/field."""
        out = []
        try:
            elements = self.elem[1][0].findall(DC_NS + self.args.element)
            for element in elements:
                if element.text:
                    out.append(element.text.encode("utf-8").strip())

            if len(out) == 0:
                out = None

            self.elements = out
            return self.elements
        except IndexError:
            self.elements = None
            return self.elements

    def get_all_data(self):
        """Gets all the values as a list for the TSV export option."""
        out = []
        for i in self.elem[1][0]:
            if i.text:
                out.append((i.tag, i.text.encode("utf-8").strip().replace("\n", " ")))
        return out

    def get_stats(self):
        """Get the field presence stats for the default report."""
        stats = {}
        try:
            for element in self.elem[1][0]:
                stats.setdefault(element.tag, 0)
                stats[element.tag] += 1
            return stats
        except IndexError:
            return stats

    def has_element(self):
        """Return True/False if a given field is present and non-empty."""
        elements = self.elem[1][0].findall(DC_NS + self.args.element)
        for element in elements:
            if element.text:
                return True
        return False


def collect_stats(stats_aggregate, stats):
    """Method for generating the default field usage report."""
    #increment the record counter
    stats_aggregate["record_count"] += 1

    for field in stats:

        # get the total number of times a field occurs
        stats_aggregate["field_info"].setdefault(field, {"field_count": 0})
        stats_aggregate["field_info"][field]["field_count"] += 1

        # get average of all fields
        stats_aggregate["field_info"][field].setdefault("field_count_total", 0)
        stats_aggregate["field_info"][field]["field_count_total"] += stats[field]


def create_stats_averages(stats_aggregate):
    """Method for generating the default field usage report."""
    for field in stats_aggregate["field_info"]:
        field_count = stats_aggregate["field_info"][field]["field_count"]
        field_count_total = stats_aggregate["field_info"][field]["field_count_total"]

        field_count_total_average = (float(field_count_total) / float(stats_aggregate["record_count"]))
        stats_aggregate["field_info"][field]["field_count_total_average"] = field_count_total_average

        field_count_element_average = (float(field_count_total) / float(field_count))
        stats_aggregate["field_info"][field]["field_count_element_average"] = field_count_element_average

    return stats_aggregate


def calc_completeness(stats_averages):
    """Method for generating the default field usage report."""
    completeness = {}
    record_count = stats_averages["record_count"]
    completeness_total = 0
    wwww_total = 0
    dpla_total = 0
    collection_total = 0
    collection_field_to_count = 0

    wwww = [
        "{http://purl.org/dc/elements/1.1/}creator",       # who
        "{http://purl.org/dc/elements/1.1/}title",         # what
        "{http://purl.org/dc/elements/1.1/}identifier",    # where
        "{http://purl.org/dc/elements/1.1/}date"           # when
    ]

    dpla = [
        "{http://purl.org/dc/elements/1.1/}title",
        "{http://purl.org/dc/elements/1.1/}identifier",
        "{http://purl.org/dc/elements/1.1/}rights"
    ]

    populated_elements = len(stats_averages["field_info"])
    for element in sorted(stats_averages["field_info"]):
            element_completeness_percent = 0
            element_completeness_percent = ((stats_averages["field_info"][element]["field_count"]
                                             / float(record_count)) * 100)
            completeness_total += element_completeness_percent

            # gather collection completeness
            if element_completeness_percent > 10:
                collection_total += element_completeness_percent
                collection_field_to_count += 1
            # gather wwww completeness
            if element in wwww:
                wwww_total += element_completeness_percent
            # gather dpla completeness
            if element in dpla:
                dpla_total += element_completeness_percent

    completeness["dc_completeness"] = completeness_total / float(15)
    completeness["collection_completeness"] = collection_total / float(collection_field_to_count)
    completeness["wwww_completeness"] = wwww_total / float(len(wwww))
    completeness["dpla_completeness"] = dpla_total / float(len(dpla))
    completeness["average_completeness"] = ((completeness["dc_completeness"] +
                                             completeness["collection_completeness"] +
                                             completeness["wwww_completeness"] +
                                             completeness["dpla_completeness"]) / float(4))
    return completeness


def pretty_print_stats(stats_averages):
    """Method for generating the default field usage report."""
    record_count = stats_averages["record_count"]
    # get header length
    element_length = 0
    for element in stats_averages["field_info"]:
        if element_length < len(element):
            element_length = len(element)

    print("\n\n")
    for element in sorted(stats_averages["field_info"]):
        percent = (stats_averages["field_info"][element]["field_count"] / float(record_count)) * 100
        percentPrint = "=" * (int((percent) / 4))
        columnOne = " " * (element_length - len(element)) + element
        print("%s: |%-25s| %6s/%s | %3d%% " % (
            columnOne,
            percentPrint,
            stats_averages["field_info"][element]["field_count"],
            record_count,
            percent
        ))

    print("\n")
    completeness = calc_completeness(stats_averages)
    for i in ["dc_completeness", "collection_completeness", "wwww_completeness", "dpla_completeness", "average_completeness"]:
        print("%23s %f" % (i, completeness[i]))


def main():
    # Sets up values needed for the default field report.
    stats_aggregate = {
        "record_count": 0,
        "field_info": {}
    }
    element_stats_aggregate = {}

    # CLI client options.
    parser = ArgumentParser(usage='%(prog)s [options] data_filename.xml')
    parser.add_argument("-e", "--element", dest="element",
                        help="element to print to screen")
    parser.add_argument("-i", "--id", action="store_true", dest="id",
                        default=False, help="prepend meta_id to line")
    parser.add_argument("-s", "--stats", action="store_true", dest="stats",
                        default=False, help="only print stats for repository")
    parser.add_argument("-p", "--present", action="store_true", dest="present",
                        default=False, help="print if the field is non-empty.")
    parser.add_argument("-d", "--dump", action="store_true", dest="dump",
                        default=False, help="Dump data to tab-delimited format")
    parser.add_argument("file", help="put the datafile you want analyzed here")

    args = parser.parse_args()

    # Returns the help text if there are no flags or a datafile present.
    if not len(sys.argv) > 0:
        parser.print_help()
        exit()

    # Sets the flow control based on the flags given in the CLI client.
    if args.element is None:
        args.stats = True

    # For record counting purposes.
    s = 0

    # Namespace dictionary creation for ease of working with NS namespaces.
    # This is used to generate a nsmap based off of the given data file.
    nsmap = {}

    def fixtag(ns, tag):
        return '{' + nsmap[ns] + '}' + tag

    # Parsing each record in dataset file passed and applying report methods:
    for event, elem in etree.iterparse(args.file, events=('end', 'start-ns')):
        if event == 'start-ns':
            ns, url = elem
            nsmap[ns] = url
        if event == 'end':
            if elem.tag == fixtag("", "record"):
                r = Record(elem, args)
                record_id = r.get_record_id()

                if args.dump is True:
                    if r.get_record_status() != "deleted":
                        record_fields = r.get_all_data()
                        for field_data in record_fields:
                            print("%s\t%s\t%s" % (record_id, field_data[0], field_data[1].replace("\t", " ")))
                    elem.clear()
                    continue

                if args.stats is False and args.present is False:
                    # move along if record is deleted or None
                    if r.get_record_status() != "deleted" and r.get_elements():
                        for i in r.get_elements():
                            if args.id:
                                print("\t".join([record_id, i]))
                            else:
                                print(i)

                if args.stats is False and args.present is True:
                    if r.get_record_status() != "deleted":
                        print("%s %s" % (record_id, r.has_element()))

                if args.stats is True and args.element is None:
                    if (s % 1000) == 0 and s != 0:
                        print("%d records processed" % s)
                    s += 1
                    if r.get_record_status() != "deleted":
                        collect_stats(stats_aggregate, r.get_stats())
                elem.clear()

    if args.stats is True and args.element is None and args.dump is False:
        stats_averages = create_stats_averages(stats_aggregate)
        pretty_print_stats(stats_averages)

if __name__ == "__main__":
    main()
```

</details>

### Analysis Step 3: Generate a Field Usage Report for a Dataset

The most basic analysis to run is an analysis report for all metadata fields in your data dump and how often records with those fields appears. To do this, with your virtual environment activated and in the top level of the workshop directory on your computer:

```bash
(venv) $ python metadataQA/analysis/oaidc_analysis.py harvested-metadata/carli_bra_jack.oai.qdc.xml
```

The script should then alert you that it is iterating through the records with this output:

```bash
(venv) $ python metadataQA/analysis/oaidc_analysis.py harvested-metadata/carli_bra_jack.oai.qdc.xml
       1000 records processed
       2000 records processed
```

Then it will output a basic field stats report:

```bash
(venv) $ python metadataQA/analysis/oaidc_analysis.py harvested-metadata/carli_bra_jack.oai.qdc.xml
       1000 records processed
       2000 records processed
       {http://purl.org/dc/elements/1.1/}creator: |======================== |   2173/2222 |  97%
          {http://purl.org/dc/elements/1.1/}date: |=============            |   1187/2222 |  53%
   {http://purl.org/dc/elements/1.1/}description: |======================== |   2221/2222 |  99%
        {http://purl.org/dc/elements/1.1/}format: |======================== |   2151/2222 |  96%
    {http://purl.org/dc/elements/1.1/}identifier: |=========================|   2222/2222 | 100%
      {http://purl.org/dc/elements/1.1/}language: |                         |     14/2222 |   0%
        {http://purl.org/dc/elements/1.1/}rights: |=========================|   2222/2222 | 100%
        {http://purl.org/dc/elements/1.1/}source: |=========================|   2222/2222 | 100%
       {http://purl.org/dc/elements/1.1/}subject: |=========================|   2222/2222 | 100%
         {http://purl.org/dc/elements/1.1/}title: |=========================|   2222/2222 | 100%
          {http://purl.org/dc/elements/1.1/}type: |======================== |   2158/2222 |  97%
             {http://purl.org/dc/terms/}isPartOf: |=========================|   2222/2222 | 100%


           dc_completeness 69.714971
   collection_completeness 95.008592
         wwww_completeness 87.803780
         dpla_completeness 100.000000
      average_completeness 88.131836
```

What this analysis report offers each field with the namespace it exists in prefixed, then a visual and numeric indicator of how many records have this field in the whole dataset. The completeness rankings at the bottom give us a rough value to attach to the presence of core fields, indicated in the `calc_completeness` method in our Metadata Analysis script.

The above works for any DC metadata - it just needs to be in the DC namespace (not necessarily valid DC). If you wanted to run this report for MODS, you would run a similar script but using the `mods_analysis.py` script instead:

```bash
(venv) $ python metadataQA/analysis/mods_analysis.py harvested-metadata/fsu_admiral.mods.xml

                                                                   mods:accessCondition: |=========================|     93/93 | 100%
    mods:extension/{info:flvc/manifest/v1}flvc/{info:flvc/manifest/v1}owningInstitution: |=========================|     93/93 | 100%
mods:extension/{info:flvc/manifest/v1}flvc/{info:flvc/manifest/v1}submittingInstitution: |=========================|     93/93 | 100%
                                                                             mods:genre: |=========================|     93/93 | 100%
                                                                        mods:identifier: |=========================|     93/93 | 100%
                                                        mods:language/mods:languageTerm: |=========================|     93/93 | 100%
                                                                 mods:location/mods:url: |=========================|     93/93 | 100%
                                                                mods:name/mods:namePart: |======================== |     90/93 |  96%
                                                      mods:name/mods:role/mods:roleTerm: |======================== |     90/93 |  96%
                                                                              mods:note: |=========                |     36/93 |  38%
                                                       mods:originInfo/mods:dateCreated: |======================== |     91/93 |  97%
                                              mods:originInfo/mods:place/mods:placeTerm: |======================   |     84/93 |  90%
                                            mods:physicalDescription/mods:digitalOrigin: |=========================|     93/93 | 100%
                                                   mods:physicalDescription/mods:extent: |=========================|     93/93 | 100%
                                                     mods:physicalDescription/mods:note: |                         |      1/93 |   1%
                                               mods:recordInfo/mods:descriptionStandard: |=========================|     93/93 | 100%
                                                mods:recordInfo/mods:recordCreationDate: |=========================|     93/93 | 100%
                                   mods:relatedItem/mods:location/mods:physicalLocation: |=========================|     93/93 | 100%
                                                mods:relatedItem/mods:location/mods:url: |=========================|     93/93 | 100%
                                             mods:relatedItem/mods:titleInfo/mods:title: |=========================|     93/93 | 100%
                                                           mods:subject/mods:geographic: |                         |      1/93 |   1%
                                                       mods:subject/mods:geographicCode: |======================   |     84/93 |  90%
                                                             mods:subject/mods:temporal: |                         |      1/93 |   1%
                                                                mods:subject/mods:topic: |=========================|     93/93 | 100%
                                                           mods:titleInfo/mods:subTitle: |                         |      1/93 |   1%
                                                              mods:titleInfo/mods:title: |=========================|     93/93 | 100%
                                                                    mods:typeOfResource: |=========================|     93/93 | 100%


collection_completeness 96.119682
      wwww_completeness 98.655914
      dpla_completeness 100.000000
   average_completeness 73.693899
```

Note that this gives us the presence of nested XML fields now.

Nota bene: often this script will fail with a 'cannot find identifier for record' or a 'float division by zero' error. This is often because the metadata being presented in the OAI-PMH feed is not in the correct namespaces - i.e., DC presented back with the wrong oai_dc namespace URL, MODS presented as DC, etc. Check the data first, using your new bash skills (`head -3 harvested-metadata/carli_bra_jack.dc.xml` for example, to check out the first part of the dataset).

**Exercise:** Take a few minutes and run general reports on the sample datasets in the harvested-metadata directory or on the metadata you harvested yourself. Click the 'details' section below for a list of sample lines to run if you need help.

<details>
```bash
(venv) $ python metadataQA/analysis/oaidc_analysis.py harvested-metadata/carli_wiu_digimgc.oai.qdc.xml
1000 records processed
2000 records processed
3000 records processed
4000 records processed



    {http://purl.org/dc/elements/1.1/}creator: |===                      |    668/4195 |  15%
       {http://purl.org/dc/elements/1.1/}date: |=========                |   1513/4195 |  36%
{http://purl.org/dc/elements/1.1/}description: |=====================    |   3653/4195 |  87%
 {http://purl.org/dc/elements/1.1/}identifier: |=========================|   4195/4195 | 100%
  {http://purl.org/dc/elements/1.1/}publisher: |===============          |   2612/4195 |  62%
   {http://purl.org/dc/elements/1.1/}relation: |======================   |   3853/4195 |  91%
     {http://purl.org/dc/elements/1.1/}rights: |=========================|   4195/4195 | 100%
    {http://purl.org/dc/elements/1.1/}subject: |======================   |   3723/4195 |  88%
      {http://purl.org/dc/elements/1.1/}title: |=========================|   4195/4195 | 100%
          {http://purl.org/dc/terms/}isPartOf: |=========================|   4195/4195 | 100%
          {http://purl.org/dc/terms/}modified: |======================   |   3859/4195 |  91%
          {http://purl.org/dc/terms/}replaces: |======                   |   1035/4195 |  24%


        dc_completeness 59.906238
collection_completeness 74.882797
      wwww_completeness 62.997616
      dpla_completeness 100.000000
   average_completeness 74.446663
```

```bash
(venv) $ python metadataQA/analysis/oaidc_analysis.py harvested-metadata/springfield.oai.qdc.xml
1000 records processed
2000 records processed
3000 records processed
4000 records processed
5000 records processed
6000 records processed
7000 records processed
8000 records processed
9000 records processed
10000 records processed
11000 records processed



{http://purl.org/dc/elements/1.1/}contributor: |                         |     32/8327 |   0%
   {http://purl.org/dc/elements/1.1/}coverage: |                         |      1/8327 |   0%
    {http://purl.org/dc/elements/1.1/}creator: |=============            |   4584/8327 |  55%
       {http://purl.org/dc/elements/1.1/}date: |=====                    |   1700/8327 |  20%
{http://purl.org/dc/elements/1.1/}description: |=========================|   8327/8327 | 100%
     {http://purl.org/dc/elements/1.1/}format: |======================== |   8323/8327 |  99%
 {http://purl.org/dc/elements/1.1/}identifier: |=========================|   8327/8327 | 100%
   {http://purl.org/dc/elements/1.1/}language: |==============           |   4910/8327 |  58%
  {http://purl.org/dc/elements/1.1/}publisher: |======================== |   8243/8327 |  98%
   {http://purl.org/dc/elements/1.1/}relation: |=============            |   4337/8327 |  52%
     {http://purl.org/dc/elements/1.1/}rights: |======================== |   8226/8327 |  98%
     {http://purl.org/dc/elements/1.1/}source: |=                        |    643/8327 |   7%
    {http://purl.org/dc/elements/1.1/}subject: |======================== |   8323/8327 |  99%
      {http://purl.org/dc/elements/1.1/}title: |=========================|   8327/8327 | 100%
       {http://purl.org/dc/elements/1.1/}type: |======================== |   8323/8327 |  99%
           {http://purl.org/dc/terms/}created: |===================      |   6627/8327 |  79%
            {http://purl.org/dc/terms/}extent: |===========              |   3847/8327 |  46%
          {http://purl.org/dc/terms/}isPartOf: |==================       |   6169/8327 |  74%
            {http://purl.org/dc/terms/}medium: |                         |    155/8327 |   1%
           {http://purl.org/dc/terms/}spatial: |===                      |   1269/8327 |  15%


        dc_completeness 80.615668
collection_completeness 74.953465
      wwww_completeness 68.866338
      dpla_completeness 99.595693
   average_completeness 81.007791
```

```bash
(venv) $ python metadataQA/analysis/oaidc_analysis.py harvested-metadata/williams.oai.dc.xml
1000 records processed
2000 records processed
3000 records processed
4000 records processed



{http://purl.org/dc/elements/1.1/}contributor: |====================     |   3518/4384 |  80%
   {http://purl.org/dc/elements/1.1/}coverage: |========                 |   1563/4384 |  35%
    {http://purl.org/dc/elements/1.1/}creator: |                         |     47/4384 |   1%
       {http://purl.org/dc/elements/1.1/}date: |=================        |   3012/4384 |  68%
{http://purl.org/dc/elements/1.1/}description: |=================        |   3089/4384 |  70%
     {http://purl.org/dc/elements/1.1/}format: |=======================  |   4150/4384 |  94%
 {http://purl.org/dc/elements/1.1/}identifier: |=========================|   4384/4384 | 100%
   {http://purl.org/dc/elements/1.1/}language: |==========               |   1850/4384 |  42%
  {http://purl.org/dc/elements/1.1/}publisher: |                         |     18/4384 |   0%
   {http://purl.org/dc/elements/1.1/}relation: |                         |     74/4384 |   1%
     {http://purl.org/dc/elements/1.1/}rights: |=======================  |   4113/4384 |  93%
    {http://purl.org/dc/elements/1.1/}subject: |=================        |   3153/4384 |  71%
      {http://purl.org/dc/elements/1.1/}title: |=========================|   4384/4384 | 100%
       {http://purl.org/dc/elements/1.1/}type: |=======================  |   4151/4384 |  94%


        dc_completeness 57.034672
collection_completeness 77.486314
      wwww_completeness 67.444115
      dpla_completeness 97.939477
   average_completeness 74.976144
```
</details>

### Analysis Step 4: Generate Field-Specific Reports

Another analysis (or set of analyses) you can run on the OAI-PMH datasets is to see field-specific queries. These come in the follow structures:

#### Return all values of a certain field

Use the field names as they appear in the above general report output (including Field.Subfield structures as they appear) in the -e or -x flag (-x will only apply to MODS and should be your default for MODS element queries).

This will return a ton of values that speed by - don't worry, that's expected. The values returned are all the values for that field across all records.

```bash
(venv) $ python metadataQA/analysis/oaidc_analysis.py harvested-metadata/carli_bra_jack.oai.qdc.xml -e 'date'
August, 1976
August 22, 1976
August, 1976
August, 1976
August, 1976
August, 1976
August, 1976
August, 1976
August, 1976
1966
1968
March 30, 1975
March 30, 1975
March 30, 1975
March 30, 1975
March 30, 1975
March 30, 1975
March 30, 1975
March 30, 1975
...
```

```bash
(venv) $ python metadataQA/analysis/mods_analysis.py harvested-metadata/fsu_admiral.mods.xml -x 'mods:titleInfo/mods:title'
Letter from J. H. Daylon appointing Richard H. Leigh member of a board to develop a navy yard plan
Order from the Navy Department regarding the filing of Richard H. Leighâs inspection report of the Galveston
Order to the Commander-in-Chief of the U.S. Asiatic Fleet from Josephus Daniels
Order from W. C. Cowles for the Commanding Officer of the U.S.S. Galveston
Order from the United States Asiatic Fleet for Richard H. Leigh to report for duty as President of Court of Inquiry
W. C. Cowles requesting an added reference to the inspection of the Galveston
Letter to Leigh from Victor Blue regarding an undated letter of commendation
Quarterly reports on progress of educational system
Rifle Match Galveston vs. American S. V. C.
Order from the Bureau of Navigation for Richard H. Leigh to report for temporary duty in New York, N.Y.
Order from the Bureau of Navigation granting Richard H. Leigh a thirty-day leave of absence
Order from the Bureau of Navigation granting Richard H. Leigh a ten-day leave of absence
Request from R. H. Leigh to report for duty in command of the U.S.S. Galveston
Order from the Rear Admiral for R. H. Leigh to report to Commander J. H. Dayton
Endorsement from J. H. Dayton addressing the completion of R. H. Leighâs duty
Letter addressing the award of gunnery trophy to R. H. Leigh
Letter to Lewis Coxe from Richard H. Leigh
Order from the Navy Department assigning Richard H. Leigh temporary additional duty
...
```

#### Return all unique values / values with counts / values with a certain value from a certain field

The mad dash of values going by is interesting, but not terribly helpful generally. We can add a bit of our new Bash skills after our query to limit the response to only unique values - this relies on the Pipe idea we learned about earlier and the Bash command `uniq`.

Here is the DC example:

```bash
(venv) $ python metadataQA/analysis/oaidc_analysis.py harvested-metadata/carli_bra_jack.oai.qdc.xml -e 'date' | uniq
      1957
      1963
      1962
      1965
      1958
      1977
      1972
      1976
      1974
      1977
      1976
      1974
      1975
      1976
      1974
      1977
      1976
      1977 [?]
      1976
      1968
      1975
      1976
      1961
      1976
...
```

and here is the MODS example:

```bash
(venv) $ python metadataQA/analysis/mods_analysis.py harvested-metadata/fsu_admiral.mods.xml -x 'mods:titleInfo/mods:title' | uniq
      Correspondence between R. H. Leigh and Navy Pay Officer
      Report of Liberty and Leave Breaking for Year
      Order from the Navy Department regarding the commendation upon award of Gunnery Trophy to Richard H. Leigh
      Quoted information from the Report of Inspection of the Galveston
      Order from the Bureau of Navigation assigning Richard H. Leigh temporary duty of the wreck of the Maine
      Order from the Bureau of Navigation denying Richard H. Leighâs request to be assigned duty at the New York Nautical School
      Order from the Bureau of Navigation transmitting Richard H. Leigh a Philippine Campaign badge
      Order from the Bureau of Navigation transmitting R. H. Leigh a Spanish Campaign Badge
      Order from U.S. Navy Commander for R. H. Leigh to send a report of duties and orders
      Order from the Bureau of Navigation enclosing Richard H. Leighâs commission
      Order from Navy Department assigning Richard H. Leigh temporary additional duty
      ...
```

But hey, those aren't unique! We need to use `uniq` and `sort` together to get the response we expect. Additionally, we can get unique values with a count of how many times they appear in our dataset by appending a little more Bash (now `sort` then `uniq -c`):

(venv) $ python scripts/artstor_analysis.py data/output.json -e 'Location.display_value' | sort | uniq -c

Here is the DC example for unique, sorted values with counts:

```bash
(venv) $ python metadataQA/analysis/oaidc_analysis.py harvested-metadata/carli_bra_jack.oai.qdc.xml -e 'date' | sort | uniq -c
      1 1954
      1 1955? - 1960?
      2 1957
      1 1958
      1 1958 - 1959
      1 1958?
      1 1958? - 1965?
      1 1958? -1965?
      19 1960?
      1 1960? - 1970?
      1 1961
      10 1962
      3 1963
      3 1964
      18 1965
      2 1966
      1 1967
      2 1968
      ...
```

and here is the MODS example for unique, sorted values with counts:

```bash
(venv) $ python metadataQA/analysis/mods_analysis.py harvested-metadata/fsu_admiral.mods.xml -x 'mods:titleInfo/mods:title' | sort | uniq -c
      1 Authorization from the Navy Department for R. H. Leigh to delay reporting to the U.S.S. Chicago for ten days
      1 Captain R.H. Cooper requesting for R. H. Leigh to report to the Head of the Department of Navigation as an instructor for the United States Naval Academy
      1 Correspondence between R. H. Leigh and Navy Pay Officer
      1 Endorsement from J. H. Dayton addressing the completion of R. H. Leighâs duty
      1 Granted request from the Chief of Bureau of Navigation, authorizing R.H. Leigh to change his permanent address
      1 Handwritten notes about locations with corresponding dates, written on Bureau of Navigation Memorandum paper
      1 History of the letter from Cousin Willie Temple
      1 Letter addressing the award of gunnery trophy to R. H. Leigh
      1 Letter from Alice to Ebbie, April 15, 1865
      1 Letter from J. H. Daylon appointing Richard H. Leigh member of a board to develop a navy yard plan
      1 Letter from Mama to âMy Dearest Rich,â August 30, 1888
      1 Letter from Papa to Richard, August 30, 1888
      ...
```

Going even a step further, we can get only those uniq values that have a certain value in the field output by now adding `grep`:

Here is the DC example for unique, sorted values with counts where the value contains a '?':

```bash
(venv) $ python metadataQA/analysis/oaidc_analysis.py harvested-metadata/carli_bra_jack.oai.qdc.xml -e 'date' | sort | uniq -c | grep '?'
      1 1955? - 1960?
      1 1958?
      1 1958? - 1965?
      1 1958? -1965?
      19 1960?
      1 1960? - 1970?
      19 1968?
      11 1970? - 1980?
      1 1970? -1980?
      17 1975-1977 [?]
      138 1975?
      72 1976 [?]
      11 1976-1977 [?]
      43 1977 [?]
```

And here is the MODS example for unique, sorted values with counts where the value contains the word 'Letter':

```bash
(venv) $ python metadataQA/analysis/mods_analysis.py harvested-metadata/fsu_admiral.mods.xml -x 'mods:titleInfo/mods:title' | sort | uniq -c | grep 'Letter'
      1 Letter addressing the award of gunnery trophy to R. H. Leigh
      1 Letter from Alice to Ebbie, April 15, 1865
      1 Letter from J. H. Daylon appointing Richard H. Leigh member of a board to develop a navy yard plan
      1 Letter from Mama to âMy Dearest Rich,â August 30, 1888
      1 Letter from Papa to Richard, August 30, 1888
      1 Letter from Richard H. Leigh requesting duty at sea
      1 Letter to Commanding Officer of the U.S.S. Galveston discussing the success of the small arms practice
      1 Letter to Leigh from Victor Blue regarding an undated letter of commendation
      1 Letter to Lewis Coxe from Richard H. Leigh
```

Note how we have whittled down our originally large data responses to more specific subsets.

**Exercise:** Take a few minutes and run some field reports using `uniq`, `sort`, and `grep` on the sample datasets in the harvested-metadata directory or on the metadata you harvested yourself. Click the 'details' section below for a list of sample lines to run if you need help.

<details>

All the unique publishers in carli_wiu_digimgc that contain the word 'Loan'
```bash
(venv) $ python metadataQA/analysis/oaidc_analysis.py harvested-metadata/carli_wiu_digimgc.oai.qdc.xml -e 'publisher' | sort | uniq -c | grep 'Loan'
```

All unique contributor values in Springfield metadata:

```bash
(venv) $ python metadataQA/analysis/oaidc_analysis.py harvested-metadata/springfield.oai.qdc.xml -e 'contributor' | sort | uniq -c
```

The unique Types available in Williams metadata:

```bash
(venv) $ python metadataQA/analysis/oaidc_analysis.py harvested-metadata/williams.oai.dc.xml -e 'type' | sort | uniq
```
</details>

#### Return all unique values / values with counts / values with a certain value with the record's ID

The above is nice, but then how do you target records or collections for work? You can add the `-i` flag to any of the above queries to not just return a field's values, but to return the OAI-PMH Header Record identifier that the value comes from.  Note: this ruins the return unique values aspect, though!

Here is the DC example:

```bash
(venv) $ python metadataQA/analysis/oaidc_analysis.py harvested-metadata/carli_bra_jack.oai.qdc.xml -i -e 'date'
        oai:collections.carli.illinois.edu:bra_jack/2078	August, 1976
        oai:collections.carli.illinois.edu:bra_jack/2083	1966
        oai:collections.carli.illinois.edu:bra_jack/2089	1968
        oai:collections.carli.illinois.edu:bra_jack/2090	March 30, 1975
        oai:collections.carli.illinois.edu:bra_jack/2091	March 30, 1975
        oai:collections.carli.illinois.edu:bra_jack/2092	March 30, 1975
        oai:collections.carli.illinois.edu:bra_jack/2093	March 30, 1975
        oai:collections.carli.illinois.edu:bra_jack/2094	March 30, 1975
        oai:collections.carli.illinois.edu:bra_jack/2095	March 30, 1975
        oai:collections.carli.illinois.edu:bra_jack/2096	March 30, 1975
        oai:collections.carli.illinois.edu:bra_jack/2097	March 30, 1975
        oai:collections.carli.illinois.edu:bra_jack/2098	March 30, 1975
        oai:collections.carli.illinois.edu:bra_jack/2099	March 30, 1975
        oai:collections.carli.illinois.edu:bra_jack/2100	March 30, 1975
        oai:collections.carli.illinois.edu:bra_jack/2101	March 30, 1975
        oai:collections.carli.illinois.edu:bra_jack/2102	March 30, 1975
        oai:collections.carli.illinois.edu:bra_jack/2103	March 30, 1975
        oai:collections.carli.illinois.edu:bra_jack/2104	March 30, 1975
        oai:collections.carli.illinois.edu:bra_jack/2105	March 30, 1975
        oai:collections.carli.illinois.edu:bra_jack/2106	March 30, 1975
        oai:collections.carli.illinois.edu:bra_jack/2107	March 30, 1975
        oai:collections.carli.illinois.edu:bra_jack/2108	March 30, 1975
        oai:collections.carli.illinois.edu:bra_jack/2109	March 30, 1975
...
```

and here is the MODS example:

```bash
(venv) $ python metadataQA/analysis/mods_analysis.py harvested-metadata/fsu_admiral.mods.xml -i -x 'mods:titleInfo/mods:title'
      oai:fsu.digital.flvc.org:fsu_406013	Order from the United States Asiatic Fleet for Richard H. Leigh to report for duty as President of Court of Inquiry
      oai:fsu.digital.flvc.org:fsu_406015	W. C. Cowles requesting an added reference to the inspection of the Galveston
      oai:fsu.digital.flvc.org:fsu_406018	Letter to Leigh from Victor Blue regarding an undated letter of commendation
      oai:fsu.digital.flvc.org:fsu_406017	Quarterly reports on progress of educational system
      oai:fsu.digital.flvc.org:fsu_406020	Rifle Match Galveston vs. American S. V. C.
      oai:fsu.digital.flvc.org:fsu_406006	Order from the Bureau of Navigation for Richard H. Leigh to report for temporary duty in New York, N.Y.
      oai:fsu.digital.flvc.org:fsu_406005	Order from the Bureau of Navigation granting Richard H. Leigh a thirty-day leave of absence
      oai:fsu.digital.flvc.org:fsu_406008	Order from the Bureau of Navigation granting Richard H. Leigh a ten-day leave of absence
      oai:fsu.digital.flvc.org:fsu_406007	Request from R. H. Leigh to report for duty in command of the U.S.S. Galveston
      oai:fsu.digital.flvc.org:fsu_406011	Order from the Rear Admiral for R. H. Leigh to report to Commander J. H. Dayton
      oai:fsu.digital.flvc.org:fsu_406012	Endorsement from J. H. Dayton addressing the completion of R. H. Leighâs duty
      ...
```

Or you can add the `-p` flag to any of the above queries to return a 'True' or 'False' to indicate if the field is present in a record (with the -i flag used to tell us the record ID).

Here is the DC example:

```bash
(venv) $ python metadataQA/analysis/oaidc_analysis.py harvested-metadata/carli_bra_jack.oai.qdc.xml -i -p -e 'date'
      oai:collections.carli.illinois.edu:bra_jack/2197 False
      oai:collections.carli.illinois.edu:bra_jack/2198 False
      oai:collections.carli.illinois.edu:bra_jack/2199 False
      oai:collections.carli.illinois.edu:bra_jack/2200 False
      oai:collections.carli.illinois.edu:bra_jack/2201 False
      oai:collections.carli.illinois.edu:bra_jack/2202 False
      oai:collections.carli.illinois.edu:bra_jack/2203 False
      oai:collections.carli.illinois.edu:bra_jack/2204 False
      oai:collections.carli.illinois.edu:bra_jack/2205 False
      oai:collections.carli.illinois.edu:bra_jack/2206 False
      oai:collections.carli.illinois.edu:bra_jack/2207 False
      oai:collections.carli.illinois.edu:bra_jack/2208 False
      oai:collections.carli.illinois.edu:bra_jack/2209 False
      oai:collections.carli.illinois.edu:bra_jack/2210 False
      oai:collections.carli.illinois.edu:bra_jack/2211 False
...
```

and here is the MODS example:

```bash
(venv) $ python metadataQA/analysis/mods_analysis.py harvested-metadata/fsu_admiral.mods.xml -i -p -x 'mods:titleInfo/mods:title'
      oai:fsu.digital.flvc.org:fsu_406014 True
      oai:fsu.digital.flvc.org:fsu_406016 True
      oai:fsu.digital.flvc.org:fsu_406019 True
      oai:fsu.digital.flvc.org:fsu_406013 True
      oai:fsu.digital.flvc.org:fsu_406015 True
      oai:fsu.digital.flvc.org:fsu_406018 True
      oai:fsu.digital.flvc.org:fsu_406017 True
      oai:fsu.digital.flvc.org:fsu_406020 True
      oai:fsu.digital.flvc.org:fsu_406006 True
      oai:fsu.digital.flvc.org:fsu_406005 True
      oai:fsu.digital.flvc.org:fsu_406008 True
      oai:fsu.digital.flvc.org:fsu_406007 True
      oai:fsu.digital.flvc.org:fsu_406011 True
      oai:fsu.digital.flvc.org:fsu_406012 True
      oai:fsu.digital.flvc.org:fsu_406010 True
      oai:fsu.digital.flvc.org:fsu_406009 True
      ...
```

#### All the Above Together

Now let's combine all of the above for many different types of queries on the metadata. For example, show me the record identifiers for all records that do not have a date field:

```bash
(venv) $ python metadataQA/analysis/oaidc_analysis.py harvested-metadata/carli_bra_jack.oai.qdc.xml -i -p -e 'date' | grep 'False'
      oai:collections.carli.illinois.edu:bra_jack/2200 False
      oai:collections.carli.illinois.edu:bra_jack/2201 False
      oai:collections.carli.illinois.edu:bra_jack/2202 False
      oai:collections.carli.illinois.edu:bra_jack/2203 False
      oai:collections.carli.illinois.edu:bra_jack/2204 False
      oai:collections.carli.illinois.edu:bra_jack/2205 False
      oai:collections.carli.illinois.edu:bra_jack/2206 False
      oai:collections.carli.illinois.edu:bra_jack/2207 False
      oai:collections.carli.illinois.edu:bra_jack/2208 False
      oai:collections.carli.illinois.edu:bra_jack/2209 False
      oai:collections.carli.illinois.edu:bra_jack/2210 False
      oai:collections.carli.illinois.edu:bra_jack/2211 False
      oai:collections.carli.illinois.edu:bra_jack/2212 False
      oai:collections.carli.illinois.edu:bra_jack/2213 False
      oai:collections.carli.illinois.edu:bra_jack/2214 False
...
```

Or all of the records that don't have a mods:titleInfo/mods:title element (we hope none!)

```bash
(venv) $ python metadataQA/analysis/mods_analysis.py harvested-metadata/fsu_admiral.mods.xml -i -p -x 'mods:titleInfo/mods:title' | grep 'False'
```

Note, there will probably be some errors that arise as one goes through all the query possibilities - just let Christina know and she'll try to update these scripts. You can also open issues on the [MetadataQA GitHub Repository](https://github.com/cmh2166/metadataQA/issues) - and thank you for helping!

### Analysis Step 5: Writing These Reports to File

You can write any of the above to a file by using the Bash commands we learned before as well - just pipe your regular commmand to a text file. For example:

```bash
(venv) $ python metadataQA/analysis/oaidc_analysis.py harvested-metadata/carli_bra_jack.oai.qdc.xml -i -p -e 'date' | grep 'False' > harvested-metadata/carli_bra_jack_missing_date.txt
```

Writes the output we saw before in our shell into the `harvested-metadata/carli_bra_jack_missing_date.txt` file.

### Examples & Practice

Using the above, work through how you would uncover the following - either with the sample `harvested-metadata` or your own harvested data (click the details section below each to find one possible answer):

1.	Number of records

<details>

Run the general report - it will tell you how many records total are present in the fields/records output at the end. e.g.:

```python metadataQA/analysis/mods_analysis.py harvested-metadata/fsu_admiral.mods.xml```

Tells us that set has 93 records total.

</details>

2.	Existence of sets

<details>

This is a trick question - you shouldn't use these scripts to uncover this, but use your web browser (or some HTTP API) to query the OAI-PMH base + `?verb=ListSets`. i.e.: http://fsu.digital.flvc.org/oai2?verb=ListSets

</details>

3.	What are all the unique elements/XPATH nodes in this data stream

<details>

Run the general report - it will tell you all the unique fields - nested or not (use the DC / MODS scripts separately!) appear and have a value.

</details>

4.	Are required DPLA elements present and mappable:

<details>

Run the general report then check for the presence of your dataset's metadata fields that map to the DPLA MAP. You can also edit the  completeness values in that general field usage report to calculate this dependent on your mapping.

If you want to generate the report, you can use:

```python metadataQA/analysis/oaidc_analysis.py harvested-metadata/springfield.oai.qdc.xml```

If you want to capture a list of all records that are missing a field, you can use the `-p` (present) and `-i` (record identifier) flags in combination with the `-e` (element) or `-x` (xpath) flag:

```python metadataQA/analysis/oaidc_analysis.py harvested-metadata/springfield.oai.qdc.xml -i -p -e 'rights' | grep 'False'```

</details>

4a.	Title

<details>

Use the `-e` (element) or `-x` (xpath) flag as appropriate (DC vs MODS) with the `sort | uniq -c` if you only want unique values:

```python metadataQA/analysis/oaidc_analysis.py harvested-metadata/springfield.oai.qdc.xml -e 'title'```

or

```python metadataQA/analysis/mods_analysis.py harvested-metadata/fsu_admiral.mods.xml -x 'mods:titleInfo/mods:title' | sort | uniq -c```

</details>

4b.	isShownAt

<details>

Use the `-e` (element) or `-x` (xpath) flag as appropriate (DC vs MODS) with the `sort | uniq -c` if you only want unique values, then review the field in your dataset that maps to isShownAt in the DPLA MAP.

</details>

4c.	dataProvider

<details>

Use the `-e` (element) or `-x` (xpath) flag as appropriate (DC vs MODS) with the `sort | uniq -c` if you only want unique values, then review the field in your dataset that maps to dataProvider in the DPLA MAP.

</details>

4d.	Rights

<details>

Use the `-e` (element) or `-x` (xpath) flag as appropriate (DC vs MODS) with the `sort | uniq -c` if you only want unique values, then review the field in your dataset that maps to Rights in the DPLA MAP.

</details>

4e.	thumbnail

<details>

Use the `-e` (element) or `-x` (xpath) flag as appropriate (DC vs MODS) with the `sort | uniq -c` if you only want unique values, then review the field in your dataset that maps to thumbnail in the DPLA MAP.

</details>

All of the above give you the ability to browse and review the data in a faster manner than using something like OpenRefine with huge datasets or reviewing the OAI-PMH feeds in a web browser. But they don't automate how you make a decision, only how you can review and analyze the data to get to a decision. Work through the following questions and figure out how the above can help you determine the following (note: this will not be the same for each person or institution).

5.	Is date mapped to the same field consistently? Does it look like a creation date or a date of digitization?
6.	Is creator mapped to the same field consistently?
7.	Is geographic information in the same field consistently (if included)?
8.	Are properties used in a semantically correct way (publisher is a good example, you can use `grep` to look for the digitization institution's name).
9.	Are DCMI types included? If not, could they be derived from `format` or another field (`title`?)?
10.	Do page-level records exist? (e.g. looking for records with title only, titles that contain the work “page”)
11.	Does full-text exist? (e.g. looking for extra-long description fields)
12.	Does all of the content meet collecting guidelines? (e.g. a qualitative analysis of titles)