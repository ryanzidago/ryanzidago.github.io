---
layout: post
title:  "Today I learned about PostgreSQL's date/time operators"
date:   2020-11-17 01:10:00 +0100
categories: databases postgresql datetime
tags: databases postgresql datetime
---

I can get the current time with the `current_time` function:
```sql
SELECT current_time
00:11:58.090572+00:00
```

\
The function `current_timestamp` returns the current datatime:
```sql
SELECT current_timestamp
2020-11-17 00:13:29.808727+00
```

\
I can even add or substract time period from it:
```sql
SELECT current_timestamp - interval '1 day'
2020-11-16 00:14:33.815554+00

SELECT current_timestamp - interval '1 year'
2012-11-17 00:15:51.287043+00

SELECT current_timestamp - interval '90 minutes'
2020-11-16 22:46:25.18201+00
```
