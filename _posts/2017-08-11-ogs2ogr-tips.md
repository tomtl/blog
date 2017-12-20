---
layout: post
title: OGR2OGR data loading tricks
date: 2017-06-28
---

**OGR2OGR** is my primary tool for loading spatial data to PostGIS databases. It's useful because it can process many data formats and has enough functionality and speed to get the job done. It's command line. Some people love command line, but I'm not really a fan of committing syntax to memory, so here are some useful flags and options for OGR2OGR tasks.


The basic command to load a shapefile looks like this:

{% highlight bash %}
ogr2ogr -f "PostgreSQL" PG:"dbname='databasename' host='addr' port='5432'
user='x' password='y'" shapefile_name.shp
{% endhighlight %}

This will load a shapefile to the default schema on the database. You can add options to the command for additional functionality. It's usually not important what order you put the options in the command, but the option's values go after the option. If there are spaces in the filename, you need to put double quotes around the filename and extension.

### Progress `-progress`

The most useful option is to show the progress of the tool, which you can add with the -progress flag. It doesn't work for every source data format, but it's better than looking at an unchanging command line and wondering how long is left to go and if it is even running.

{% highlight bash %}
ogr2ogr -f "PostgreSQL" PG:"..." shapefile_name.shp -progress
{% endhighlight %}

### Set destination schema `-lco schema=schemaname`

To store the new table in something other than the default schema, you use the Layer Creation Options flag -lco then the schema name option schema=schemaname.

{% highlight bash %}
ogr2ogr -f "PostgreSQL" PG:"..." shapefile_name.shp -lco schema=schemaname
{% endhighlight %}

If you get an error that a new database or schema can't be created, check that you got the schema name correct as normally ogr2ogr can't create a new schema in the database.

### Set destination table name `-nln new_tablename`

To give the file a different name in the database, use the flag -nln then the new name.

{% highlight bash %}
ogr2ogr -f "PostgreSQL" PG:"..." shapefile_name.shp -nln tablename
{% endhighlight %}

### Set the name of the geometry column `-lco geometry_name=geom `

The ogr2ogr tool creates a geometry column named something like "ogc_geometry". Normally I change that name to "geom".

{% highlight bash %}
ogr2ogr -f "PostgreSQL" PG:"..." shapefile_name.shp -lco geometry_name=geom
{% endhighlight %}

### Set the destination number of dimensions `-lco dim=2`

Some files converted by OGR2OGR end up with 3 dimensions (x, y, z) in the target table when they really only need two (x, y). This takes up extra space in the database and can cause certain tools to be slower or crash when using that geometry. You can force the geometry to 2D by dropping the third dimension using the Layer Creation Option flag and the DIM option.

{% highlight bash %}
ogr2ogr -f "PostgreSQL" PG:"..." shapefile_name.shp -lco dim=2
{% endhighlight %}

### Set the source file spatial reference system `-a_srs "EPSG:4326"`

For source files that do not have a Spatial Reference System (SRS) or OGR2OGR doesn't recognise the SRS, you can set the SRS with the -a_srs option. Note that this doesn't project the data to a new SRS or change the coordinate values in any way, it just adds the SRS information. "EPSG:4326" is the EPSG code for the WGS84 SRS.

{% highlight bash %}
ogr2ogr -f "PostgreSQL" PG:"..." shapefile_name.shp -a_srs "EPSG:4326"
{% endhighlight %}

### Project/transform to a new different spatial reference `-t_srs "EPSG:4326"`

To project the data to a different Spatial Reference System (SRS) than the source data, use the -t_srs option. Note that this will change the geometry during the projection/transformation process to convert it to the new SRS. If the SRS is just missing, use the -a_srs option mentioned above.

{% highlight bash %}
ogr2ogr -f "PostgreSQL" PG:"..." shapefile_name.shp -t_srs "EPSG:4326"
{% endhighlight %}

### Sourcing data from a geodatabase

If the source data is an ESRI featureclass inside a geodatabase, you need to use the geodatabase name and the featureclass name. You can look inside the geodatabase to see the featureclass names using the OGRINFO function.

{% highlight bash %}
ogr2ogr -f "PostgreSQL" PG:"..." geodatabase.gdb featureclassname
{% endhighlight %}

### Set the data type of a column in the destination table `-lco COLUMN_TYPES='column_name=data_type'`

Sometimes you need to force a column to have a certain data type. For that you can use the Layer Creation and column_type options. Just make sure you checked that it worked the way you were intending after you load the data.

{% highlight bash %}
ogr2ogr -f "PostgreSQL" PG:"..." shapefile_name.shp
-lco COLUMN_TYPES='geoid=varchar(25)'
{% endhighlight %}

### Load only selected columns `-select "column_name, column_name, ..."`

For some datasets you only want to load select columns. For example, some of the American Community Survey data tables contain thousands of columns, but you only need a few loaded. Use the -select option, then include the column names in a comma separated list with double quotes around the list.

{% highlight bash %}
ogr2ogr -f "PostgreSQL" PG:"..." ACS_2015_5YR_BG.gdb X25_HOUSING_CHARACTERISTICS
-select "geoid, B25001e1, B25002e1, B25002e2, B25002e3"
{% endhighlight %}


### More information
The full documention for OGR2OGR is [here](http://www.gdal.org/ogr2ogr.html).

Documentation for the OGR2OGR commands specific to PostGIS are [here](http://www.gdal.org/drv_pg.html).

**Regina Obe's** cheat sheet is what got me started using OGR2OGR: [BostonGIS](http://www.bostongis.com/PrinterFriendly.aspx?content_name=ogr_cheatsheet)
