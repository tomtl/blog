---
layout: post
title: Postgres window functions
date: 2017-12-15
---

**Window functions are easy** said everyone that can do them but no one that can't. What do they even do? They perform a calculation across a table related to the current row in a specific way. That still doesn't explain much though. A good use case is you have a row that belongs to a category and you want to get the sum or average of a column for all other rows in that same category. It's easy to do other ways but it is simpler and faster using a window function.


#### Example
For example, I needed determine which counties a zip code is in and how many households of the zip code are within each county. There is one row for each zip code / county pair and a column giving how many households for that zip code are within the county. To get the percentage of households that are in the county I need the sum of households for all rows of the zip code.

{% highlight sql %}
SELECT zipcode, county, overlap_households
FROM zip_county_overlap
ORDER BY 1, 2;
{% endhighlight %}

| zipcode | county         | overlap_households|
|---------|----------------|-------------------|
| 10101   | Appleton       | 162               |
| 10102   | Barkleyville   | 50                |
| 10102   | Charlieborough | 150               |
| 10103   | Barkleyville   | 75                |
| 10103   | Danopolis      | 10                |
| 10103   | Eaton          | 15                |


#### Sum of the households in a zip code
{% highlight sql %}
SELECT zipcode, county, overlap_households,
SUM(households) OVER (PARTITION BY zipcode) AS zipcode_households
FROM zip_county_overlap ORDER BY 1, 2;
{% endhighlight %}

zipcode | county         | overlap_households | zipcode_households
--------|----------------|------------------- | ------------------
10101   | Appleton       | 162                | 162
10102   | Barkleyville   | 50                 | 200
10102   | Charlieborough | 150                | 200
10103   | Barkleyville   | 75                 | 100
10103   | Danopolis      | 10                 | 100
10103   | Eaton          | 15                 | 100

#### Percentage coverage

{% highlight sql %}
SELECT zipcode, county, overlap_households,
SUM(households) OVER (PARTITION BY zipcode) AS zipcode_households,
overlap_households / (SUM(households) OVER (PARTITION BY zipcode))
  AS overlap_percent
FROM zip_county_overlap ORDER BY 1, 2;
{% endhighlight %}`

zipcode | county         | overlap_households | zipcode_households | overlap_percent
--------|----------------|------------------- | -------------------|----------------
10101   | Appleton       | 162                | 162                | 100.0
10102   | Barkleyville   | 50                 | 200                | 25.0
10102   | Charlieborough | 150                | 200                | 75.0
10103   | Barkleyville   | 75                 | 100                | 75.0
10103   | Danopolis      | 10                 | 100                | 10.0
10103   | Eaton          | 15                 | 100                | 15.0

### You did it!
That's it! That's all I needed to do. It was easy after all. There are lots of better examples available than available than this one, I just wanted to get this down so I remembered it for next time I need to use window functions.
