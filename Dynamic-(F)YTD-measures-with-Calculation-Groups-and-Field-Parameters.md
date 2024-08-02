# [How To Create Dynamic (Fiscal/) YTD measures for any selected metric in Power BI ?](youtubelink)

Open source ["Contoso Dataset"](https://www.microsoft.com/en-us/download/details.aspx?id=18279&msockid=3147878eac3f607d1fc995f2ad956136) to follow along. 

Pre-requisite : Create a couple of metrics such as: Sales $, Cost $, Profit $, Units Sold etc. 


## Step 1.

1. Create a calculation group called "Time Inteliigence" using [Tabular Editor 2](https://tabulareditor.github.io/TabularEditor/) by left-clicking on the "Tables" folder -> "Create New" -> "Calculation Group" or Alternatively, using the shortcut "Alt + 7" . 
2. Then create the calculation items below:

```
Calculation Item 1 : Original Value = Selectedmeasure()
```

```
Calculation Item 2 : This (Fiscal/) Year  YTD = 

Calculate( 
    selectedmeasure(), // Metric Value selected in the report
    Datesytd( DimDate[Date], "3/31" )  // Assuming, Fiscal Year is from 1 April to 31 March. If a calendar year is preferred, delete the optional parameter in DatesYTD.
)

```

```
Calculation Item 3 : Last (Fiscal/)Year YTD = 

var _LY_FYTD = 

Calculate
(
        Calculate( 
               selectedmeasure(),
               Dateadd( DimDate[Date], -12 , Month) // For Last Year's value
              ),
    Datesytd( DimDate[Date], "3/31" )  // YTD pattern, as shown above
)

var _output = 
if( 
isblank( selectedmeasure() ),  
blank(), // Do not display rows where "This Year's" value is not visible.
_LY_FYTD
)

return 

_output
```



```
Calculation Item 4 : YTD Variance $ (TY vs LY) = 

// leverage to previously created calculation item

var _TY_FYTD =
calculate(
selectedmeasure(),
'Test_TimeIntelligence'[Name] = "TY Fiscal YTD" 
) 

// leverage to previously created calculation item

var _LY_FYTD =
calculate(
selectedmeasure(),
'Test_TimeIntelligence'[Name] = "LY Fiscal YTD" 
)

var _difference = _TY_FYTD-_LY_FYTD

var _output = if( isblank(_LY_FYTD), blank(), _difference) // do not provide results if there is no LY value, else difference will be skewed

Return 
_output
```


## Step 2. Create a Field Parameter:

1. Click on the "Modeling" Tab
2. Click on "New parameter" -> "Fields". /
   ![](/assets/img/Field-Parameter.png)

4. Select the metrics you would like to show the YTD measures for.
5. Click on create
   ![](/assets/img/Field-Parameter1.png)


