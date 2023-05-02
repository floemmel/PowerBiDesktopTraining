# Power BI Desktop Training Lab

This is a simple Power BI Desktop training lab to learn the basics of the tool. It will walk through the main steps that would be done when building a Power BI report. Some steps will be described with more details than others, just look around the UI, the answer should be close! 

Start by installing Power BI Desktop by following the [documentation](https://learn.microsoft.com/en-us/power-bi/fundamentals/desktop-get-the-desktop), if not done yet. Open a new report file and save it, which will create a .PBIX file. Power BI Desktop is free to download & use. 

## Data connection

In this step, we will connect to our data and import it into Power BI. It should take around 10 minutes. 

As source, we will use sample sales data provided via .csv files in the [Data](data/) folder: 
- [Products.csv](data/Products.csv) - Information about the products
- [France.csv](data/France.csv) - Sales data for France
- [Belgium.csv](data/Belgium.csv) - Sales data for Belgium
- [Austria.csv](data/Austria.csv) - Sales data for Austria

Note: The _Manufacturer.xlsx_ file is not required. 

To connect, you can either download the files and use the _Text/CSV_ connector to import the data, alternatively you can use the _Web_ connector and directly import from the link. For this to work, you need to select "View raw" after selecting a file in Github. 

![View raw in Github](https://user-images.githubusercontent.com/49025350/235483349-abd53ddd-ee94-4d64-8acd-fe6da182a5b2.png)

Select _Get data_ in Power BI Desktop (you will find it in the ribbon on the _Home_ tab) and search for the connector that you are looking for. As there is no authentication required, you can select _Anonymous_ when using the Web connector. The Text/CSV connector doesn't require authentication at all, as it connects to a local file on your device. Once confirmed, the Power Query Editor will open with a preview of the query. 

You will need to repeat it for all four files, alternatively to using _Get data_ on the ribbon you can also _New Source_ in the Power Query Editor. After following the steps, the four tables (queries) should be visible in your Power Query editor. 

<img width="144" alt="image" src="https://user-images.githubusercontent.com/49025350/235483775-48bc7894-13d3-479e-9b34-96b0c7109b2e.png">

Don't forget to save your file from time to time. While you are in the Power Query Editor, it will ask you if you want to apply changes, which you don't need to do right away, select _Apply later_:

<img width="345" alt="image" src="https://user-images.githubusercontent.com/49025350/235687970-b45d4d36-2b9e-4b28-9cbb-244ef6e6143d.png">

If you accidentally close the Power Query Editor, you can open it again by selecting _Transform data_ in the ribbon:

<img width="1128" alt="image" src="https://user-images.githubusercontent.com/49025350/235688503-53f182a5-2689-4ac7-8eba-e4d144f76ed6.png">


## Data transformation

In this step, we will transform the data to make it ready for the report building. It should take around 15 minutes. 

First, the different sales data tables need to be combined into one. As they have the same structure, you can simple append them into a single table. 
Start by selecting one of the Sales tables. Then select _Home -> Append Queries as New (selecting the arrow) -> Three or more tables_, then add the other three tables to the right side and click ok. 

<img width="525" alt="image" src="https://user-images.githubusercontent.com/49025350/235491824-b221aa71-e557-4216-9824-4171fd7332a0.png">

When asked about privacy levels, you can select _ignore_ or put all of them on _Public_ and continue. Pricacy levels in Power BI define whether you are allowed to combine multiple data sources. 

<img width="593" alt="image" src="https://user-images.githubusercontent.com/49025350/235491984-5e2a8520-6fd0-4ffe-9997-1f8a96a42649.png">

Rename the new query (the combination of the three tables) to "Sales" in the query properties or by right clicking the query and selecting _Rename_ from the menu. 

As you don't need the individual tables anymore, you can deactivate them by right clicking them and unchecking _Enable load_. Their names should now appear in italic font and they will not be loaded into the report file. 

In the _Sales_ table, format the _Revenue_ column as _Fixed Decimal_, which is the currency format. You can do that by selecting the format icon left of the column name or in the right-click menu under _Change type_.

You will also notice that some of the values in the _Country_ table are wrong, apparently someone made a typo when chosing the country. Fix it by replacing the value with the correct one, either in the source table (which you deactivated) or in the new _Sales_ table. 

Let's continue with the _Products_ table. You will see that the headers are not correct. Select _Home -> Use First Row as Headers_ to fix it. We now need to do some improvements to the actual data: 
- The _Product_ column currently contains both the product name and the category, separated by a "|". Split the column by the correct delimiter and rename the columns accordingly. 
- The _Category_ column contains a lot of missing values and is not required. Remove the column. 
- The _Price_ column contains the currency and the value. Split the column, rename it correctly and change the type if necessary. 

You should now have cleaned data. By selecting _View_ you can play around with the _Data Preview_ options such as _Column quality_ and _Column distribution_ to check if the quality is as expected. If you are happy, select _Home -> Close & Apply_. After a few seconds, the data should be loaded into Power BI Desktop. 

## Modeling and calculations

In this step you will create your data model by connecting the tables and create calculations. This should take around 10 mins. 

First of all, select the _Model view_ in Power BI Desktop. Power BI will automatically try to detect relationships between your tables, and should have linked them based on ProductID. You can double click on the relationship or select _Manage relationships_ to view it and do changes if necessary. In this example, no changes should be required. 

Go to the _Data view_ and check the imported data. Verify if the currencies are formatted correctly, if not you can select _Column tools_ and adapt the currency to the right format. Do this for the _Revenue_ column in Sales and the _Price_ column in Products. In the Sales table, select the _Country_ column and categorize it as Country in the Column tools. A little globe icon should appear next to the column name in the data pane. 

We will now create some calculations. Select the Sales table, then click _New measure_ in the _Table tools_ menu to add a measure. Create the following measures: 

```
Number of Sales = COUNTROWS(Sales)
```


```
Total Sales = SUM(Sales[Revenue])
```

Format _Total Sales_ with the right currency. 

Now select _Quick measure -> Calculations -> Month-over-month change_. Add _Total Sales_ as Base value, _Date_ as Date and click _Add_. This should add the folowing code to your table: 
```
Total Sales MoM% = 
IF(
	ISFILTERED('Sales'[Date]),
	ERROR("Time intelligence quick measures can only be grouped or filtered by the Power BI-provided date hierarchy or primary date column."),
	VAR __PREV_MONTH = CALCULATE([Total Sales], DATEADD('Sales'[Date].[Date], -1, MONTH))
	RETURN
		DIVIDE([Total Sales] - __PREV_MONTH, __PREV_MONTH)
)
```

Optional: As a last step, right click on _Product Category_ in the Products table and select _Create hierarchy_. Then right click on _Product -> Add to hierarchy_ and select the one you just created. 

## Build a simple report

In this step you will build a report. There will not be any detailed instructions, as you can play around if the visuals that you like and format them according to your preferences. However there will be a list of requirements according to the data 

Add the following visualizations on your first page: 
- Display the two measures _Total Sales_ and _Number of Sales_ as standalone values
- Create a map that shows the _Total Sales_ by _Country_ 
- A visual (you choose the type) that shows _Total Sales_ and _Total Sales MoM%_ by _Date_ (Power BI will automatically add the Date Hierarchy, which is also required for the MoM measure to display correctly)
- Add a filter for _Year_

Add a second page:
- Just add the _Decomposition Tree_ visual and resize it to fit the entire page. 
- Add _Total Sales_ to the _Analyze_ field
- Add the following fields to _Explain by_: Country, Product, Product Category, Date Hierarchy
- Note how you can expand the value by the different fields you added. This allows you to analyze a value and do a root cause analysis 

Optional: Now add a third page: 
- Add a filter on _Country_
- Add a visual that shows _Total Sales_ by _Date_
- Add a Matrix, that has the _Product Category Hierarchy_ as Rows, the _Date_ as columns and _Total Sales, Total Sales MoM%_ as values.

Matrix:

<img width="189" alt="image" src="https://user-images.githubusercontent.com/49025350/235499838-fbd6cfb8-abe2-4b3e-a180-1af28e9956c8.png">

You successfully built your first report, congratulations. Now play around with the formatting (you can chose different themes or adapt the current one by selecting _View -> Themes_ in the Ribbon) and see how the interactions between your visuals help you understand the data. 

## Finished early? 

If you finished early, you can try to implement the following features: 
- Add an option to _drill down_ from the Map to the Details page, by chosing a country
- Create a mobile layout for your report
- Connect to the last file (Manufacturing.xlsx) by downloading it and using the Excel connector. Transform it into a useable format (are the colums and rows reversed?) and create a new report page, showing Sales and/or Product information by Manufacturer

