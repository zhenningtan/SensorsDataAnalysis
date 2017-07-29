Introduction
------------

SensorsData.cn is a Chinse website that provides user behavior analysis
for customers. Here, I am going to clean the website log data for
SensorsData and analyze user activities on this website. My goal is to
discover interesting pattern and propose business insights to improve
user application. This document is the first part, containing raw data
transformation and cleaning.

Load libraries
--------------

    library(jsonlite)
    library(plyr)

Load data and convert JSON file into csv format
-----------------------------------------------

    f <- readLines(con = "sensorswww_data.txt", encoding = "UTF-8")
    n <- length(f)
    n # total number of lines 

    data <- lapply(f[1:n-1], fromJSON) # the last line is incomplete
    data <- lapply(data, as.data.frame)
    df <- do.call(rbind.fill, data)

    detach(package:plyr) # remove plyr to prevent conflict with dplyr used later in analysis

    dim(df)
    head(df)

    write.csv(df, "sensors_all_data.csv", row.names=FALSE, fileEncoding = "UTF-8")

Data exploration: identify useful records for analysis
------------------------------------------------------

### Read in data file

    df <- read.csv("sensors_all_data.csv", stringsAsFactors = FALSE)
    dim(df)

    ## [1] 51086    70

    head(df)

    ##                                distinct_id lib..lib lib..lib_method
    ## 1 595466e9a8e733434ce08de16e927d985e0b5d48       js            code
    ## 2 9939d3e087bca29c42334d96dccd25ca0e06652a       js            code
    ## 3 9939d3e087bca29c42334d96dccd25ca0e06652a       js            code
    ## 4 9939d3e087bca29c42334d96dccd25ca0e06652a       js            code
    ## 5 9939d3e087bca29c42334d96dccd25ca0e06652a       js            code
    ## 6 595466e9a8e733434ce08de16e927d985e0b5d48       js            code
    ##   lib..lib_version properties..os properties..model properties..os_version
    ## 1           1.6.20        windows                pc                    6.1
    ## 2           1.6.20           <NA>              <NA>                     NA
    ## 3           1.6.20        windows                pc                   10.0
    ## 4           1.6.20        windows                pc                   10.0
    ## 5           1.6.20        windows                pc                   10.0
    ## 6           1.6.20        windows                pc                    6.1
    ##   properties..screen_height properties..screen_width properties..lib
    ## 1                       800                     1280              js
    ## 2                        NA                       NA            <NA>
    ## 3                       768                     1366              js
    ## 4                       768                     1366              js
    ## 5                       768                     1366              js
    ## 6                       800                     1280              js
    ##   properties..lib_version properties..browser properties..browser_version
    ## 1                  1.6.20              chrome                          56
    ## 2                    <NA>                <NA>                          NA
    ## 3                  1.6.20              chrome                          56
    ## 4                  1.6.20              chrome                          56
    ## 5                  1.6.20              chrome                          56
    ## 6                  1.6.20              chrome                          56
    ##       properties..latest_referrer properties..latest_referrer_host
    ## 1                                                                 
    ## 2                            <NA>                             <NA>
    ## 3                                                                 
    ## 4                                                                 
    ## 5                                                                 
    ## 6 https://www.baidu.com/baidu.php                    www.baidu.com
    ##   properties..latest_utm_source properties..latest_utm_medium
    ## 1                         baidu                           cpc
    ## 2                          <NA>                          <NA>
    ## 3                          <NA>                          <NA>
    ## 4                          <NA>                          <NA>
    ## 5                          <NA>                          <NA>
    ## 6                         baidu                           cpc
    ##   properties..latest_utm_campaign
    ## 1        <U+901A><U+7528><U+8BCD>
    ## 2                            <NA>
    ## 3                            <NA>
    ## 4                            <NA>
    ## 5                            <NA>
    ## 6        <U+901A><U+7528><U+8BCD>
    ##                      properties..latest_utm_content
    ## 1 <U+901A><U+7528>-<U+7528><U+6237><U+753B><U+50CF>
    ## 2                                              <NA>
    ## 3                                              <NA>
    ## 4                                              <NA>
    ## 5                                              <NA>
    ## 6 <U+901A><U+7528>-<U+7528><U+6237><U+753B><U+50CF>
    ##        properties..latest_utm_term properties._latest_ch
    ## 1 <U+7528><U+6237><U+753B><U+50CF>                  demo
    ## 2                             <NA>                  <NA>
    ## 3                             <NA>                  demo
    ## 4                             <NA>                  demo
    ## 5                             <NA>                  demo
    ## 6 <U+7528><U+6237><U+753B><U+50CF>                  <NA>
    ##      properties._session_referrer properties._session_referrer_host
    ## 1 https://www.baidu.com/baidu.php                     www.baidu.com
    ## 2                            <NA>                              <NA>
    ## 3                                                                  
    ## 4                                                                  
    ## 5                                                                  
    ## 6 https://www.baidu.com/baidu.php                     www.baidu.com
    ##                                                                                                                                                                                                properties.session_page_url
    ## 1 https://www.sensorsdata.cn/?utm_source=baidu&utm_medium=cpc&utm_term=%E7%94%A8%E6%88%B7%E7%94%BB%E5%83%8F&utm_content=%E9%80%9A%E7%94%A8%2D%E7%94%A8%E6%88%B7%E7%94%BB%E5%83%8F&utm_campaign=%E9%80%9A%E7%94%A8%E8%AF%8D
    ## 2                                                                                                                                                                                                                     <NA>
    ## 3                                                                                                                                                                                          https://sensorsdata.cn/?ch=demo
    ## 4                                                                                                                                                                                          https://sensorsdata.cn/?ch=demo
    ## 5                                                                                                                                                                                          https://sensorsdata.cn/?ch=demo
    ## 6 https://www.sensorsdata.cn/?utm_source=baidu&utm_medium=cpc&utm_term=%E7%94%A8%E6%88%B7%E7%94%BB%E5%83%8F&utm_content=%E9%80%9A%E7%94%A8%2D%E7%94%A8%E6%88%B7%E7%94%BB%E5%83%8F&utm_campaign=%E9%80%9A%E7%94%A8%E8%AF%8D
    ##                     properties.pageUrl properties.pageStayTime
    ## 1      https://sensorsdata.cn/?ch=demo                   5.692
    ## 2                                 <NA>                      NA
    ## 3                                 <NA>                      NA
    ## 4      https://sensorsdata.cn/?ch=demo                      NA
    ## 5      https://sensorsdata.cn/?ch=demo                      NA
    ## 6 https://www.sensorsdata.cn/demo.html                  21.291
    ##   properties.pagePosition properties..is_first_day
    ## 1                       2                     TRUE
    ## 2                      NA                       NA
    ## 3                      NA                     TRUE
    ## 4                      NA                     TRUE
    ## 5                      NA                     TRUE
    ## 6                       1                     TRUE
    ##   properties..is_first_time  properties..ip             type       event
    ## 1                     FALSE  219.135.131.99            track index_leave
    ## 2                        NA            <NA> profile_set_once        <NA>
    ## 3                      TRUE 111.204.198.242            track   $pageview
    ## 4                     FALSE 111.204.198.242            track    btnClick
    ## 5                     FALSE 111.204.198.242            track    btnClick
    ## 6                     FALSE  219.135.131.99            track  demo_leave
    ##      X_nocache         time properties..first_visit_time
    ## 1 6.543924e+11 1.488791e+12                         <NA>
    ## 2 3.040563e+12 1.490958e+12      2017-03-06 17:04:10.999
    ## 3 9.587553e+12 1.488791e+12                         <NA>
    ## 4 6.529371e+11 1.488791e+12                         <NA>
    ## 5 8.207408e+12 1.488791e+12                         <NA>
    ## 6 4.967393e+12 1.488791e+12                         <NA>
    ##   properties..first_referrer properties..first_browser_language
    ## 1                       <NA>                               <NA>
    ## 2                                                         zh-CN
    ## 3                       <NA>                               <NA>
    ## 4                       <NA>                               <NA>
    ## 5                       <NA>                               <NA>
    ## 6                       <NA>                               <NA>
    ##   properties..first_referrer_host properties.ch properties..referrer
    ## 1                            <NA>          <NA>                 <NA>
    ## 2                                          demo                 <NA>
    ## 3                            <NA>          demo                     
    ## 4                            <NA>          <NA>                 <NA>
    ## 5                            <NA>          <NA>                 <NA>
    ## 6                            <NA>          <NA>                 <NA>
    ##   properties..referrer_host                 properties..url
    ## 1                      <NA>                            <NA>
    ## 2                      <NA>                            <NA>
    ## 3                           https://sensorsdata.cn/?ch=demo
    ## 4                      <NA>                            <NA>
    ## 5                      <NA>                            <NA>
    ## 6                      <NA>                            <NA>
    ##   properties..url_path
    ## 1                 <NA>
    ## 2                 <NA>
    ## 3                    /
    ## 4                 <NA>
    ## 5                 <NA>
    ## 6                 <NA>
    ##                                                                                                                                            properties..title
    ## 1                                                                                                                                                       <NA>
    ## 2                                                                                                                                                       <NA>
    ## 3 <U+795E><U+7B56><U+6570><U+636E> | Sensors Data - <U+56FD><U+5185><U+9886><U+5148><U+7684><U+7528><U+6237><U+884C><U+4E3A><U+5206><U+6790><U+4EA7><U+54C1>
    ## 4                                                                                                                                                       <NA>
    ## 5                                                                                                                                                       <NA>
    ## 6                                                                                                                                                       <NA>
    ##   properties.page properties.name properties.requestBtn
    ## 1            <NA>            <NA>                    NA
    ## 2            <NA>            <NA>                    NA
    ## 3            <NA>            <NA>                    NA
    ## 4           index         request                     2
    ## 5           index         request                     2
    ## 6            <NA>            <NA>                    NA
    ##   properties..utm_source properties..utm_medium properties..utm_campaign
    ## 1                   <NA>                   <NA>                     <NA>
    ## 2                   <NA>                   <NA>                     <NA>
    ## 3                   <NA>                   <NA>                     <NA>
    ## 4                   <NA>                   <NA>                     <NA>
    ## 5                   <NA>                   <NA>                     <NA>
    ## 6                   <NA>                   <NA>                     <NA>
    ##   properties..utm_content properties..utm_term properties.info
    ## 1                    <NA>                 <NA>            <NA>
    ## 2                    <NA>                 <NA>            <NA>
    ## 3                    <NA>                 <NA>            <NA>
    ## 4                    <NA>                 <NA>            <NA>
    ## 5                    <NA>                 <NA>            <NA>
    ## 6                    <NA>                 <NA>            <NA>
    ##   properties.result properties.contact properties.verification_code
    ## 1              <NA>               <NA>                         <NA>
    ## 2              <NA>               <NA>                         <NA>
    ## 3              <NA>               <NA>                         <NA>
    ## 4              <NA>               <NA>                         <NA>
    ## 5              <NA>               <NA>                         <NA>
    ## 6              <NA>               <NA>                         <NA>
    ##   properties.company properties.email properties.site_url
    ## 1               <NA>             <NA>                <NA>
    ## 2               <NA>             <NA>                <NA>
    ## 3               <NA>             <NA>                <NA>
    ## 4               <NA>             <NA>                <NA>
    ## 5               <NA>             <NA>                <NA>
    ## 6               <NA>             <NA>                <NA>
    ##   properties.from_url properties.project_name properties.isSuccess
    ## 1                <NA>                    <NA>                   NA
    ## 2                <NA>                    <NA>                   NA
    ## 3                <NA>                    <NA>                   NA
    ## 4                <NA>                    <NA>                   NA
    ## 5                <NA>                    <NA>                   NA
    ## 6                <NA>                    <NA>                   NA
    ##   properties.isMsg properties.referrerUrl properties.referrHostUrl
    ## 1               NA                   <NA>                     <NA>
    ## 2               NA                   <NA>                     <NA>
    ## 3               NA                   <NA>                     <NA>
    ## 4               NA                   <NA>                     <NA>
    ## 5               NA                   <NA>                     <NA>
    ## 6               NA                   <NA>                     <NA>
    ##   properties.siteUrl properties.url_path
    ## 1               <NA>                <NA>
    ## 2               <NA>                <NA>
    ## 3               <NA>                <NA>
    ## 4               <NA>                <NA>
    ## 5               <NA>                <NA>
    ## 6               <NA>                <NA>
    ##   properties._session_referrer_domain properties._session_from_url
    ## 1                                <NA>                         <NA>
    ## 2                                <NA>                         <NA>
    ## 3                                <NA>                         <NA>
    ## 4                                <NA>                         <NA>
    ## 5                                <NA>                         <NA>
    ## 6                                <NA>                         <NA>
    ##   jssdk_error
    ## 1        <NA>
    ## 2        <NA>
    ## 3        <NA>
    ## 4        <NA>
    ## 5        <NA>
    ## 6        <NA>

    str(df)

    ## 'data.frame':    51086 obs. of  70 variables:
    ##  $ distinct_id                        : chr  "595466e9a8e733434ce08de16e927d985e0b5d48" "9939d3e087bca29c42334d96dccd25ca0e06652a" "9939d3e087bca29c42334d96dccd25ca0e06652a" "9939d3e087bca29c42334d96dccd25ca0e06652a" ...
    ##  $ lib..lib                           : chr  "js" "js" "js" "js" ...
    ##  $ lib..lib_method                    : chr  "code" "code" "code" "code" ...
    ##  $ lib..lib_version                   : chr  "1.6.20" "1.6.20" "1.6.20" "1.6.20" ...
    ##  $ properties..os                     : chr  "windows" NA "windows" "windows" ...
    ##  $ properties..model                  : chr  "pc" NA "pc" "pc" ...
    ##  $ properties..os_version             : num  6.1 NA 10 10 10 ...
    ##  $ properties..screen_height          : int  800 NA 768 768 768 800 864 864 768 800 ...
    ##  $ properties..screen_width           : int  1280 NA 1366 1366 1366 1280 1536 1536 1366 1280 ...
    ##  $ properties..lib                    : chr  "js" NA "js" "js" ...
    ##  $ properties..lib_version            : chr  "1.6.20" NA "1.6.20" "1.6.20" ...
    ##  $ properties..browser                : chr  "chrome" NA "chrome" "chrome" ...
    ##  $ properties..browser_version        : num  56 NA 56 56 56 56 54 54 56 51 ...
    ##  $ properties..latest_referrer        : chr  "" NA "" "" ...
    ##  $ properties..latest_referrer_host   : chr  "" NA "" "" ...
    ##  $ properties..latest_utm_source      : chr  "baidu" NA NA NA ...
    ##  $ properties..latest_utm_medium      : chr  "cpc" NA NA NA ...
    ##  $ properties..latest_utm_campaign    : chr  "<U+901A><U+7528><U+8BCD>" NA NA NA ...
    ##  $ properties..latest_utm_content     : chr  "<U+901A><U+7528>-<U+7528><U+6237><U+753B><U+50CF>" NA NA NA ...
    ##  $ properties..latest_utm_term        : chr  "<U+7528><U+6237><U+753B><U+50CF>" NA NA NA ...
    ##  $ properties._latest_ch              : chr  "demo" NA "demo" "demo" ...
    ##  $ properties._session_referrer       : chr  "https://www.baidu.com/baidu.php" NA "" "" ...
    ##  $ properties._session_referrer_host  : chr  "www.baidu.com" NA "" "" ...
    ##  $ properties.session_page_url        : chr  "https://www.sensorsdata.cn/?utm_source=baidu&utm_medium=cpc&utm_term=%E7%94%A8%E6%88%B7%E7%94%BB%E5%83%8F&utm_content=%E9%80%9A"| __truncated__ NA "https://sensorsdata.cn/?ch=demo" "https://sensorsdata.cn/?ch=demo" ...
    ##  $ properties.pageUrl                 : chr  "https://sensorsdata.cn/?ch=demo" NA NA "https://sensorsdata.cn/?ch=demo" ...
    ##  $ properties.pageStayTime            : num  5.69 NA NA NA NA ...
    ##  $ properties.pagePosition            : int  2 NA NA NA NA 1 NA NA NA NA ...
    ##  $ properties..is_first_day           : logi  TRUE NA TRUE TRUE TRUE TRUE ...
    ##  $ properties..is_first_time          : logi  FALSE NA TRUE FALSE FALSE FALSE ...
    ##  $ properties..ip                     : chr  "219.135.131.99" NA "111.204.198.242" "111.204.198.242" ...
    ##  $ type                               : chr  "track" "profile_set_once" "track" "track" ...
    ##  $ event                              : chr  "index_leave" NA "$pageview" "btnClick" ...
    ##  $ X_nocache                          : num  6.54e+11 3.04e+12 9.59e+12 6.53e+11 8.21e+12 ...
    ##  $ time                               : num  1.49e+12 1.49e+12 1.49e+12 1.49e+12 1.49e+12 ...
    ##  $ properties..first_visit_time       : chr  NA "2017-03-06 17:04:10.999" NA NA ...
    ##  $ properties..first_referrer         : chr  NA "" NA NA ...
    ##  $ properties..first_browser_language : chr  NA "zh-CN" NA NA ...
    ##  $ properties..first_referrer_host    : chr  NA "" NA NA ...
    ##  $ properties.ch                      : chr  NA "demo" "demo" NA ...
    ##  $ properties..referrer               : chr  NA NA "" NA ...
    ##  $ properties..referrer_host          : chr  NA NA "" NA ...
    ##  $ properties..url                    : chr  NA NA "https://sensorsdata.cn/?ch=demo" NA ...
    ##  $ properties..url_path               : chr  NA NA "/" NA ...
    ##  $ properties..title                  : chr  NA NA "<U+795E><U+7B56><U+6570><U+636E> | Sensors Data - <U+56FD><U+5185><U+9886><U+5148><U+7684><U+7528><U+6237><U+884C><U+4E3A><U+52"| __truncated__ NA ...
    ##  $ properties.page                    : chr  NA NA NA "index" ...
    ##  $ properties.name                    : chr  NA NA NA "request" ...
    ##  $ properties.requestBtn              : int  NA NA NA 2 2 NA NA NA 2 NA ...
    ##  $ properties..utm_source             : chr  NA NA NA NA ...
    ##  $ properties..utm_medium             : chr  NA NA NA NA ...
    ##  $ properties..utm_campaign           : chr  NA NA NA NA ...
    ##  $ properties..utm_content            : chr  NA NA NA NA ...
    ##  $ properties..utm_term               : chr  NA NA NA NA ...
    ##  $ properties.info                    : chr  NA NA NA NA ...
    ##  $ properties.result                  : chr  NA NA NA NA ...
    ##  $ properties.contact                 : chr  NA NA NA NA ...
    ##  $ properties.verification_code       : chr  NA NA NA NA ...
    ##  $ properties.company                 : chr  NA NA NA NA ...
    ##  $ properties.email                   : chr  NA NA NA NA ...
    ##  $ properties.site_url                : chr  NA NA NA NA ...
    ##  $ properties.from_url                : chr  NA NA NA NA ...
    ##  $ properties.project_name            : chr  NA NA NA NA ...
    ##  $ properties.isSuccess               : logi  NA NA NA NA NA NA ...
    ##  $ properties.isMsg                   : logi  NA NA NA NA NA NA ...
    ##  $ properties.referrerUrl             : chr  NA NA NA NA ...
    ##  $ properties.referrHostUrl           : chr  NA NA NA NA ...
    ##  $ properties.siteUrl                 : chr  NA NA NA NA ...
    ##  $ properties.url_path                : chr  NA NA NA NA ...
    ##  $ properties._session_referrer_domain: chr  NA NA NA NA ...
    ##  $ properties._session_from_url       : chr  NA NA NA NA ...
    ##  $ jssdk_error                        : chr  NA NA NA NA ...

