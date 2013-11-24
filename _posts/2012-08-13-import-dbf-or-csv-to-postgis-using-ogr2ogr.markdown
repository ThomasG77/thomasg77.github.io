---layout: post title: "Import DBF/CSV to PostGIS using ogr2ogr" date:
2012-08-13 23:46 comments: true categories: ---

Recently, I read a [post from Nathaniel
Kelso](http://kelsocartography.com/blog/?p=4240) about some behaviours
of PostGIS he want to be improved. Some days after, Paul Ramsey, one of
the core PostGIS commiters made [an answer about
this](http://blog.cleverelephant.ca/2012/08/postgis-apologia.html).

Both of them, are using for importing data from DBF/CSV with location
information a manual table creation, a csv import and a geometry
creation similar to
[http://www.kevfoo.com/2012/01/Importing-CSV-to-PostGIS](http://www.kevfoo.com/2012/01/Importing-CSV-to-PostGIS)/

I use another way I wanted to share.

I rely on ogr2ogr, an utility from [the library
ogr/gdal](http://www.gdal.org/) and [the virtual table
format](http://www.gdal.org/ogr/drv_vrt.html) (a way to wrap other
format in order to make some custom things)

Be careful, we're using version 1.9.1 the latest stable release of
ogr/gdal, some old binaries will not stand all options of our example.

The CSV example
===============

Considering,

-   a myfile.csv file

<!-- -->

    Latitude,Longitude,Name
    48.1,0.25,"First point"
    49.2,1.1,"Second point"
    47.5,0.75,"Third point"

-   a myfile.vrt file

<!-- -->

    <OGRVRTDataSource>
        <OGRVRTLayer name="myfile">
            <SrcDataSource>myfile.csv</SrcDataSource>
            <GeometryType>wkbPoint</GeometryType>
            <LayerSRS>WGS84</LayerSRS>
            <GeometryField encoding="PointFromColumns" x="Longitude" y="Latitude"/>
        </OGRVRTLayer>
    </OGRVRTDataSource>

-   and ogr2ogr command line (we custom it look like shp2pgsql default
    id to gid and geometry to the\_geom)

<!-- -->

    ogr2ogr -f "PostgreSQL" -lco GEOMETRY_NAME=the_geom -lco FID=gid PG:"host=localhost user=postgres dbname=mydb password=mypassword" myfile.vrt -nln mytable

This below mean import from myfile.vrt (this file point to your
mycsvfile.csv) into a PostgresSQL table named mytable using a geometry
name set to the\_geom and the id set to gid. The PostgreSQL connection
is defined into PG:"..."

The DBF example
===============

It's the same instructions except:

-   you create a DBF file using same structure (simple as a cut & paste
    of csv into LibreOffice or OpenOffice and a save as dbf)

-   you replace your myfile.vrt file with

<!-- -->

    <OGRVRTDataSource>
        <OGRVRTLayer name="myfile">
            <SrcDataSource>myfile.dbf</SrcDataSource>
            <GeometryType>wkbPoint</GeometryType>
            <LayerSRS>WGS84</LayerSRS>
            <GeometryField encoding="PointFromColumns" x="Longitude" y="Latitude"/>
        </OGRVRTLayer>
    </OGRVRTDataSource>

-   reload the same command line changing only option for overwriting
    previous csv table already loaded

<!-- -->

    ogr2ogr -f "PostgreSQL" -overwrite -lco GEOMETRY_NAME=the_geom -lco FID=gid PG:"host=localhost user=postgres dbname=mydb password=mypassword" myfile.vrt -nln mytable

Small goodies
=============

**ogr2ogr related**

The way we import is raw, the input is default to string but with VRT,
you can custom input type for columns (default is string) See the fields
part of the vrt doc
[http://www.gdal.org/ogr/drv\_vrt.html](http://www.gdal.org/ogr/drv_vrt.html)
and the example "Example: Renaming attributes" Be cautious, I haven't
try with DBF to set custom type

In our example, the ogr2ogr option "-nln mytable" can be forgotten if
you don't need to custom the output table name

Another great ressource (outside of the official doc) if you want to
dive into ogr2ogr further is [BostonGIS
ogr\_cheatsheet](http://www.bostongis.com/PrinterFriendly.aspx?content_name=ogr_cheatsheet)

For continuing DBF example, I borrow them this example

    ogr2ogr -f "PostgreSQL" PG:"host=myserver user=myusername dbname=mydbname password=mypassword" sometable.dbf -nln "sometable"

**shp2pgsql related**

Following another Kelso question concerning make default some options in
shp2pgsql, another tip is to use functions in bash (only available in
Unix-like systems)

So put in \~/.bashrc

    shp2pgsql_im () {
      shp2pgsql -dID $@
    }

reload \~/.bashrc

    source ~/.bashrc

Now shp2pgsql\_im the new command with default options added to
shp2pgsql

Hope you enjoy this small contribution
