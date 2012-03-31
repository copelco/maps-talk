Django and Maps
===============

----

Talk Outline
============

- Standards and geospatial libraries
- Web Stack
- Demos

Presenter Notes
---------------

- I wanted a simple intro into GeoDjango
- I will touch on the technologies, but not go in depth
- Understand small fraction of what's possible
- First, standards!

----

Open Geospatial Consortium (OGC)
================================

Presenter Notes
---------------

- Standards organization for geospatial content
- Defines OGC geometries used by geospacial libraries

----

Geospatial Libraries
--------------------

* GEOS - required by PostGIS
* PROJ.4 - required by PostGIS
* GDAL - Not required, but Django uses it a lot, including the Django admin
* PostGIS

Presenter Notes
---------------

- The required geospatial libraries depends on the spatial database used
- Django recommends PostGIS, because it is the most mature and feature-rich open source spatial database.

----

Geometry Engine Open Source (GEOS)
==================================

Presenter Notes
---------------

- C++ library required by PostGIS
- Provides a lot, but is commonly used for representing geometries
- Django has a Python interface to the GEOS geometry routines

----

GEOSGeometry
============

.. code-block:: pycon

    >>> from django.contrib.gis.geos import GEOSGeometry
    >>> pnt = GEOSGeometry('POINT(5 23)') # WKT
    >>> pnt.coords
    (5.0, 23.0)

Presenter Notes
---------------

- Well-known Text is a text markup language for representing vector geometry

----

Well-known Text
===============

- POINT (30 10)
- LINESTRING (30 10, 10 30, 40 40)
- POLYGON ((30 10, 10 20, 20 40, 40 40, 30 10))

Presenter Notes
---------------

- Defined by the Open Geospatial Consortium (OGC)
- Or you can use specific geometry types

----

Geometry Types
==============

.. code-block:: pycon

    >>> from django.contrib.gis.geos import Point, LineString
    >>> pnt = Point(5, 23)
    >>> line = LineString((0, 0), (0, 50), (50, 50), (50, 0), (0, 0))
    >>> pnt.wkt
    'POINT (5.0000000000000000 23.0000000000000000)'

----

PROJ.4
======

Presenter Notes
---------------

- Cartographic Projections library
- A library for converting geospatial data to different coordinate reference systems

----

Spatial Reference Systems (SRS)
===============================

Presenter Notes
---------------

- Defines a specific map projection, as well as transformations between different spatial reference systems
- Referred to by SRID

----

World Geodetic System of 1984 (WGS84) 
=====================================

Presenter Notes
---------------

- Django's default; World Geodetic System of 1984
- Coordinates correspond to latitude and longitude
- Commonly used, Global Positioning System (GPS)

----

Spherical Mercator
==================

- Google Maps
- Microsoft Virtual Earth
- Open Street Maps

Presenter Notes
---------------

- Mercator projection which treats the earth as a sphere, rather than an ellipsoid
- Raster map tiles are all projected into the ‘spherical mercator’ projection.

----

Geospatial Data Abstraction Library (GDAL)
==========================================

Presenter Notes
---------------

- "Swiss army knife" for GIS data
- Reading and writing vector geographic data in a variety of standard formats

----

PostGIS
=======

Presenter Notes
---------------

- Spatial extensions for PostgreSQL
- Adds geographic objects
- Support for spacial queries

----

Web Stack
=========

- GeoDjango
- API
- Map Tiles
- JavaScript Interface Library

Presenter Notes
---------------

- Let's start with a Django model

----

Place
=====

.. code-block:: python
    :emphasize-lines: 1,5

    from django.contrib.gis.db import models

    class Place(models.Model):
        name = models.CharField(max_length=255)
        point = models.PointField()
        address = models.TextField(blank=True)
        description = models.TextField(blank=True)

        def __unicode__(self):
            return self.name

----

Place Admin
===========

.. code-block:: python
    :emphasize-lines: 1,4

    from django.contrib.gis import admin
    from shapefiles.models import Place

    class PlaceAdmin(admin.OSMGeoAdmin):
        list_display = ('name', 'address', 'point')
        search_fields = ('name',)

    admin.site.register(Place, PlaceAdmin)

----

Demo Place Admin
================

Presenter Notes
---------------

- What is that map?

----

Slippy Maps
===========

Presenter Notes
---------------

- Pre-generated map tiles
- 256 × 256 pixel PNG files
- Created for every zoom level

----

Show Polymaps Example
=====================

----

Map Tile Sources
================

- Google Maps
- Open Street Maps
- MapQuest, Yahoo, etc.
- Serve your own with Mapnik (http://mapnik.org/)

Presenter Notes
---------------

- Look and feel of maps

----

Custom Tiles
============

- TileMill: Import shapefiles and style layers with CSS-like syntax
    - http://mapbox.com/tilemill/
- MapBox: Service to serve rendered tiles
    - http://mapbox.com/tilemill/

----

JavaScript Interface Library
============================

- Google Maps
- OpenLayers (http://openlayers.org/)
- Leaflet (http://leaflet.cloudmade.com/)

Presenter Notes
---------------

- Powers Slippy Maps and manages the UI controls
- Everyone knows Google Maps
- OpenLayers is standard and includes powerful polygon tools

----

Template Example
================

.. code-block:: javascript

    var map = $('#map').map();
    map.map("add_marker", {
        'title': "{{ place.name }}",
        'point': [{{ place.point.coords.0 }}, {{ place.point.coords.1 }}]
    });
    map.map("center", [{{ place.point.coords.0 }}, {{ place.point.coords.1 }}]);
    map.map("zoom", 16);

Presenter Notes
---------------

- Wrote jQuery UI widget to abstract calls

----

Behind the Scenes
=================

.. code-block:: javascript

    function(data) {
        var marker = new google.maps.Marker({
            'map': widget.map,
            'title': data.title,
            'position': new google.maps.LatLng(data.point[1], data.point[0])
        });
    }

----

Demo Places
===========

----

Demo Locations
==============

----

Demo Population
===============

----

Demo Points of Interest
=======================

----
