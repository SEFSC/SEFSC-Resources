---
title: "R, SAS, DBI and ROracle"
author: "James Primrose - OMI Data Management"
date: "6/27/2022"
output:
  html_document: default
  pdf_document: default
---

##### **NOTE:** While this document is mostly about R and ROracle, the 32 bit Oracle client is required for SAS and the steps to install that are in here also
##### [R] Steps needed for R 
##### [SAS] Steps needed for SAS


#### Prerequisites:
##### **NOTE:** It is strongly recommended that you update R-Core, R-Studio and Rtools to the *latest* versions.
##### **NOTE:** Be very aware of the version of R-Core you have installed. The version of Rtools you'll need depends on which version of R-Core you have installed.
```
You'll need:
1) R-Studio 2021.09.01 Build 372 or greater
2) R-Core 4.0 or greater (tested on 4.1.2 "Bird Hippie")
3) SEFSC Oracle Account (contact james.primrose@noaa.gov for an account)
Optional: 
4) SAS 9+ 32 bit
```

#### Downloads:
##### **NOTE:** You'll need to be on VPN to download the Oracle Instant Client .zip file.
##### **Oracle Instant Client: [SAS & R]** <http://software:software@secgs-radmb.nmfs.local/software/ICPKG.zip>
##### **MS Visual C++ Redist: [SAS & R]** <http://drive.google.com/file/d/1taRdxpT2esz70REUKkfER76NKwJi-t11/view?usp=sharing>
##### **ALT. www site - MS Visual C++ Redist (2017+): [SAS & R]** <https://docs.microsoft.com/en-us/cpp/windows/latest-supported-vc-redist>
##### **rtools40: [R]** <https://drive.google.com/file/d/16TxReyVFq0cU3flMMZRdR_mnWhPix6Mn/view?usp=sharing>
##### **ALT. www site - rtools40 & rtools42: [R]** <https://cran.r-project.org/bin/windows/Rtools/>



### 1) [SAS & R] [AS ADMIN] install files from above (in this order)
```
1) [SAS & R] Extract ICPKG.zip to C:\Oracle 
[Should result in C:\Oracle\intantclient and C:\Oracle\instantclient32]

2) [R] Install rtools40 (or rtools42 depending on which version of RStudio you have)

2b) [R] Copy C:\Oracle\instantclient\sdk\include\ociver.h to C:\Program Files\R\R-4.x.x\include\ociver.h
NOTE: Be careful to copy this ociver.h file into the version of R you're using (there may be many versions installed in C:\Program Files\R\)

3) [SAS & R] Install MS Visual C++ Redist

4) [SAS & R] Reboot computer three times
```
### 2) [SAS & R] [AS ADMIN] modify environment variables and create symbolic links
##### **NOTE:** You'll need to create 2 new environment variables and edit one, so keep this screen open until you're done with step 2c
##### 2a) ORACLE_HOME
```
A) Start -> Type "Advanced System Settings"
*or*
A) Start -> Settings
B)About -> Advanced System Settings (right side)
C) Click on Environment Variables (lower right)
D) System Variables (lower section) click on NEW
  Variable Name: ORACLE_HOME
  Variable Value: C:\Windows\System32\oracle
```

##### 2b) TNS_ADMIN
```
D) System Variables (lower section) click on NEW
  Variable Name: TNS_ADMIN
  Variable Value: %ORACLE_HOME%\network\admin
```

##### 2c) Path
```
D) In the System Variables (lower section) click on Path and EDIT
E) Edit the "Path" system variable. 
E1) Remove anything you see here relating to Oracle
E2) NEW: C:\Oracle\instantclient32
E3) NEW: C:\Oracle\instantclient
```
##### 2d) Symbolic Links
```
Open Command Prompt (CMD) as Administrator and issue the following commands to make symoblic links:

mklink /j c:\windows\system32\oracle C:\Oracle\instantclient
mklink /j c:\windows\sysWOW64\oracle c:\Oracle\instantclient32
```

### 3) [SAS & R] Verify SQLplus and path settings

