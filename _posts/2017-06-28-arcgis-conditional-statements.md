---
layout: post
title: ArcMap raster calculator conditional statements
date: 2017-06-28
---

The Raster Calculator can do a lot, but the documentation is pretty scarce. I got stuck adding together multiple raster layers with null values in ArcMap. If one of the rasters has a cell with a null value, then when that cell is added to values from other cells it still equals null.

`null + any value = null`

The way around this is to use a conditional statement.

`Con( condition, value if true, value if false )`

For example, to check if a cell value is null and reassign that cell value to zero if it is null:

`Con(isNull("raster_layer"), 0, "raster_layer")`

So, if you want to sum two raster layers with null values and want to substitute zeros for the nulls, do this:

```
Con(IsNull("raster_layer_1"), 0, "raster_layer_1") +
Con(IsNull("raster_layer_2"), 0, "raster_layer_2")
```

This works for ArcGIS Desktop 10.5 and you probably need the Spatial Analyst extension to be able to use the Raster Calculator. You could just use the reclassify tool to convert the null values to 0 before running the raster calculator, but that's an extra step. More information on conditional statements is available here.