### Select users with complete information

    # total unique dinstinc_id: 8267
    total_id <- unique(df$distinct_id) 
    length(total_id)

    ## [1] 8267

    # Some entries do not have "event" column. These entries have "first_visit_time" data. 
    # The first_visit_time is the same as the time reported on the user's first visit time (properties..is_first_time = TRUE)

    # id without "event" 
    id <- df[is.na(df$event),]$distinct_id
    length(id) #6492 total

    ## [1] 6492

    length(unique(id)) # 6486 unique

    ## [1] 6486

    id[duplicated(id)] # same id repeats many times 

    ## [1] "6be727db0adc4b0ea41431cc91c8a5e1481125ac"
    ## [2] "6be727db0adc4b0ea41431cc91c8a5e1481125ac"
    ## [3] "6be727db0adc4b0ea41431cc91c8a5e1481125ac"
    ## [4] "6be727db0adc4b0ea41431cc91c8a5e1481125ac"
    ## [5] "6be727db0adc4b0ea41431cc91c8a5e1481125ac"
    ## [6] "6be727db0adc4b0ea41431cc91c8a5e1481125ac"

    df.repeat.id <- df[df$distinct_id == "6be727db0adc4b0ea41431cc91c8a5e1481125ac",] # some data points are dupilcates

    # only select users whose first visit is recorded in this dataset

    # is_first_time dataframe
    df.is.first.time <- subset(df, properties..is_first_time == TRUE)
    dim(df.is.first.time) # 6459 

    ## [1] 6459   70

    id.is.first.time <- df.is.first.time$distinct_id

    id.is.first.time.unique <- unique(id.is.first.time)
    length(id.is.first.time.unique) 

    ## [1] 6453

    # There are some duplicated id's. Take a look
    dup.ind <- duplicated(id.is.first.time)
    id.is.first.time[dup.ind] 

    ## [1] "6be727db0adc4b0ea41431cc91c8a5e1481125ac"
    ## [2] "6be727db0adc4b0ea41431cc91c8a5e1481125ac"
    ## [3] "6be727db0adc4b0ea41431cc91c8a5e1481125ac"
    ## [4] "6be727db0adc4b0ea41431cc91c8a5e1481125ac"
    ## [5] "6be727db0adc4b0ea41431cc91c8a5e1481125ac"
    ## [6] "6be727db0adc4b0ea41431cc91c8a5e1481125ac"

    # The same id shows up many times. Take a look at its entries
    df.dup.id <- subset(df, distinct_id == "6be727db0adc4b0ea41431cc91c8a5e1481125ac") 

    # This user has duplicated "profile_set_once". Not useful information. Delete this user for simplicity
    df <- subset(df, distinct_id != "6be727db0adc4b0ea41431cc91c8a5e1481125ac")

    # dataframe of users with information since first visit
    df.complete <- subset(df, distinct_id %in% id.is.first.time.unique & !is.na(event))

