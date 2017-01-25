# Watson Data Platform - Introduction tutorial for Data Engineer
These instructions (and some related files, if any) are provided at:  https://github.com/jruponen/wdp_de_tutorial


###This tutorial in short:
1. Set up a simple Data Lake in Bluemix
2. Refine & ingest data with Data Connect
3. Catalog the data in Data Science Experience


For more information about the IBM Watson Data Platform, go to:  
https://www.ibm.com/analytics/us/en/watson-data-platform/platform.html


##STEP 1 - SET UP A SIMPLE DATA LAKE
(10 min)

	1. Login to http://bluemix.net
	- Make sure you are using "US South" region.

	2. Open Bluemix Catalog
	- Press "Catalog" on the top row

	3. Provision a dashDB analytic data store
	- Type "dashDB" in seach field and click "dashDB for Analytics" icon

	4. Name the new dashDB service
	- Service name: "dashDB for Analytics"
	- Press [Create]

	5. Go back to Bluemix Catalog and provision a "Data Connect" service
	- Type "Data Connect" in search field and then click "Data Connect" icon

	6. Name the new Data Connect service
	- Service name: "Data Connect"
	- Credential name: <leave to default>
	- Press [Create]

	7. Go back to your Bluemix dashboard
	- Click on the "IBM Bluemix" logo in the top left corner to get back to dashboard

	8. Open dashDB service
	- Click on your "dashDB" service to open it.
	- On the welcome page, press the green [Open] button to launch the service

	9. Observe the dashDB credentials for later use
	- Close the "Console Quick Tour" dialog, if it is opened
	- On the left navigation, select "Connect"
	- You can either Copy-Paste the "Database details" aside, or you can leave this browser tab open to come back here later for this information.

End of STEP 1

Congratulations, you have now set up the following:
- dashDB as an analytic data store
- Data Connect for data ingestion

Note: Should you also need a raw data store for collecting data, you may want to try out Cloudant NoSQL data store for simplicity. The easiest way to set up a Cloudant NoSQL data store along with data collection tools is to set up a "Note-RED Starter" boilerplate (which is also found in the Bluemix Catalog).


