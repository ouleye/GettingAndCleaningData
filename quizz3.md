Question 1
==========

```r
download.file('https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2Fss06hid.csv',destfile = './hid.csv')
data <- read.csv('./hid.csv')
```



Creation of a logical vector indicating how each row in the dataset respond or not to the conditions

```
agricultureLogical <- data$ACR == 3 & data$AGS == 6

# EX of result (not accurate) => head(agricultureLogical) : [1] FALSE  NA FALSE TRUE FALSE FALSE  NA FALSE TRUE FALSE
```



Display the indices where the elements of agricultureLogical == TRUE

```
which(agricultureLogical)
```
Result on  the dataset   [1]  125  238  262  470 ...


Question 2
==========

```r
download.file("https://d396qusza40orc.cloudfront.net/getdata%2Fjeff.jpg", destfile = "./image.jpg",mode = "wb")
vf <-readJPEG("./image.jpg",native = TRUE)
quantile(vf,probs=seq(0,1,0.1))
```


Question 2
==========

```r
library("data.table")
download.file("https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2FGDP.csv ", destfile = "./FGDP.csv")
download.file("https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2FEDSTATS_Country.csv ", destfile = "./FEDSTATS.csv")

fgdp <-data.table(read.csv("./FGDP.csv"))
setkey(fgdp,X)
fedstats <-data.table(read.csv("./FEDSTATS.csv"))
setkey(fedstats,CountryCode)

DATA <- merge(fedstats, fgdp, by.x=c("CountryCode") , by.y=c("x"))
```
