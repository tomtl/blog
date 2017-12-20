---
layout: post
title: ArcMap raster calculator conditional statements
date: 2017-06-28
---

The ArcGIS Desktop **Raster Calculator** can do a lot, but the documentation is pretty scarce. I got stuck adding together multiple raster layers with null values in ArcMap. If one of the rasters has a cell with a null value, then when that cell is added to values from other cells it still equals null.

{% highlight vb %} null + any value = null {% endhighlight %}

The way around this is to use a conditional statement.

{% highlight vb %}Con( condition, value if true, value if false ) {% endhighlight %}

For example, to check if a cell value is null and reassign that cell value to zero if it is null:

{% highlight vb %} Con(isNull("raster_layer"), 0, "raster_layer") {% endhighlight %}

So, if you want to sum two raster layers with null values and want to substitute zeros for the nulls, do this:


{% highlight vb %}
Con(IsNull("raster_layer_1"), 0, "raster_layer_1")
  + Con(IsNull("raster_layer_2"), 0, "raster_layer_2")
{% endhighlight %}


This works for ArcGIS Desktop 10.5 and you probably need the Spatial Analyst extension to be able to use the Raster Calculator. You could just use the reclassify tool to convert the null values to 0 before running the raster calculator, but that's an extra step. More information on conditional statements is available [here](http://desktop.arcgis.com/en/arcmap/latest/tools/spatial-analyst-toolbox/conditional-evaluation-with-con.htm).
