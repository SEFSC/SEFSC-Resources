---
title: "Installing ROracle"
author: "James Primrose - SEFSC OMI Data Management"
date: "2021-12-02"
output: html_document
---
#### Prereq:
```
R-Studio 2021.09.1 Build 372 or greater
R-Core 4.1 or greater (tested on 4.1.2 "Bird Hippie")
SEFSC Oracle Account (Contact james.primrose@noaa.gov for an account)
```
#### Prep downloads:
```
Install MS Visual C++ Redist: 
https://drive.google.com/file/d/1taRdxpT2esz70REUKkfER76NKwJi-t11/view?usp=sharing

Install RTools 4.0: 
https://drive.google.com/file/d/16TxReyVFq0cU34lMMZRdR_mnWhPix6Mn/view?usp=sharing

Install 19c_11 Instant Client:
https://drive.google.com/file/d/1J7ZT_slfAz0cxEevvYFT2NgXRcCLn6eM/view?usp=sharing
```
#### 1) [As Admin] remove all previous Oracle installation folders. 
#### 2) [AS Admin] install files from above (in this order)
```
Unzip ic1911.zip to C:\  [Should result in C:\Oracle\instantclient_19_11 ]
Install rtools40
Install MS Visual C++ Redist
Reboot computer three times (Reboot is not optional)
```
#### 3) [As Admin] modify environment variables

```
Click on Start button and type: "Advanced System Settings"
Click on Environment Variables (lower right)
System Variables (lower section) click on New...
Variable Name: ORACLE_HOME
Variable Value: C:\Oracle\instantclient_19_11
```
#### 4) [As non-admin / user] Launch R-Studio and write environ file
##### NOTE: Admin not needed beyond this point.
##### NOTE: Should result in a .Renviron file in C:\\Users\\%username%\\Documents\\.Renviron
```
write('PATH="${RTOOLS40_HOME}\\usr\\bin;${PATH}"', file = "~/.Renviron", append = TRUE)
```
#### 5) Verify environment file in R-Studio
##### NOTE: Should return the path of the make.exe in the RTOOLS40 home: "C:\\\\rtools40\\\\usr\\\\bin\\\\make.exe"
```
Sys.which("make")
```
#### 6) Confirm again and install jsonlite from source inside R-Studio
```
install.packages("jsonlite", type = "source")
```
#### 7) Install DBI and ROracle from source inside R-Studio
```
install.packages("DBI")
install.pacakges("ROracle")
```
#### 8) Set env and test connection to Oracle in R-Studio
##### NOTE: Replace Oracle_username, Oracle_password & test_table with real values
```
Sys.setenv(
  'ORACLE_HOME' = 'C:/Oracle/instantclient_19_11/',
  'OCI_INC'     = 'C:/Oracle/instantclient_19_11/sdk/include',
  'OCI_LIB64'   = 'C:/Oracle/instantclient_19_11/'
)

un <- Oracle_username
pw <- Oracle_password
library(ROracle)
drv <- dbDriver("Oracle")
con <- dbConnect(drv, un, pw, dbname = "SECPR")
dbGetQuery(con,"select count(*) from test_table")
dbDisconnect(con)
```
