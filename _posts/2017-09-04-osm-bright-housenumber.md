---
layout: post
title: Map Style OSM Bright With House Numbers
---
I really like the map style ["OSM Bright"](https://github.com/mapbox/osm-bright). It is very clean and concentrates on the important stuff in a map. It is not overloaded with a lot of POIs. But unfortunately it lacks one feature which is very important for me: house numbers.

![OSM Bright Example](https://camo.githubusercontent.com/3912f543dafa005d64c8b2b698f7d521db6ffcbc/68747470733a2f2f7261772e6769746875622e636f6d2f6d6170626f782f6f736d2d6272696768742f6d61737465722f707265766965772e706e67)

This article explains what to do to add house numbers to the OSM Bright style and get it rendered with the classic [OpenTileServer-Stack](https://opentileserver.org/).

## Prerequisits

I assume that you used the script from OpenTileServer to set up a classic tile server based on Ubuntu, mod_tile, mapnik and postgresql. You should have specified "bright" as the style during installation. I also assume, that you have imported a set of data form OSM and your tile server does what it is supposed to do: happily delivering tiles.

## Overview

The following steps are needed to get house numbers in OSM Bright:

* Patching style sheet in labels.mss
* Patching Data Source in project.mml
* Compiling XML for renderd
* Clearing cached tiles
* Restarting renderd

## Patching style sheets

By default all styles are created in /usr/local/share/maps/style. There should be a folder called OSMBright. There is a file called labels.mss. This file contains the CartoCSS definitions of various labels that appear later on your tiles. Our house numbers will also appear as labels. So we need to add to the end of this file the following lines:
`````
/* ================================================================== */
/* Housenumbers
/* ================================================================== */

#housenumbers[zoom >= 17] {
  ::label {
    text-name: '[addr:housenumber]';
    text-face-name:@sans;
    text-size: 9;   
    text-placement:interior;
    text-min-distance: 1;
    text-wrap-width: 0;
    text-fill: #444;
  }    
}
`````
This basically tells the renderer that all house numbers should only be rendered on zoom levels greater than 17, how it should look like and where it is supposed to be placed.

What this file does not tell you: What are house numbers? Where do they come from? This is the responsibility of the next file we have to patch.

## Patching the Data Source

In the same folder you will find a file called project.mml. This file is kind of a build file for CartoCSS and defines for example which style sheet files should be used. It also defines a bunch of data sources which define the input for the style sheets. Up to now there is no input definition for house numbers, so we need to add another data source to the file:
`````
   {
      "Datasource": {
        "dbname": "gis", 
        "extent": "-20037508.34,-20037508.34,20037508.34,20037508.34",
        "geometry_field": "way",
        "key_field": "",  
        "table": "(SELECT way, \"addr:housenumber\" \n FROM planet_osm_polygon \n WHERE \"addr:housenumber\" IS NOT NULL) AS data\n",
        "type": "postgis"
      }, 
      "advanced": {},
      "class": "",
      "extent": [
        -122.85990918289764,
        47.38001179491598, 
        -121.86970418303555,
        47.842970094896586
      ], 
      "geometry": "point", 
      "id": "housenumbers", 
      "name": "housenumbers", 
      "srs": "+proj=merc +a=6378137 +b=6378137 +lat_ts=0.0 +lon_0=0.0 +x_0=0.0 +y_0=0.0 +k=1.0 +units=m +nadgrids=@null +wktext +no_defs +over",
      "srs-name": "900913"
    }
`````
This tells the renderer which SQL to use to retrieve house numbers from the database.

## Compiling XML for the renderer

The renderer Mapnik does not work directly with CartoCSS. We need to compile the files to the format Mapnik understands. By default the renderd uses the file called OSMBright.xml in the same folder. In order to get it we need to execute `carto project.mml > OSMBright.xml`.

## Clearing cached tiles and restart renderd

The next step is to clear the already rendered tiles. Otherwise they do not get rerendered and the house numbers do not show up.

1. Stop renderd: `systemctl stop renderd`
2. Clear cached files: `rm -rf /var/lib/mod_tile/default/*`
3. Restart renderd: `systemctl start renderd`
4. Maybe you should delete your browser's cache.

That's it. When you visit your tile server the tiles should get regenerated with house numbers on them.