### convert time from POSIX to human readable time

    df.complete["time"] <- as.POSIXct(df.complete$time/1000, origin = "1970-01-01 00:00:00") # the raw time is in miliseconds
    dim(df.complete)

    ## [1] 30723    70

    head(df.complete)

    ##                                 distinct_id lib..lib lib..lib_method
    ## 3  9939d3e087bca29c42334d96dccd25ca0e06652a       js            code
    ## 4  9939d3e087bca29c42334d96dccd25ca0e06652a       js            code
    ## 5  9939d3e087bca29c42334d96dccd25ca0e06652a       js            code
    ## 9  9939d3e087bca29c42334d96dccd25ca0e06652a       js            code
    ## 15 ac2b15e51b9d22b6b46782fbde206657db0c1a26       js            code
    ## 18 9939d3e087bca29c42334d96dccd25ca0e06652a       js            code
    ##    lib..lib_version properties..os properties..model
    ## 3            1.6.20        windows                pc
    ## 4            1.6.20        windows                pc
    ## 5            1.6.20        windows                pc
    ## 9            1.6.20        windows                pc
    ## 15           1.6.20        windows                pc
    ## 18           1.6.20        windows                pc
    ##    properties..os_version properties..screen_height
    ## 3                    10.0                       768
    ## 4                    10.0                       768
    ## 5                    10.0                       768
    ## 9                    10.0                       768
    ## 15                    6.3                       900
    ## 18                   10.0                       768
    ##    properties..screen_width properties..lib properties..lib_version
    ## 3                      1366              js                  1.6.20
    ## 4                      1366              js                  1.6.20
    ## 5                      1366              js                  1.6.20
    ## 9                      1366              js                  1.6.20
    ## 15                     1600              js                  1.6.20
    ## 18                     1366              js                  1.6.20
    ##    properties..browser properties..browser_version
    ## 3               chrome                          56
    ## 4               chrome                          56
    ## 5               chrome                          56
    ## 9               chrome                          56
    ## 15              chrome                          45
    ## 18              chrome                          56
    ##        properties..latest_referrer properties..latest_referrer_host
    ## 3                                                                  
    ## 4                                                                  
    ## 5                                                                  
    ## 9                                                                  
    ## 15 https://www.baidu.com/baidu.php                    www.baidu.com
    ## 18                                                                 
    ##    properties..latest_utm_source properties..latest_utm_medium
    ## 3                           <NA>                          <NA>
    ## 4                           <NA>                          <NA>
    ## 5                           <NA>                          <NA>
    ## 9                           <NA>                          <NA>
    ## 15                         baidu                           cpc
    ## 18                          <NA>                          <NA>
    ##    properties..latest_utm_campaign
    ## 3                             <NA>
    ## 4                             <NA>
    ## 5                             <NA>
    ## 9                             <NA>
    ## 15        <U+901A><U+7528><U+8BCD>
    ## 18                            <NA>
    ##                       properties..latest_utm_content
    ## 3                                               <NA>
    ## 4                                               <NA>
    ## 5                                               <NA>
    ## 9                                               <NA>
    ## 15 <U+901A><U+7528>-<U+7528><U+6237><U+5206><U+6790>
    ## 18                                              <NA>
    ##         properties..latest_utm_term properties._latest_ch
    ## 3                              <NA>                  demo
    ## 4                              <NA>                  demo
    ## 5                              <NA>                  demo
    ## 9                              <NA>                  demo
    ## 15 <U+7528><U+6237><U+5206><U+6790>                  <NA>
    ## 18                             <NA>                  demo
    ##       properties._session_referrer properties._session_referrer_host
    ## 3                                                                   
    ## 4                                                                   
    ## 5                                                                   
    ## 9                                                                   
    ## 15 https://www.baidu.com/baidu.php                     www.baidu.com
    ## 18                                                                  
    ##                                                                                                                                                                                                 properties.session_page_url
    ## 3                                                                                                                                                                                           https://sensorsdata.cn/?ch=demo
    ## 4                                                                                                                                                                                           https://sensorsdata.cn/?ch=demo
    ## 5                                                                                                                                                                                           https://sensorsdata.cn/?ch=demo
    ## 9                                                                                                                                                                                           https://sensorsdata.cn/?ch=demo
    ## 15 https://www.sensorsdata.cn/?utm_source=baidu&utm_medium=cpc&utm_term=%E7%94%A8%E6%88%B7%E5%88%86%E6%9E%90&utm_content=%E9%80%9A%E7%94%A8%2D%E7%94%A8%E6%88%B7%E5%88%86%E6%9E%90&utm_campaign=%E9%80%9A%E7%94%A8%E8%AF%8D
    ## 18                                                                                                                                                                                          https://sensorsdata.cn/?ch=demo
    ##                 properties.pageUrl properties.pageStayTime
    ## 3                             <NA>                      NA
    ## 4  https://sensorsdata.cn/?ch=demo                      NA
    ## 5  https://sensorsdata.cn/?ch=demo                      NA
    ## 9  https://sensorsdata.cn/?ch=demo                      NA
    ## 15                            <NA>                      NA
    ## 18                            <NA>                      NA
    ##    properties.pagePosition properties..is_first_day
    ## 3                       NA                     TRUE
    ## 4                       NA                     TRUE
    ## 5                       NA                     TRUE
    ## 9                       NA                     TRUE
    ## 15                      NA                     TRUE
    ## 18                      NA                     TRUE
    ##    properties..is_first_time  properties..ip  type                event
    ## 3                       TRUE 111.204.198.242 track            $pageview
    ## 4                      FALSE 111.204.198.242 track             btnClick
    ## 5                      FALSE 111.204.198.242 track             btnClick
    ## 9                      FALSE 111.204.198.242 track             btnClick
    ## 15                      TRUE   58.62.134.182 track            $pageview
    ## 18                     FALSE 111.204.198.242 track click_send_cellphone
    ##       X_nocache                time properties..first_visit_time
    ## 3  9.587553e+12 2017-03-06 01:04:10                         <NA>
    ## 4  6.529371e+11 2017-03-06 01:04:11                         <NA>
    ## 5  8.207408e+12 2017-03-06 01:04:16                         <NA>
    ## 9  6.907472e+12 2017-03-06 01:04:23                         <NA>
    ## 15 5.787747e+12 2017-03-06 01:04:58                         <NA>
    ## 18 2.078299e+12 2017-03-06 01:05:14                         <NA>
    ##    properties..first_referrer properties..first_browser_language
    ## 3                        <NA>                               <NA>
    ## 4                        <NA>                               <NA>
    ## 5                        <NA>                               <NA>
    ## 9                        <NA>                               <NA>
    ## 15                       <NA>                               <NA>
    ## 18                       <NA>                               <NA>
    ##    properties..first_referrer_host properties.ch
    ## 3                             <NA>          demo
    ## 4                             <NA>          <NA>
    ## 5                             <NA>          <NA>
    ## 9                             <NA>          <NA>
    ## 15                            <NA>          <NA>
    ## 18                            <NA>          <NA>
    ##               properties..referrer properties..referrer_host
    ## 3                                                           
    ## 4                             <NA>                      <NA>
    ## 5                             <NA>                      <NA>
    ## 9                             <NA>                      <NA>
    ## 15 https://www.baidu.com/baidu.php             www.baidu.com
    ## 18                            <NA>                      <NA>
    ##                                                                                                                                                                                                             properties..url
    ## 3                                                                                                                                                                                           https://sensorsdata.cn/?ch=demo
    ## 4                                                                                                                                                                                                                      <NA>
    ## 5                                                                                                                                                                                                                      <NA>
    ## 9                                                                                                                                                                                                                      <NA>
    ## 15 https://www.sensorsdata.cn/?utm_source=baidu&utm_medium=cpc&utm_term=%E7%94%A8%E6%88%B7%E5%88%86%E6%9E%90&utm_content=%E9%80%9A%E7%94%A8%2D%E7%94%A8%E6%88%B7%E5%88%86%E6%9E%90&utm_campaign=%E9%80%9A%E7%94%A8%E8%AF%8D
    ## 18                                                                                                                                                                                                                     <NA>
    ##    properties..url_path
    ## 3                     /
    ## 4                  <NA>
    ## 5                  <NA>
    ## 9                  <NA>
    ## 15                    /
    ## 18                 <NA>
    ##                                                                                                                                             properties..title
    ## 3  <U+795E><U+7B56><U+6570><U+636E> | Sensors Data - <U+56FD><U+5185><U+9886><U+5148><U+7684><U+7528><U+6237><U+884C><U+4E3A><U+5206><U+6790><U+4EA7><U+54C1>
    ## 4                                                                                                                                                        <NA>
    ## 5                                                                                                                                                        <NA>
    ## 9                                                                                                                                                        <NA>
    ## 15 <U+795E><U+7B56><U+6570><U+636E> | Sensors Data - <U+56FD><U+5185><U+9886><U+5148><U+7684><U+7528><U+6237><U+884C><U+4E3A><U+5206><U+6790><U+4EA7><U+54C1>
    ## 18                                                                                                                                                       <NA>
    ##    properties.page properties.name properties.requestBtn
    ## 3             <NA>            <NA>                    NA
    ## 4            index         request                     2
    ## 5            index         request                     2
    ## 9            index         request                     2
    ## 15            <NA>            <NA>                    NA
    ## 18            <NA>            <NA>                    NA
    ##    properties..utm_source properties..utm_medium properties..utm_campaign
    ## 3                    <NA>                   <NA>                     <NA>
    ## 4                    <NA>                   <NA>                     <NA>
    ## 5                    <NA>                   <NA>                     <NA>
    ## 9                    <NA>                   <NA>                     <NA>
    ## 15                  baidu                    cpc <U+901A><U+7528><U+8BCD>
    ## 18                   <NA>                   <NA>                     <NA>
    ##                              properties..utm_content
    ## 3                                               <NA>
    ## 4                                               <NA>
    ## 5                                               <NA>
    ## 9                                               <NA>
    ## 15 <U+901A><U+7528>-<U+7528><U+6237><U+5206><U+6790>
    ## 18                                              <NA>
    ##                properties..utm_term properties.info properties.result
    ## 3                              <NA>            <NA>              <NA>
    ## 4                              <NA>            <NA>              <NA>
    ## 5                              <NA>            <NA>              <NA>
    ## 9                              <NA>            <NA>              <NA>
    ## 15 <U+7528><U+6237><U+5206><U+6790>            <NA>              <NA>
    ## 18                             <NA>     1**********      ajax success
    ##    properties.contact properties.verification_code properties.company
    ## 3                <NA>                         <NA>               <NA>
    ## 4                <NA>                         <NA>               <NA>
    ## 5                <NA>                         <NA>               <NA>
    ## 9                <NA>                         <NA>               <NA>
    ## 15               <NA>                         <NA>               <NA>
    ## 18               <NA>                         <NA>               <NA>
    ##    properties.email properties.site_url properties.from_url
    ## 3              <NA>                <NA>                <NA>
    ## 4              <NA>                <NA>                <NA>
    ## 5              <NA>                <NA>                <NA>
    ## 9              <NA>                <NA>                <NA>
    ## 15             <NA>                <NA>                <NA>
    ## 18             <NA>                <NA>                <NA>
    ##    properties.project_name properties.isSuccess properties.isMsg
    ## 3                     <NA>                   NA               NA
    ## 4                     <NA>                   NA               NA
    ## 5                     <NA>                   NA               NA
    ## 9                     <NA>                   NA               NA
    ## 15                    <NA>                   NA               NA
    ## 18                    <NA>                   NA               NA
    ##    properties.referrerUrl properties.referrHostUrl properties.siteUrl
    ## 3                    <NA>                     <NA>               <NA>
    ## 4                    <NA>                     <NA>               <NA>
    ## 5                    <NA>                     <NA>               <NA>
    ## 9                    <NA>                     <NA>               <NA>
    ## 15                   <NA>                     <NA>               <NA>
    ## 18                   <NA>                     <NA>               <NA>
    ##    properties.url_path properties._session_referrer_domain
    ## 3                 <NA>                                <NA>
    ## 4                 <NA>                                <NA>
    ## 5                 <NA>                                <NA>
    ## 9                 <NA>                                <NA>
    ## 15                <NA>                                <NA>
    ## 18                <NA>                                <NA>
    ##    properties._session_from_url jssdk_error
    ## 3                          <NA>        <NA>
    ## 4                          <NA>        <NA>
    ## 5                          <NA>        <NA>
    ## 9                          <NA>        <NA>
    ## 15                         <NA>        <NA>
    ## 18                         <NA>        <NA>

    # This dataframe contains information for analysis. Save it
    write.csv(df.complete, "sensors_user_info_complete.csv", row.names=FALSE)