##STEP 2 - REFINE & INGEST DATA
(15 min)

	1. Open "Data Connect" service
	- In the Bluemix console, under "Services", click the "Data Connect" service to open it
	- Then press the green [Launch] button to open the service

	2. Create connection to your dashDB
	- On the left side navigation, under "Tasks", select "Create Connection"
	- Select "IBM dashDB" from the list
	(also notice the other data source options listed)

	3. Enter dashDB connection details 
	- Enter connection information for your dashDB (copy-paste from dashDB console)
	  Connection Name:	"dashDB for Analytics"
	  Description:		"Analytic Data Store for Data Science"
	  Hostname:		<your dashDB hostname>
	  Database:		BLUDB
	  Username:		<your dashDB username>
	  Password:		<your dashDB password>

	  Notice the "Secure Gateway" option. This is needed to get access to data stores behind a firewall. However, do NOT select it at this point.

	- When done, press the green [Create Connection] button.

	4. Let's create the first data "Refine & Copy" activity to ingest some data
	- On the left navigation, under "Tasks", select "Refine & Copy"
	- In the top row, press [Pencil] and edit the name of the activity: "Sales Orders and Products"
	- Press the round green OK symbol to set the name.

	5. Point to the source data
	- The source data we need, happens to be in the "Data Connect" already, as CSV files. Lets reuse them.
	- On the source data list on left, select "Samples"
	- Select both files, "GoSales ORDERS.csv" and "GoSales PRODUCT.csv"
	- Notice, that further on the right on that line you may press [binoculars] to preview the data
	- Once both files are selected press the green [Refine Data] button

	6. "Refine" screen will open
	- In this screen you can do multiple operations to data, incl:
	  Cleanse the data
	  Standardize data in the columns
	  Re-organize the data (like Join, Split, Match & Manage, etc...

	7. Check the quality of data
	- You currently have "GoSales ORDERS.csv" data selected (in the bottom row, left tab is selected).
	- From top row, besides the Undo/Redo buttons, press the [Toggle Metrics] button
	- Notice the data quality indicators under column headers
	- Also notice that you can select/de-select column headers, changing the target for details & operations to either a column or the entire table

	8. Let's Join the data
	- Having no columns selected, press "Join data sets" under "Organize" on the right
	- Press "Select join key" under "A. GoSales ORDERS.csv" and select "PRODUCT NUMBER".
	- Press "Select join key" under "B. GoSales PRODUCTS.csv" and select "PRODUCT NUMBER".
	- A connection line should now appear between the tables
	- Under tables, leave "Matches are case sensitive" selected
	- For Join type, select "Matching rows" (= inner join).
	- Press [Join] button on top right.

	9. Let's define the target
	- Press the green [Next] button on top right
	- With "Connections", select "dashDB for Analytics" (or whatever you named your connection)
	- Under "Schemas", select "DASHXXXX"
	- Leave "Table action" to a setting "Recreate the table"
	- IMPORTANT: On the right most column, press [Pencil] icon and rename the target tables as: "GOSALES-ORDERS-PRODUCTS"
	- Press the green circle "Ok" button to accept it
	- Finally, press the green [RUN] button to run this action immediately
	  Notice, that you could have also set schedule to run this at later time

	10. Monitor activity progress
	- You are now at the "Activities" page where you see all your activities
	- The "status" shows "running", until your activity is completed
	- To see more details, click menu icon on your activity [three dots icon] and select "Details"
	- On the detailed page that opens, you can then:
	  Monitor your activity in more detail
	  View what the action was about
	  Open the activity for further modifications, etc

	11. Verify target table in dashDB
	- Go back to browser tab where you left your dashDB console open
	- Select "Tables" on the left navigation
	- In the "Table name" drop down list, select "GOSALES-ORDERS-PRODUCTS"
	- Table Definition (the schema) will show.
	- To see the data, press "Browse Data" tab.

	12. Run SQL script to check the data
	- Select "Run SQL" on the left navigation and copy-paste the following script in the editor:
	  IMPORTANT: Replace the "DASHXXXX" with your corresponding schema name!

	  SELECT COUNT("ORDER NUMBER") AS ORDERS, "PRODUCT NAME"
	  FROM "DASHXXXX"."GOSALES-ORDERS-PRODUCTS"
	  GROUP BY "PRODUCT NAME"
	  ORDER BY COUNT("ORDER NUMBER") desc;

	- Then press [Run All].
	- To see the result, press "Data" link in the results window (next to "Log" link).

	13. Results
	- You should see:

	  ORDERS	PRODUCT NAME
	  13		Maximus
	  11		Infinity
	  11		Retro
	  11		Zone
	   9		Sam
	   9		Hawk Eye
	   6		Polar Sun
	   6		Inferno
	   6		Polar Sports
	   4		Lux


End of STEP 2

Congratulations, you have now refined & joined new data and ingested it to the data lake.
If you got any errors in the last step, verify your SQL script (did you change the schema name?).


## STEP 3 - CATALOG THE DATA
(10 min)

	1. Login to  http://datascience.ibm.com

	2. Define a connection for dashDB
	- Click the drop-down menu on top left corner and select "Connections"
	- Press the round [plus-sign] to create new connection and enter the following:

	  Connection name:	"dashDB for Analytics"
	  Description:		"Data for Data Scientists"
	  Service Category:	"Data Service"
	  Service Instance:	<Select your dashDB instance>
	  Database:		BLUDB

	- Press [Create].

	- Note: As you can see, your Cloud Data Services are automatically located. As with the "Data Connect", you only need to define each data source here once.

	3. Catalog data for Data Scientists
	- Click the "Data Science Experience" dropdown list and select "Data Hub"

	4. If you don't have catalog yet, create a new one
	- Click the round [plus-sign] on top right and select "Create Catalog"
	- Enter the following:

	  Catalog name:		"GO Catalog"
	  Description:		"GO DW Data for Data Scientists"

	- Press [Create]

	5. Create new data set in the Catalog
	- Under the "Create Data Set" press [Start]
	- Select "Stored Connection"
	- Notice the "Local File" option, which is useful for data scientists to bring their own data, ad hoc
	- Under "Connection", select the "dashDB for Analytics" connection you just defined
	- Press [Connect]
	- Select your schema "DASHXXXX"
	- Press [Select Tables] and in the dialog that opens, select the whole table "GOSALES-ORDERS-PRODUCTS".
	- Press [Done]
	- Press [Upload]

	6. Monitor data movement progress
	- You are now looking at the "Monitor your data movement" progress screen
	- The data is now being sandboxed for data scientists and other user groups

	7. View summary of data catalogging results
	- Press [View Report] to see summary.
	- Notice here, that a new area "datalake instance" was created for sandboxing the data.

	8. Browse the new data in the Catalog
	- Select "Browse" tab and click on the data set you just added
	- Preview the data and also take a look on the other tabs (don't change anything)

	9. Review Access to data
	- On the top row, select "Access Control" tab
	- By pressing the round [plus-sign] you can now invite other people to collaborate around this data

	10. See the Data Hub Dashboard in DSx
	- Press the [Dashboard] on top row
	- This is the statistics of the data and assets in this data Catalog.


End of STEP 3

Congratulations! You have now finnished the WDP exercise as Data Engineer by catalogging new data for data scientists to re-use.
From here on, Data Engineers would create new projects around the catalogs and iteratively analyze data using Notebooks for machine learning with Spark and R. In addition to data in catalog, they may also load their own data ad hoc, or capture streaming data.