##### 3a) Verify SQLPlus
```
Open Command Prompt (CMD) and type: sqlplus

You should see something like:

SQL*Plus: Release 19.0.0.0.0 - Production on Tue Jun 7 12:04:55 2022
Version 19.11.0.0.0

Copyright (c) 1982, 2020, Oracle.  All rights reserved.

Enter user-name:

You don't need to login, just close the CMD window or type CTRL+C
```

##### 3b) Verify Path
###### Verify ORACLE_HOME exists
###### Verify ORACLE_HOME set to **C:\\Windows\\System32\\oracle**
###### Verify TNS_ADMIN exists
###### Verify TNS_ADMIN set to **C:\\Windows\\System32\\oracle\\network\\admin**
###### Verify Path includes **C:\\Oracle\\instantclient**
###### Verify Path includes **C:\\Oracle\\instantclient32**
```
Open Command Prompt (CMD) and type: set
Verify: 
1) ORACLE_HOME path variable exists and is pointing to C:\Windows\System32\oracle
2) Path variable exists (it will be a long string of paths) and includes both C:\Oracle\instantclient and also C:\Oracle\instantclient32
3) Either: depends on which version of R-core is installed: 
R-core <= 4.2 will need Rtools40
R-core >= 4.2 will need Rtools42
  A) RTOOLS40_HOME path variable exists and is set to C:\rtools40
  B) RTOOLS42_HOME path variable exists and is set to C:\rtools42
```

### 4) [R] Launch R-Studio and write environ file
##### **NOTE:** localadmin no longer needed.
##### Depending on which version of Rtools are installed either:
```
write('PATH="${RTOOLS40_HOME}\\usr\\bin;${PATH}"', file = "~/.Renviron", append = TRUE)
*or*
write('PATH="${RTOOLS42_HOME}\\usr\\bin;${PATH}"', file = "~/.Renviron", append = TRUE)
```

### 5) [R] Verify Rtools install in RStudio
##### Open R-Studio and enter the following:
```
Sys.which("make")
```
###### Should return path of RTOOLS set in Step 3b
###### Either:
###### "C:\\\\rtools40\\\\usr\\\\bin\\\\make.exe"
###### "C:\\\\rtools42\\\\usr\\\\bin\\\\make.exe"

### 6) [R] Install DBI and ROracle in R-Studio
##### Launch R-Studio and run the following:
```
Sys.setenv(
  'ORACLE_HOME' = 'C:/Oracle/instantclient/',
  'OCI_INC'     = 'C:/Oracle/instantclient/sdk/include',
  'OCI_LIB64'   = 'C:/Oracle/instantclient/'
)
```
###### and then
```
install.packages("DBI")
```
###### and then
```
install.packages("ROracle")
```
##### **NOTE:** ROracle install will pop-up a window to ask if you'd like to compile from source. Yes, you do.
##### **NOTE:** This is where most errors will occur. If you run into an error here, please carefully copy it down and email the error to: james.primrose@noaa.gov  


### 7) [R] Test ROracle connection in R-Studio
##### If everything above is completed and all your testing works, you can now test using the following code in R-Studio. The user and password here are a read-only account on SECPR that has some dummy tables we can use to test these connections. 
##### **NOTE:** Here you can see the user and pass are in plain text. You'd not want to do this with production code. 
```
Sys.setenv(
  'ORACLE_HOME' = 'C:/Oracle/instantclient/',
  'OCI_INC'     = 'C:/Oracle/instantclient/sdk/include',
  'OCI_LIB64'   = 'C:/Oracle/instantclient/'
)

library(ROracle)

un <- "rotest"
pw <- "rotest"
drv <- dbDriver("Oracle")
con <- dbConnect(drv, un, pw, dbname = "SECPR")
dbGetQuery(con,"select * from employees")
dbDisconnect(con)
```

### 8) [SAS] Test SAS
##### This section could use some better proc sql examples
```
proc sql;
 connect to oracle ( user=rotest orapw=rotest path="@SECPR");
 create table libref.dataset_name as select * from connection to
 oracle ( select * from employees );
disconnect from oracle;
quit;
```








