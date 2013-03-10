OpenTripPlanner instance for Montréal
------------------------------------

This page is based on the work done to setup an OpenTripPlanner instance for the region of Montréal, Canada. This is based on the work done by Guillaume B
arreau and [Nicolas Saunier](https://github.com/nsaunier). 90% of the work is detailed in the OTP wiki, but some specific steps needed some precision.

##Custom Graph##

The build of the graph is based on the Custom Graph section of [5 minutes introduction](https://github.com/openplans/OpenTripPlanner/wiki/FiveMinutes) from OTP wiki.

Here are the specific elements:

###Open Street Map###
In order to get the Open Street Map data, the simplest option is to download a metropolitan area file. For example Teczno provide a [Metro Extract](http://metro.teczno.com/) containing most of the north american metropolitan areas (and others).

###Elevation Data ###
For the elevation data, OTP usually works with NED data in the US but this dataset does not cover Canada. In order to get elevation data (which is useful for biking), the Reverb ASTER data are needed. 

The data portal is [here](http://reverb.echo.nasa.gov/) and there is a [howto document](http://www.echo.nasa.gov/reference/reverbastergdem_tutorial.htm). The important step is to use the map in the left panel to select a geographical area needed to cover the graph.

For example, to cover the Montréal metro area, the following files were needed: 
- ASTGTM2_N45W073.zip
- ASTGTM2_N45W074.zip  
- ASTGTM2_N45W075.zip
- ASTGTM2_N46W073.zip  
- ASTGTM2_N46W074.zip
- ASTGTM2_N46W075.zip

The OTP graph builder is only able to process single files, so the gdal-bin package is needed to merge the DEM files with a commande like: 
~~~
	gdal_merge.py ASTGTM2_N4*_dem.tif mtl.tif
~~~

Which will result in a geotiff file ready to be processed by graph builder.

The builder configuration item for elevation looks like that: 
~~~
    <bean id="nedBuilder" class="org.opentripplanner.graph_builder.impl.ned.NEDGraphBuilderImpl">
        <property name="gridCoverageFactory">
                <bean class="org.opentripplanner.graph_builder.impl.ned.GeotiffGridCoverageFactoryImpl">
                    <property name="cacheDirectory" value="/otp/cache/ned/mtl.tif" />
                </bean>
        </property>
    </bean>
~~~

###GTFS###

GTFS files have to be located where available. The graph builder is able to download them if an URL is provided.


##Language##

For Montreal, we wanted the default language to be french, which can be set in the config.js file:
~~~
	otp.config.locale = otp.locale.French;
~~~

This will also have the consequence of setting the metric system to 'international'.

Note: In the version used, the french.js file (and several other languages) were invalid, resulting in an awkward javascript error when trying to use the language file. This can be solved by downloading the last language file on the GitHub repository.
