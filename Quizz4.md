Question 1
----------
>The American Community Survey distributes downloadable data about United States communities. Download the 2006 microdata survey about housing for the state of Idaho using download.file() from here:

>https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2Fss06hid.csv
>and load the data into R. The code book, describing the variable names is here:

>https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2FPUMSDataDict06.pdf

>Apply strsplit() to split all the names of the data frame on the characters "wgtp". What is the value of the 123 element of the resulting list?


```
download.file('https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2Fss06hid.csv', destfile = './datasshid.csv')
datasshid <-read.csv('./datasshid.csv')
```

```
names(datasshid)
```

```
a <-strsplit(names(datasshid),'wgtp')
 (return a list)
```

```
a[[123]]
[1] ""   "15"
```


Question 2
----------
>Load the Gross Domestic Product data for the 190 ranked countries in this data set:

>https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2FGDP.csv

>Remove the commas from the GDP numbers in millions of dollars and average them. What is the average?

>Original data sources: http://data.worldbank.org/data-catalog/GDP-ranking-table 


```
download.file('https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2FGDP.csv', destfile = './fgdp.csv')

```

After taking a look at the file,I can see that the 4 rows of csv can be skipped and only the 190 next rows are relevant as per the question informations :
   >Load the Gross Domestic Product data for the **190** ranked countries in this data set: 

I also prevent the read.csv function from creating variable as factors.
```
fgdp <- read.csv('./fgdp.csv', skip=4, nrows=190, stringsAsFactors = FALSE)
```


I had colnames to the fgdp dataframe, in order to work more easily
```
colnames(fgdp) <-  c("CountryCode", "rankingGDP",'RandomInfo', "Long.Name", "gdp")
```


```
str(fgdp)
'data.frame':	190 obs. of  10 variables:
 $ CountryCode: chr  "USA" "CHN" "JPN" "DEU" ...
 $ rankingGDP : int  1 2 3 4 5 6 7 8 9 10 ...
 $ RandomInfo : logi  NA NA NA NA NA NA ...
 $ Long.Name  : chr  "United States" "China" "Japan" "Germany" ...
 $ gdp        : chr  " 16,244,600 " " 8,227,103 " " 5,959,718 " " 3,428,131 " ...
 $ NA         : chr  "" "" "" "" ...
 $ NA         : logi  NA NA NA NA NA NA ...
 $ NA         : logi  NA NA NA NA NA NA ...
 $ NA         : logi  NA NA NA NA NA NA ...
 $ NA         : logi  NA NA NA NA NA NA ...
```


The structure shows that the gdp variable is a string
I will then, remove the commas in gdp variable and transform it as numeric
```
fgdp$gdp <- as.numeric(gsub(',','',fgdp$gdp))
```


Calculate the mean
```
mean(fgdp$gdp, na.rm = TRUE)
[1] 377652.4
```




Question 3
-----------
>In the data set from Question 2 what is a regular expression that would allow you to count the number of countries whose name begins with "United"? Assume that the variable with the country names in it is named countryNames. How many countries begin with United?

`grep("^United",countryNames), 3`


Question 4
-----------

>Load the Gross Domestic Product data for the 190 ranked countries in this data set:

>https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2FGDP.csv

>Load the educational data from this data set:

>https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2FEDSTATS_Country.csv

>Match the data based on the country shortcode. Of the countries for which the end of the fiscal year is available, how many end in June?

>Original data sources:
>http://data.worldbank.org/data-catalog/GDP-ranking-table
>http://data.worldbank.org/data-catalog/ed-stats

we will use the fgdp data frame  from question 2
```
download.file('https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2FEDSTATS_Country.csv', destfile = './fedstats.csv')
fedstats <- read.csv('./fedstats.csv',stringsAsFactors = FALSE)
```

Transformation into data.table format and merge into one dataset.
```
library("data.table")
fgdp <- data.table(fgdp)
fedstats <- data.table(fedstats)

setkey(fedstats,CountryCode)
setkey(fgdp,CountryCode)

data <- merge(fedstats,fgdp)
View(data)
```

By looking at the data, I can see that info on the fiscal year end is indicated in the "Special.Notes" variable
I will subset this variable CountryCode and Special.Notes to create a new df and work on it with dplyr package using 
chaining.
```
library("dplyr")
datadplyr <- tbl_df(data)
 v <- datadplyr %>% select (CountryCode,Special.Notes)
                %>% filter (grepl("Fiscal year end: June",Special.Notes) == "TRUE")
                
 nrow(v)
[1] 13
```


Question 5
-----------
>You can use the quantmod (http://www.quantmod.com/) package to get historical stock prices for publicly traded companies on the NASDAQ and NYSE. Use the following code to download data on Amazon's stock price and get the times the data was sampled.

  ```
   library(quantmod)
   amzn = getSymbols("AMZN",auto.assign=FALSE)
   sampleTimes = index(amzn) 
 ```

>How many values were collected in 2012? How many values were collected on Mondays in 2012?

```
library(quantmod)
amzn = getSymbols("AMZN",auto.assign=FALSE)
sampleTimes = index(amzn)

library("data.table")
Q5 <- data.table(sampleTimes)

library('dplyr')
Q5 <- mutate(Q5, days= weekdays(sampleTimes), year= year(sampleTimes))

addmargins(table(Q5$days,Q5$year))
          2007 2008 2009 2010 2011 2012 2013 2014 2015  Sum
  jeudi      51   50   51   51   51   51   50   50    8  413
  lundi      48   48   48   47   46  *47*  48   48    6  386
  mardi      50   52   52   52   52   50   52   52    8  420
  mercredi   51   53   52   52   52   51   51   52    8  422
  vendredi   51   50   49   50   51   51   51   50    8  411
  Sum       251  253  252  252  252 *250* 252  252   38 2052

```
