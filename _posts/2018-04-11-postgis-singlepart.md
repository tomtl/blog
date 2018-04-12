---
layout: post
title: PostGIS multipart to single part
date: 2018-04-11
---

**PostGIS** doesn't have a specific tool to convert multipart features such as multilinestrings to single part features (e.g. linestrings), but you can use ST_Dump to do the job if the geometry is valid.

{% highlight sql %}
SELECT
  ST_Dump(geom).geom
FROM sometable
{% endhighlight %}

ST_Dump expands a multipart geometry into one row for each feature. It returns the data as a geometry_dump type, which is different from the regular geometry type, but you can convert it back to the geometry type by adding `.geom`.


### More Information
[ST_Dump documentation](https://postgis.net/docs/ST_Dump.html)
