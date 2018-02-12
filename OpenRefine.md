# OpenRefine Workshop, DPLA Members Meeting, March 14, 2018

*led by [Gretchen Gueguen, Data Services Coordinator, Digital Public Library of America](mailto:gretchen@dp.la)*
*Based on [an OpenRefine workshop](https://github.com/DLFMetadataAssessment/DLFMetadataQAWorkshop17/blob/master/OpenRefine.md) created by Scotty Carlson*

## License
All instructional materials are being made available under a [Creative Commons Attribution license] (https://creativecommons.org/licenses/by/4.0/). Feel free to reuse these materials according to these license terms.

## Goals of This Module:
* Introduce Open Refine
* Basic Data Assessment for DPLA Hubs
* Data Remediation
* Data Validation
* Data Enhancement

## What is OpenRefine?
OpenRefine (formerly Google Refine) is a powerful tool for working with messy data, including tools for cleaning, transforming from one format into another, extending with web services, and connecting to external data.

Can OpenRefine be used to create data from scratch? Not really. OpenRefine is built to work with existing data, although projects can be enriched with outside data.

Download links and instructions can be found [here](). OpenRefine requires a working Java runtime environment, otherwise the program will not start. Upon launch, OpenRefine will run as a local server, opening in your computer's browser. As long as OpenRefine is running, you can point your browser at either http://127.0.0.1:3333/ or http://localhost:3333/ to use it, even in several browser tabs/windows.

## Module Objectives

What kinds of assessments can we do with OpenRefine?

* Completeness: Checking to see what elements/properties/attributes are present (and how much of it is missing!)
* Accuracy: Information is correct and factual (to the best of our abilities)
* Conformance to expectations: Information adheres to our expectations
* Consistency: Values are consistent within our domain, elements are represented in a consistent manner

Based on these assessment measures, what assessment techniques can be applied to work in OpenRefine?

* Do the number of rows/records match up to expectations?
* Which unique elements/fields are represented in the data?
* How much of each represented element/field is empty?
* What are the common values in particular elements/fields?
* Does the data need to be converted to intelligible formatting?
* Can the data be validated externally?
* Can the data be enriched with new information?

## Table of Contents

* Importing Data
* The OR Interface
* Clean Up XML in Tabular Format
* Assessment Tools
* Analysis for DPLA Compatibility
* Validating Data
* Data Remediation
* Data Enhancement
* Getting Data Out

## Importing Data

From Refine's start screen, you can create projects from structured data files, continue working on past projects, and import projects that were exported out of OpenRefine. 

Refine can import TSV, CSV, Excel, XML, JSON, and Google Data documents as well as parse raw, unformatted data copied and pasted using the Clipboard function. 


## Import an XML file

In our DPLA workflows we might typically work with XML data and Open Refine can help us convert that data to a tabular format. However, it requires some clean-up when records have multiple instances of a property. Let's start by uploading a file.

Start with **Create Project** in the menu, then use the button to select a file from your computer. Let's upload the sample file from our workshop documents. This is a file typical of the output of an OAI PMH feed with about 74,000 records. Once you've selected the file click **Next**.

OpenRefine will then upload the XML file. The amount of time this takes will depend on the size of the files. Very large files may be difficult or even impossible to work with.

Once the file is uploaded, you must select the XML property that is the parent for the record, so that they can be parsed accurately. Refine presents you with the beginning of the file and you must click on the opening <record> tag. You will be able to tell that you have the correct element selected because the record will be highlighted yellow.

You should now see a preview of what your data will look like as a table in the main interface. It may look a bit off at this point, but don't worry, we will clean it up in the next step. Just make sure that the values in columns that have values look like they are coming from the correct properties (e.g. creators in one column, dates in another as opposed to seeing several different types of values in a column.) 

Click **Create Project**.

## The OR Interface
After the data loads, you will be staring at the default Refine interface for your new project. In the upper lefthand corner, you will see your project name (which can be changed by clicking on it) next to the OpenRefine logo. (If you need to return to the starting screen, click that logo.)

Each of the columns in the data have drop-down menus (the upside down triangles). When you select an option in a particular column (e.g. to make a change to the data), it will affect all the cells in the column currently selected. (We will discuss this more below in Faceting). The column at the extreme left of the screen is the **All Data** menu. The All drop-down will let you make changes across all columns in one pass. It also manages the Star and Flag toggles -- more on that in Faceting.

### Rows vs. Records

OpenRefine has two modes of viewing data: Rows and Records. Upon project creation, the default setting is Rows mode, where each row represents a single record in the data set -- in our case, one entry in the database. In Records mode, OpenRefine can group multiple rows as belonging to the same Record in the original XML file. Switching to this mode, we see that the All Data column now shows a number of blank cells. These represent rows that are part of a single record, the start of which is indicated by the numbered row. Multi-row records happen when Refine detects multiple values within the selected record or node.

We also notice that our count of records is considerably higher than the 74,000 or so we expect. This is because, like a database, the first column needs to act as "Key" column, or a column with data that will be unique for each record. If the first column has empty cells, as ours does, these will be erroneously treated as part of a previous record. On the other hand if this column originally had multiple values in the original record, these will now be treated as multiple records. If we toggle back and forth between Records and Rows we also notice that the two views show different totals

To combat this, we need to move a unique record identifier to the first column of our OpenRefine Data. This will help make sure that multi-row records stay grouped according to your understanding of a record.

Let's identify the column containing the `<identifier>` element in the OAI `<header>` and move it to the first position: from the drop-down, select **Edit column** > **Move column to the beginning**. The Records and Rows totals should now be equal.

Set the view to Records.

## Clean-up XML in Tabular Format

There are several artifacts of the conversion of our XML to a table that we now want to clean up in order to proceed. Our goal will be to have each record represented in a single row so that we can eventually evaluate whether records have blank properties. If we were to search for this now, we would find numerous examples of false blanks due to the multiple rows for each record.

### Remove Unneccessary Columns

First however, let's remove unnecessary columns. The process of creating the table from our XML created a separate column for every parent element. For example, if our record was in MODS and had a `<titleInfo><title>` tag, the resulting table would have two columns, one blank one for `<titleInfo>` and another with the actual values for `<title>`.

Go through each column in the spreadsheet and look at the heading. Since the heading contains the entire XPATH for each element, some of them may not fit, but hovering over the column heading for a few seconds will open a small text box with the full title. For each column you want to remove, click the drop-down menu in the heading and choose **Edit column** > **Remove this column**.

### Combine Multiple Properties Into a Single Cell

Once all the unnecessary columns are removed, we can now combine the multi-valued properties into a single cell for each record. 

First, click the drop-down on the OAI `<identifier>` column and select **Facet** > **Customized facets** > **Facet by blank**. Facet information appears in the left hand panel in the OpenRefine interface. The faceted values for *True* are cells that are empty, and the values for *False* shows the fields that do have data. Since each record has only one OAI identifier, the blank cells here represent rows that hold multiple values for other columns. 

Select the *True* option to limit our view to only the records with multiple values. You will notice that the record count at the top of the table now shows the number of records with these blank rows. 

To begin to collapse the values, let's go to the next column in the table. Click on the drop-down menu and select **Edit cells** > **Join multi-value cells**. A window will pop-up asking for the delimiter you would like to use to separate values. In order to reduce confusion with semi-colons or commas that may already be in the records, put **" | "** in the text box and click OK. 

Refine may take some time to complete that process if the table is very large. Proceed through the rest of the columns in the table performing the same process. The number at the top of the table will get progressively smaller until there are no blank rows left. Once this happens, deselect the True option in the facet to return to the full view of all records. We now should have the same number of rows and records.

Once you have joined all the cells, remove the blank facet for identifier by clicking the "X" in the upper left corner of the facet box.

*(In order to save time with this step, you may instead load a tab-delimited version of the project with all the joins completed.)*


### Remove Deleted Records

One last step we need to take to get the table ready to analyze is removed "deleted" records from the table. The OAI protocol allows publishers of data to indicate when records have been removed from a resource by adding the value "deleted" to an element called `<status>`. To identify which records these are, find the status column. Click on the drop-down and select **Facet** > **Text facet**. A new facet box will appear with the unique values for this property. Select "deleted" from this facet to limit the table to only the rows for the deleted records.

Now we need to remove those records. Go to the **All Data** column in the far left. This column has different options than the others. Click on the drop-down menu on that column and select **Edit rows** > **Remove all matching rows**. When Refine finishes this step, you show have no records showing. Unselect the option for "deleted" in the facet we created for status to restore the remaining records.

### Undo/Redo & Applying Saved Actions

OpenRefine lets you undo (and redo) any number of the transformations you will enact on your imported data. Your Undo/Redo history is stored with the Project and is saved automatically as you work, so the next time you open the project, you can access your full history of transformations; this means you can always try out transformations and wipe them if needed.

The 'Undo' and 'Redo' options are accessed via the lefthand panel, which lists all the steps you have undertaken. To undo, simply click on the last step you want to preserve in the list. This will automatically wipe out all the changes made after that step. The remaining steps will continue to show as greyed out; you can reapply them by simply clicking on the last step you want to apply. However, if you undo something and then apply new transformations, the greyed out steps will disappear completely, so make sure you don't need to save any of these steps before you get back to work!

This method will also allow you to take your work on one dataset and apply it to another via simple copy-paste. If you wish to save what you have done to be re-applied later, or to an entirely different project, click **Extract**. This allows you to copy any of the steps (or all of them) as JSON (Javascript Object Notation). This JSON data can be saved to separate file and used later by clicking the **Apply** button and pasting in the JSON data. This could be a great time-saver as you work through iterative XML feeds of the same data over time.

## Assessment Tools

### Sorting

You can sort data in OpenRefine by to the drop-down menu and choosing **Sort**. Once you have sorted the data, a new 'Sort' drop-down menu will be displayed that lets you amend the existing sort (e.g., reverse the sort order), remove existing sorts, and/or make sorts permanent.

Let's try it ourselves on the title column. Click Sort in the drop-down menu for the Title column. Choose to sort by text, and in a-z order.

Note that in the furthest lefthand column, the Refine-appointed row ID numbers are still tied to their original rows. This is because sorts in OpenRefine are temporary -- if you remove the Sort, the data will go back to its original "unordered" form. You can do this by selecting **Sort** > **Remove Sort**. If you want to make the sort permanent, you can instead choose **Sort > Reorder rows permanently**. The numbers in the lefthand column will now change to reflect the new order.

### Faceting

The core of Refine's power lies in its use of Facets and Filtering. We have already used facets to do some basic clean-up of our data set, but we will now use them to analyze our data. Facets allow you to take a macro-level look at a large amount of data by counting individual pieces of column data, and listing them. Filtering can also allow you to select subsets of your data to act on, instead of changing entire columns.

 Refine supports a range of other types of facet. These include:

* **Text** facets simply list the complete text of each cell and how often those text values exactly appear in the column.
* **Numeric** and **Timeline** facets display graphs instead of lists of values, with controls to set a start and end range to filter the data displayed.
* **Scatterplot** facets are less commonly used ([see this tutorial for more information](http://enipedia.tudelft.nl/wiki/OpenRefine_Tutorial#Exploring_the_data_with_scatter_plots)).
* **Custom** facets offer a range of different customized facets, and also allow you write your own.

Let's create a facet on the `<edm:dataProvider>` column. Select: **Facet** > **Text facet**. A new facet should appear in the lefthand window.

Clicking on any of the entries in the facet window will change the interface to include only the record(s) featuring that facet entry. If you move your mouse pointer over an entry in the facet window, you can select multiple facet entries using the **Include** popup next to each entry. You can also select **Invert** at the top of the facet window to automatically select the opposite of the values you chose.

If you move your mouse pointer over an entry in the facet window, you'll also see the option to **Edit** the term comes up. By changing the text in the edit box and clicking Apply, you will automatically change all instances in the data at once.

Text facets are probably the most useful type, but another we have also used is essential for determining how much of each represented element/field is empty. Try creating a custom facet for empty values in the `<edm:isShownAt>`: **Facet** > **Customized Facets** > **Facet by Blank**

### Filtering

You can also apply Text Filters which looks for a particular piece of text appearing in a column. Click the drop down menu at the top of the subject column and choose **Text filter**. In the facet area, a filter box will appear. Type in the text you want to use in the Filter to display only rows which contain that text in the selected column.

In our data, typing *null* will limit what we see to only the 3 records who values in the Facet contain the word nulln somewhere.

### Splitting & Joining Multi-Valued Cells

We've already learned how to join multiple rows into a single cell. As part of our analysis though we might want to split these apart for individual analysis. Refine can easily break apart this data and put it back together later. For example, in this data set we want to make sure that the identifier in the first position in each record is the one that should be used for the DPLA identifier. If we split the identifiers apart we can evaluate just those properties, and then we can put them back together to do other review.

To do this, select **Edit Cells** > **Split Multi-Valued Cells** on the `<dc:identifier>` column's dropdown menu. Enter the same pipe character we used earlier to indicate where the values have been delimited **" | "** in the pop-up menu:

This splits the values back into separate rows. That may not be the most useful way to analyze these values however. Instead, let's try splitting them into new columns.

First, let's rejoin this split data by undoing our last action.

Next, let's click on the drop-down menu and choose **Edit Column** > **Split into several columns**. Put our pipe delimiter in the pop-up window and hit Ok.

Refine has now added several new columns to our table for the multiple values. We can facet and filter each column to see the identifiers that were in the first, second, and third position for each record and analyze the identifiers in the first column to see what they look like. In this case, we could filter for the prefixes that we expect to see here (e.g. "auhist", "auislandora", "p16808coll12", etc.).

To put our columns back together, let's again undo our last action.

## Analysis for DPLA Compatibility 

DPLA has very specific requirements and recommendations for data that is being shared with us. Open Refine gives you analysis tools to determine if data is ready for sharing.

### Required Fields

The most important thing to check with Refine is the presence of values in required properties. Since we cleaned up the table to create a single row for each record we can simply run the **Facet** > **Customized Facet** > **Facet by blank** to see if every record contains a value for:
* Title
* Data Provider (contributing institution)
* Rights statement
* IsShownAt
* Thumbnail (in some cases this may not be in every record if objects that are video or audio are included)

To evaluate *rights* in particular, you may need to analyze more than one column at once. Luckily, OpenRefine allows you to combine more than one facet. Let's create text facets for both the `dc:rights` and `edm:rights` columns. At the bottom of the lists of text values for each facet we see the value *(blank)*. This is not an indication of a value in the text, but that the cell is actually blank. Clicking on the option for blank in the `edm:rights` facet first, we see that the number for the blank values in the `dc:rights` facet updates to reflect only these newly selected records. Clicking the blank facet now in the `dc:rights` facet will limit our table to just those records missing a value in both of these properties. From here we could choose to star these records for later analysis or export just these rows in a table to download (more on that later).

### Types

While type is not a required field, DPLA uses the values for one of its search facet, so it is highly recommended. We can normalize values in your data to the standard values we use based on the DCMIType vocabulary, but your source data must have some consistent indicator of type that we can use. You can evaluate this by creating a text facet for the values in whichever property you are using for type values. The standardized forms that DPLA will use are:
* image
* text
* moving image
* sound
* physical object

Look for terms that would match to these in your records. These will be important to know in order to set up a mapping for these values.

### Languages

Language is another field that is not required but is highly recommended. We can also use a text facet to analyze this property. In this case we are looking for values that correspond to either the name or ISO 639-3 code for the language list at https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes

### Formats

Formats can also be valuable data for DPLA searchers. While we don't have specific recommendations for this field, it may be valuable to try to review this property to see if things like extent or digital image format have mixed in with the formats. This superfluous data will make it hard for DPLA to make progress in mapping free text format values to a specific list of format values, a project DPLA is hoping to implement in the future to improve faceted searching and browsing.

### Places

Another property that DPLA attempts to do some normalization on is spatial data. After the mapping process in complete, we try to match all names in place properties to an external vocabulary. We then try to explicitly fill in sub-properties for country, state, county, city and geo-spatial coordinates.

This process works best when data is entered as a geo-spatial coordinate. That provides the best match. If that is not possible, the next best format is as a single property with names in ascending order, such as "Erie, Pennsylvania."

Using the text facet you can take a look at the data in your place names to determine if more effort needs to be put into normalizing these values.

### Date vs. Temporal

Some metadata properties can be confusing and implemented inconsistently. A common source of confusion is the difference between date fields and temporal coverage fields. 

Date is meant to refer to the date of the creation of the original material, not the date of digitization or an era or time period that the content is about. DPLA maps dates of original item creation to our <dc:date> property.

Temporal on the other is a property that describes a time period that an item is _about_. A book written in 2001 about the Civil War would have a date of 2001 and temporal coverage of 1861-1865.

Unfortunately, these three facets of temporal data (date of creation, date of digitization, and date of temporal coverage) can get confused. Reviewing the dates in these properties if they are present can help to determine if they have been used consistently across all records.

One method that can be helpful is to use the filter tool to find values that are after a particular date, for example if we know a collection shouldn't have any creation dates after 2000. Start by clicking on the drop-down for the date column and choose Text Filter. In the text filter box, click the checkbox for regular expression. This will allow us to do our filter using the regular expression query language, which is a very powerful way to do very precise string searching. We are just going to do a very simple search though for "^20". The "^" is the regular expression indicator for the beginning of a string. The results of this filter should show us only rows with a value in this cell that starts with "20" which, for date, would mean dates after 2000. (If you'd like to learn more regular expressions, try a regex cheatsheet. I like the one at: https://www.cheatography.com/davechild/cheat-sheets/regular-expressions/)

### Collection Title

Finally, you might want to pay attention to collection titles if they are included. DPLA is hoping to begin displaying collection titles in the website view of these records and enabling hyperlinking to other items with the same collection title. 

"Collection" is a word that means a lot of different things to different people. For DPLA's context we would like to define collection as: 

>Any intentionally created grouping of materials. This could include, but is not limited to: materials that are related by provenance, and described and managed using archival control; materials intentionally assembled based on a theme, era, etc.; and groupings of materials gathered together to showcase a particular topic (e.g., digital exhibits or DPLA primary source sets). Not included in this definition are assemblages of digital objects that are not the result of some sort of intentional selection. For example, all of the objects that are exposed to DPLA by a particular institution would, generally speaking, not be a collection in this sense. All of the digital objects that belong to a specific type or form/genre – maps, for instance – would also not be a collection in the context of DPLA.

Take a look through the values in the collection title property. Would these titles make sense if viewed in a DPLA record? Do they fit into the defintion of collection above?

### Set Names

One final column that might be helpful to facet is the set name. By selecting a set name from the facet, you will notice that all the other facets adjust to only reflect just the records in that set. You can even select more than one facet to precisely pinpoint different groups of records within sets. Depending on how your records are created and how your sets are put together, this kind of refinement may help you identify issues.

## Data Remediation

The following sections refer to methods of enhancing and changing data rather than just analyzing it. With the exception of the section on **Adding a New Column From Another Spreadsheet**, most of the information to follow will be of limited usefulness when analyzing data at DPLA Hubs for compatibility. However, as it may be quite useful in other contexts, or for DPLA Contributing Institutions themselves, we will include these sections as well. 

## Clustering

Clustering is Refine's method of algorithmically comparing a column's data against itself to look for inconsistencies. Refine uses two methods (Key Collision and Nearest Neighbor) with different functions to look for potential data inconsistencies.

On the facet sidebar, hit **Reset all** to clear any facets or filters that are currently selected. Let's try clustering the Subject column. To start, let's use the split cells function to put each subject back in a single row. Once that is complete, click the drop-down on this column and choose **Edit cells** > **Cluster and edit**.

The Clusters from our dataset are mostly textual inconsistencies -- spelling, capitalization, etc. For each, you have the option of merging the values together, replacing inconsistencies with a single, consistent value. By default OpenRefine uses the most common value in the cluster as the new value, but you can select one of the other values by clicking the value itself, or you can simply type the desired value into the New Cell Value box.

Clustering can raise some interesting assessment questions: ***Are data values/terms used consistently? If not, what is the formatting/entry standard for this data?***

#### Exercise

The default Cluster method, Key Collision/Fingerprint, is designed to provide as few false-positive results as possible. Other cluster functions will give you a wide range of supposed inconsistencies. Take 5 minutes to play around with the clustering results.

#### Note
>It is worth noting that clustering in OpenRefine works only at the syntactic level (the character composition of the cell value) and while very useful to spot errors, typos, and inconsistencies it's by no means enough to perform effective semantically-aware reconciliation.

When finished, re-join the values with a pipe character: **Edit Cells** > **Join Multi-Valued Cells**.

### GREL Expressions

Transformations in OpenRefine are ways of manipulating data in columns beyond facets and filters. Transformations are predominately written in *GREL*, or [General Refine Expression Language](https://github.com/OpenRefine/OpenRefine/wiki/General-Refine-Expression-Language). If you are familiar with Python commands or Excel formulas, you may see a number of similarities in GREL.

#### Common Transformations Menu

Some common transformations are accessible directly through menu options, without having to type them directly. Click on a column drop-down and select **Edit Cells** > **Common Transformations** to see them:

![refine-7.png](images/refine-7.png)

Behind these menu options, however, are GREL expressions that can be applied manually:

|Common Transformation | GREL expression|
|:--------------------:|:--------------:|
|Convert the value to uppercase text|`value.toUppercase()`|
|Convert the value to lowercase text|`value.toLowercase()`|
|Convert the value to titlecase text|`value.toTitlecase()`|
|Trim leading and trailing whitespace|`value.trim()`|
|Collapse consecutive whitespace|`value.replace(/\s+/,' ')`|
|Convert the value to a number|`value.toNumber()`|
|Convert the value to a date|`value.toDate()`|
|Convert the value to a string of text|`value.toString()`|

#### Writing GREL Expressions

The transform window is where we can get into the grooves of Refine's power by executing custom-written GREL commands. To get here, select from a column drop-down: **Edit Cells** > **Transform...**. Upon opening, you'll notice the word `value` is logged in the command window; this variable stands in for the original value of the cells that we aim to change. (`value` is also a valid GREL expression -- make no changes.)

GREL expressions are written as a function being applied to some kind of data value. Some GREL functions require additional parameters or options to control what the function does. Underneath the command window, you can see a preview of the changes your expressions will inflict.

![refine-8.png](images/refine-8.png)

Try out these GREL expressions on the any column of your choice. You don't need to actually change the data, but do watch the previews on the command window and report any problems you run into!

* Change cases to uppercase and lowercase
* Replace a string of text: `value.replace('a', '@@@'))`
* You can also stack commands on top of each other: split apart strings by whitespace and then re-join them with pipes using `value.split(' ').join('|')`

### Adding a new column from another spreadsheet
Let's imagine that we have a second spreadsheet of the DPLA records for these items. We want to add a column to our spreadsheet with the DPLA identifier.

The first thing we need to do is upload our second spreadsheet. Click **Open** from the top of the refine window and upload the spreadsheet the same way we had previously uploaded our XML file.

Joining the two spreadsheet relies on finding a key column. These are columns in each spreadsheet that hold identical values. For this example, let's use the `isShownAt` column in the DPLA spreadsheet and the `edm:isShownAt` column in our first spreadsheet.

We will first need to add a new column to hold our new values. Click the drop-down on the `edm:isShownAt` column and choose **Edit column** > **Add column based on this column**. A window will open for the GREL expression to create our new column. First we need to add a new column name in the first blank box. Next you'll notice the word value is logged in the command window; this variable stands in for the original value of the cells that we aim to change.

GREL expressions are written as a function being applied to some kind of data value. Some GREL functions require additional parameters or options to control what the function does. Underneath the command window, you can see a preview of the changes your expressions will cause.

To add the new column we are going to use a command called cell.cross. The paramenters required will be the spreadsheet and column names:

`cell.cross("DPLA Spreadsheet", "isShownAt").cells["edm:isShownAt"].value[0]`

You can see in the preview window that we now have matching DPLA id numbers for each record.

We can make this expression slightly better by adding some logic to avoid errors when there is not a matching row in the DPLA spreadsheet:

`if (value!='null',cell.cross("Merge Test B", "HESA code").cells["Average Teaching Score"].value[0],'')`

This statement now says to add the DPLA id **only if** the **edm:isShownAt** field is populated.

## Validating Data

The following sections on validating and enhancing data will be better served by using a different data set with more reconcilable creator and place names. To do these sections, let's create a new OpenRefine Project by clicking **CreateProject** and going through the steps again to upload the file *book.csv* from our sample data.

### Reconciliation

Reconciliation allows you to match your data against external data services to reconcile known entities. Remember when we said clustering works only at the syntactic level, and can't perform semantically-aware reconciliation? This function fills in that gap; the only trick is, it requires external data resources to support the service. (Full info on Reconciliation can be found [here](https://github.com/OpenRefine/OpenRefine/wiki/Reconciliation-Service-API).)

#### Exercise: VIAF Reconciliation

Let's try out a Reconciliation example using [Jeff Chiu's](https://github.com/codeforkjeff) VIAF Reconciliation service. (Note: Jeff's original version of this reconciliation service has been superseded by a new version, which can be found [here](https://github.com/codeforkjeff/conciliator). However, since our needs are super low for this example, we can use his deprecated public server at http://refine.codefork.com/. If you plan to play around with his service on your own, use the newer version.)

1. First, let's use our split apart *Creator* column. Let's facet on the column: **Facet** > **Text facet**
2. In the facet, let's **Include** the following five author names: 'Aesop', 'Aristotle', 'Caesar, Julius', 'Chaucer, Geoffrey', and 'Dante Alighieri'. You should see 19 matching records/rows.
3. In the dropdown menu, select **Reconcile** > **Start Reconciling**.
4. Click **Add Standard Service** and in the dialogue that appears, enter: http://refine.codefork.com/reconcile/viaf
5. Since we know we are searching for the names of people, check the bubble next to **People** under **Reconcile each cell to an entity of one of these types**.
6. Click **Start Reconciling**.

Reconciliation can take a lot of time if you have a lot to look up. However, we only have 5 names to look up, so the service should work quickly.

Once the reconciliation has completed two Facets should be created automatically: **Author: Judgement** and **Author: best candidate's score**. Close the **best candidate's score** facet, but leave the **Judgement** facet open.

According to the Judgement facet, 12 of our values (Aristotle, Caesar, and Dante) matched and 7 (Aesop and Chaucer) did not.

Aristotle, Caesar, and Dante auto-matched because the judgement score on each search had a high enough probably that the VIAF match was correct. If we look at those values in the column, we'll see that they are now links that do indeed take us to the VIAF records for [Aristotle](https://viaf.org/viaf/7524651/), [Caesar](https://viaf.org/viaf/286265178/), and [Dante](https://viaf.org/viaf/97105654/).

Aesop's top match from VIAF is [Æsop](https://viaf.org/viaf/64013451/), which has a 60 percent (0.6) probability of being correct; the second-best match for Chaucer is, maddeningly, [Chaucer, Geoffrey](https://viaf.org/viaf/100185203/) at 100 percent probability. Aesop's non-match can be understood, but why no insta-match for Chaucer? Like a Tootsie Roll Pop, the world may never know.

In any case, these are the right records, so we will click the double checkmark icon next to them, which will match all identical cells to this same VIAF entity. In our facet, non-matches should disappear.

So we're done, right? **Nope.** All we have right now is the standard VIAF name form of the entity. This is good, but we still need to extract this name form to its own column, its identifier, or both.

On the Authors column, select **Edit column** > **Add column based on this column...**. In the transformation window, enter your GREL:
`'https://viaf.org/viaf/' + cell.recon.match.id + '/'`
This will not only extract the identifier for the entity, but create the full VIAF URI. Name this column **viafIDs**.

Once you have this new column, reconciliation data can be dispatched by selecting **Reconcile** > **Actions** > **Clear reconciliation data**.

## Data Enhancement

### Enriching with Web APIs

Depending on what you need or want from your data, it may be pertinent to enrich existing data from an outside source. Open web APIs are great for this, because Refine can send out such web API requests, and parse the results (especially if the response is in JSON, XML, and HTML).

What if we wanted our Schoenberg data to contain geolocational data for the auction houses that sold some of the manuscripts? That metadata could potentially fuel a discovery layer to see where most of the manuscripts were sold.

1. First, [download this Refine project](https://github.com/DLFMetadataAssessment/DLFMetadataQAWorkshop17/blob/master/OR-Data/Auction-Houses.openrefine.tar.gz?raw=true), which contains some of the addresses of auction houses found in our Schoenberg data.
2. Open a new browser tab and navigate to http://127.0.0.1:3333/, which will open a new Refine window. Click the lefthand **Import Project** tab and open *Auction-Houses.openrefine.tar.gz*. You should be looking at a previously worked on Refine project with two columns: *Company* and *Address*.
3. On the dropdown for *Address*, select **Add Column** > **Add column by fetching URLs**. Name this new column *JSON*.
4. Run this GREL expression: `"https://maps.googleapis.com/maps/api/geocode/json?address=" + escape(value,"url")`

The new column should be filled with JSON data from Google's Maps API. Now let's get the latitude and longitude coordinates for each address.

5. On the dropdown for *JSON*, select **Add Column** > **Add column based on this column**. Name this new column *Coordinates*.
6. Run this GREL expression: `value.parseJson().results[0]["geometry"]["location"]["lat"] + ',' + value.parseJson().results[0]["geometry"]["location"]["lng"]`

This new column is filled with parsed JSON data featuring the exact coordinates for each address.

### Cross, OpenRefine's VLOOKUP Function

Now that we have geo-coordinates, let's go back to our Schoenberg project and enrich the data with this new geographic information. To do this, we can use a **Cross** GREL, a command not unlike a VLOOKUP formula in Excel.

The structure of a Cross GREL expression looks like this:

`cell.cross("[Source Project]", "[Matching Column]")[0].cells["[Column Data We Want]"].value`

So, we will select **Add Column** > **Add column based on this column** on *PrimarySeller* and make a new column, *LatLng*, using this GREL expression:

`cell.cross("Auction-Houses", "Company")[0].cells["Coordinates"].value`

You should now have a new column of coordinates next to *PrimarySeller*.

## Getting Data Out of OpenRefine

When your data has been cleaned to your satisfaction, you can export it to a variety of different file formats. Be sure all text filters and facet windows are closed, and that you have joined any split multi-valued data before you export! Clicking on the **Export** button in the upper right corner of the screen will display the export file formats available. **Export Project**, on the other hand, will save a copy of your Refine project as a TAR archive that can be shared with other people and opened in other Refine installations.

Refine's **Templating** function allows you to export your data in a "roll your own" fashion. As Refine's wiki states, "this is useful for formats that we don't support natively yet, or won't support." Currently, Templating is set up to generate a single JSON document of your data by default; with a few changes, we can set up a template to get our enriched Schoenberg data back to an XML file. You'll need to fill out these spaces with the below template:

Prefix:
```
<?xml version="1.0" encoding="UTF-8"?>
<records>
[blank line break]
```

Row template:
```
   <record>
      <Duplicates>{{escape(cells["Duplicates"].value,"xml")}}</Duplicates>
      <ID>{{cells["ID"].value,"xml")}}</ID>
      <CatOrTranslateDate>{{escape(cells["CatOrTranslateDate"].value,"xml")}}</CatOrTranslateDate>
      <PrimarySeller>{{escape(cells["PrimarySeller"].value,"xml")}}</PrimarySeller>
      <LatLng>{{escape(cells["LatLng"].value,"xml")}}</LatLng>
      <SecondarySeller>{{escape(cells["SecondarySeller"].value,"xml")}}</SecondarySeller>
      <InstitutionOrCollection>{{escape(cells["InstitutionOrCollection"].value,"xml")}}</InstitutionOrCollection>
      <Buyer>{{escape(cells["Buyer"].value,"xml")}}</Buyer>
      <CatalogueID>{{escape(cells["CatalogueID"].value,"xml")}}</CatalogueID>
      <LotOrCat>{{escape(cells["LotOrCat"].value,"xml")}}</LotOrCat>
      <Price>{{escape(cells["Price"].value,"xml")}}</Price>
      <Currency>{{escape(cells["Currency"].value,"xml")}}</Currency>
      <Sold>{{escape(cells["Sold"].value,"xml")}}</Sold>
      <Source>{{escape(cells["Source"].value,"xml")}}</Source>
      <CurrentLocation>{{escape(cells["CurrentLocation"].value,"xml")}}</CurrentLocation>
      <Author>{{escape(cells["Author"].value,"xml")}}</Author>
      <AuthorVariant>{{escape(cells["AuthorVariant"].value,"xml")}}</AuthorVariant>
      <viafIDs>{{escape(cells["viafIDs"].value,"xml")}}</viafIDs>
      <Title>{{escape(cells["Title"].value,"xml")}}</Title>
      <Language>{{escape(cells["Language"].value,"xml")}}</Language>
      <Material>{{escape(cells["Material"].value,"xml")}}</Material>
      <Place>{{escape(cells["Place"].value,"xml")}}</Place>
      <LiturgicalUse>{{escape(cells["LiturgicalUse"].value,"xml")}}</LiturgicalUse>
      <Date>{{escape(cells["Date"].value,"xml")}}</Date>
      <Circa>{{escape(cells["Circa"].value,"xml")}}</Circa>
      <Artist>{{escape(cells["Artist"].value,"xml")}}</Artist>
      <Scribe>{{escape(cells["Scribe"].value,"xml")}}</Scribe>
      <Folios>{{escape(cells["Folios"].value,"xml")}}</Folios>
      <Columns>{{escape(cells["Columns"].value,"xml")}}</Columns>
      <Lines>{{escape(cells["Lines"].value,"xml")}}</Lines>
      <Height>{{escape(cells["Height"].value,"xml")}}</Height>
      <Width>{{escape(cells["Width"].value,"xml")}}</Width>
      <Binding>{{escape(cells["Binding"].value,"xml")}}</Binding>
      <Provenance>{{escape(cells["Provenance"].value,"xml")}}</Provenance>
      <Comments>{{escape(cells["Comments"].value,"xml")}}</Comments>
   </record>
```

Row separator:
```
[blank line break]
```

Suffix:
```
[blank line break]
</records>
```
