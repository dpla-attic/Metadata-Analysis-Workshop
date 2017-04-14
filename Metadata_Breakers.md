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

### Analyze Your Local SharedShelf API Data - Overall View

The most basic analysis to run is an analysis report for all metadata fields in your data dump and how often records with those fields appears. To do this, with your virtual environment activated and in the top level of the sharedshelf-metadata directory on your computer:

```bash
(venv) $ python scripts/artstor_analysis.py data/output.json
```

The script should then alert you that it is iterating through the records with this output:

```bash
(venv) $ python scripts/artstor_analysis.py data/output.json
1000 records processed
2000 records processed
3000 records processed
4000 records processed
5000 records processed
6000 records processed
7000 records processed
...
ARTstor Earliest Date: |===                      |  42093/296363 |  14%
           ARTstor Id: |                         |   7334/296363 |   2%
  ARTstor Latest Date: |===                      |  42093/296363 |  14%
       Accession Date: |                         |   6254/296363 |   2%
     Accession Number: |========                 |  95043/296363 |  32%
   Accession Sequence: |                         |    907/296363 |   0%
     Acquisition Date: |                         |   5780/296363 |   1%
    Acquisition Notes: |                         |   5780/296363 |   1%
               Active: |=====                    |  67611/296363 |  22%
Additional Imaging Notes: |                         |     81/296363 |   0%
     Additional Notes: |=                        |  14926/296363 |   5%
Additional Physical Form: |                         |   3443/296363 |   1%
Adler Notes (transcribed): |                         |   2432/296363 |   0%
 Administrative Notes: |                         |   3446/296363 |   1%
Agent_Display.display_value: |==                       |  30871/296363 |  10%
  Agent_Display.links: |==                       |  30871/296363 |  10%
      Alternate Title: |                         |   3149/296363 |   1%
      Analog Creators: |                         |    189/296363 |   0%
                Angle: |                         |   2234/296363 |   0%
       Appraisal Date: |                         |    114/296363 |   0%
       Appraisal Firm: |                         |    247/296363 |   0%
       Appraisal Note: |                         |      9/296363 |   0%
             Approval: |                         |   7620/296363 |   2%
Architect Culture (nationality): |                         |    147/296363 |   0%
       Architect Date: |                         |    184/296363 |   0%
Architect Earliest Date: |                         |    147/296363 |   0%
Architect Latest Date: |                         |    147/296363 |   0%
Architect.display_value: |                         |    233/296363 |   0%
      Architect.links: |                         |    233/296363 |   0%
      ...
```

What this analysis report offers each field (as mapped to a text label where it exists in that collection's API assets response), then a visual and numberic indicator of how many records have this field in the whole dataset.

### Analyze Your Local SharedShelf API Data - Field Specific

Another analysis (or set of analyses) you can run on this SharedShelf data is to see field-specific queries. These come in the follow structures:

#### Return all values of a certain field

Use the field names as they appear in the above output (including Field.Subfield structures as they appear):

```bash
(venv) $ python scripts/artstor_analysis.py data/output.json -e 'Architect Date'
1896-1966
1895-1960
1925-1996
1919-2013
1930-
1905-1981
1936-
1920-2005
1891-1957
1940-2012
1940-2012
1940-2012
1895-1960
1940-2012
1880-1938
...
```

```bash
(venv) $ python scripts/artstor_analysis.py data/output.json -e 'Work Type.links'
AAT-300046159
AAT-300005768
AAT-300047806
AAT-300128359
AAT-300006902
AAT-300008059
AAT-300007466
AAT-300007423
AAT-300047090
AAT-300053622
AAT-300033618
AAT-300128359
AAT-300060417
AAT-300128359
AAT-300041365
AAT-300053242
AAT-300041349
AAT-300128343
AAT-300033618
...
```

#### Return all unique values / values with counts / values with a certain value from a certain field

We can add a bit of bash after our query to limit the response to only unique values ...

```bash
(venv) $ python scripts/artstor_analysis.py data/output.json -e 'Image_Title' | uniq
Detail, trapezoidal shaped grid holding marble panels, corner of facade
Statue of George IV, intersection of Hanover and George St., looking east to St. Andrew\'s Square; Royal Society of Edinburgh at right
Painted plaster wall (colors: tan)
Landscaped grounds by the firm of Hanna Olin, Ltd. (name changed in 1996 to Olin Partnership); exterior view of fiberglass panels
Detail, figures in the clouds representing angels and the Holy Spirit
Marble carriageway carved with imperial motifs, detail, clouds
Altar-like fountain, front view
Courtyard building, detail of roof
Model on site showing the 2010-2011 expansion project
Detail, figure of Jean de Fiennes, the youngest burgher
Site plan of Zhongshan Park, photographed on site
Carved and painted niches of the wall west of the central pillar
View of a typical entry
...
```

Or to get unique values with a count of how many times they appear in our dataset:

```bash
(venv) $ python scripts/artstor_analysis.py data/output.json -e 'Location.display_value' | sort | uniq -c
119
  1
  1   Akureyri
  1   Egypt,  Holmes,  Mississippi,  United States
  1   Florence,  Lauderdale,  Alabama,  United States
  1   Florida,  United States
  1   Iceland
  1  Alexandria Bay
  2  Alexandria, Alexandria, Virginia, United States
  1  Anniston, Calhoun, Alabama, United States
  1  Arkansas, United States
  1  Arlington, Middlesex, Massachusetts, United States
  1  Asheville, Buncombe, North Carolina, United States
  4  Aubervilliers, Seine-Saint-Denis, Île-de-France, France
  1  Bobigny, Seine-Saint-Denis, Île-de-France, France
  1  Boscawen, Merrimack, New Hampshire, United States
 15  Boston, Suffolk, Massachusetts, United States
  1  Bristol, Bristol, Virginia, United States
  1  Brookline, Norfolk, Massachusetts, United States
 15  Brooklyn, New York, New York, United States
  1  Bucine, Arezzo, Tuscany, Italy
  1  Camaiore, Lucca, Tuscany, Italy
 15  Cambridge, Middlesex, Massachusetts, United States
  1  Carteret, Middlesex, New Jersey, United States
  1  Chattanooga, Hamilton, Tennessee, United States
 35  Chicago, Cook, Illinois, United States
...
```

Or to get only those uniq values that have a certain value in the field output:

```bash
(venv) $ python scripts/artstor_analysis.py data/output.json -e 'Measurements' | sort | uniq -c | grep "in."
2 width: 8.75 in; height: 9 in
4 width: 9 in; height: 11 in
8 width: 9 in; height: 11.5 in
2 width: 9 in; height: 11.75 in
2 width: 9 in; height: 12 in
2 width: 9 in; height: 12.5 in
4 width: 9 in; height: 13 in
42 width: 9 in; height: 5.5 in
2 width: 9 in; height: 5.75 in
2 width: 9 in; height: 9 in
2 width: 9 in; height: 9.25 in
...
```

#### Return all unique values / values with counts / values with a certain value with the record's SharedShelf ID

You can add the -i flag to any of the above queries to not just return a field's values, but to return the SharedShelf record id that the value comes from. The SharedShelf record identifiers returned are a concatenation of: CollectionID_RecordID:

```bash
(venv) $ python scripts/artstor_analysis.py data/output.json -i -e 'Language' | sort
139_637452	Japanese|Dutch
139_637467	Japanese|Dutch
139_637481	Japanese|Dutch
139_637496	Japanese|Dutch
139_637512	Japanese|Dutch
139_637528	Japanese|Dutch
139_637543	Japanese|Dutch
139_637558	Japanese|Dutch
139_637573	Japanese|Dutch
139_637589	Japanese|Dutch
139_637604	Japanese|Dutch
139_637620	Japanese|Dutch
139_637636	Japanese|Dutch
139_637650	Japanese|Dutch
139_637665	Japanese|Dutch
...
```

Or you can add the -p flag to any of the above queries to return a 'True' or 'False' to indicate if the field is present in a record (with the -i flag used to tell us the record ID):

```bash
(venv) $ python scripts/artstor_analysis.py data/output.json -i -p -e 'ID Number'
1150_11239776 True
1150_11224502 True
139_629781 True
1150_11255159 True
1150_11259822 True
1150_11272656 True
695_12465715 True
695_12442738 True
98_319902 True
174_4045808 True
153_356182 True
209_770438 True
1150_11246218 True
1098_12810323 True
185_1316799 True
695_12439301 True
1150_11270923 True
...
```

Or combine all of the above for many different types of queries on the metadata. For example, show me the record identifiers for all records that do not have an ID Number field:

```bash
(venv) $ python scripts/artstor_analysis.py data/output.json -i -p -e 'ID Number' | grep 'False'
1098_12812420 False
1150_11221580 False
452_3385578 False
1150_11245562 False
185_1326334 False
185_1322972 False
2849_16824114 False
452_1974872 False
1168_11313474 False
893_13518077 False
185_1319586 False
185_1323006 False
922_10637225 False
1150_11259136 False
1150_11243404 False
746_8787381 False
695_12464694 False
1098_12815018 False
...
```

Note, there will probably be some errors that arise as one goes through all the query possibilities - just let Christina know and she'll try to update these scripts.

Questions for analysis:
1.	Number of records
2.	Existence of sets
3.	What are all the unique elements/XPATH nodes in this data stream
4.	Are required DPLA elements present and mappable:
a.	Title
b.	isShownAt
c.	dataProvider
d.	Rights
e.	thumbnail
5.	Is date mapped to the same field consistently? Does it look like a creation date or a date of digitization?
6.	Is creator mapped to the same field consistently?
7.	Is geographic information in the same field consistently (if included)?
8.	Are properties used in a semantically correct way (publisher is a good example
9.	Are DCMI types included? If not, could they be derived from format?
10.	Do page-level records exist? (e.g. looking for records with title only, titles that contain the work “page”
11.	Does full-text exist? (e.g. looking for extra-long description fields)
12.	Does all of the content meet collecting guidelines? (e.g. a qualitative analysis of titles)