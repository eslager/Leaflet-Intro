# Introduction to web mapping with Leaflet

This is a tutorial written by [Emma Slager](http://faculty.washington.edu/ejslager/) for the Washington Women in GIS & Technology group in September 2020. It builds on an excellent [Maptime Boston tutorial](https://maptimeboston.github.io/leaflet-intro/), but is tailored a bit to our local context (#earthquakes!) and assumes an audience that knows a lot about mapping but might be new to web development.

## Why Leaflet? 

[Leaflet](https://leafletjs.com/) is one of many different frameworks that exist for making interactive web maps. I like it because it is free and open-source, is lightweight and flexible, and can be easily combined with other tools to expand its capabilities. Most importantly for a workshop like this, you can build Leaflet maps without signing up for any kind of account, which makes it an ideal framework for new learners. 

Leaflet is a JavaScript library. JavaScript is a programming language used extensively in modern web development, usually in combination with HTML and CSS. HTML (or Hyper Text Markup Language) provides the meaning and structure of the web page, CSS (Cascading Style Sheets) provides the styling, and JavaScript provides the functionality. 

We'll be working a little bit with all three of these. Writing and editing HTML, CSS, and JS files requires a text editor. I recommend [Atom.io](https://atom.io/). It's free and available on Windows, Mac, and Linux operating systems. 

## A basic Leaflet map

<iframe src="https://ejslgr.github.io/Leaflet-Intro/code-samples/basic.html" style="width:100%; height:500px;"></iframe>

The map above is a very simple Leaflet map. You can pan, zoom, and click it, but it only requires a few lines of code that include: 

1. An HTML page with ```<head>``` and ```<body>``` elements
2. A link to the Leaflet CSS styles
3. A link to the Leaflet JavaScript library
4. A ```<div>``` element to hold the map
5. A height style specified for the map div
6. A bit of JavaScript to create the map in the div

View the code below with comments explaining what each part does:
```html
<html>
<head>
	<title>Intro to Leaflet</title>
 	<!-- the Leaflet CSS -->
	<link rel="stylesheet" href="https://unpkg.com/leaflet@1.5.1/dist/leaflet.css"/>
	<!-- the Leaflet JavaScript library -->
	<script src="https://unpkg.com/leaflet@1.5.1/dist/leaflet.js"></script>
</head>

<body>
  	<!-- code where we create and name the container that holds the map -->
	<div id="map" style="height: 95%"></div>
  	<!-- code where we build the map and its functionality -->
	<script>
		var mymap = L.map('map').setView([47.258, -122.465], 12);
		var basemap = L.tileLayer('https://stamen-tiles-{s}.a.ssl.fastly.net/terrain/{z}/{x}/{y}{r}.png', {
			attribution: 'Map tiles by <a href="http://stamen.com">Stamen Design</a>, <a href="http://creativecommons.org/licenses/by/3.0">CC BY 3.0</a> &mdash; Map data &copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors',
		}).addTo(mymap);
		var point = L.marker([47.258, -122.465]).addTo(mymap);
			point.bindPopup("<b>Hello world!</b><br>I am a popup.");
  	</script>
</body>
</html>
```
Let's take a closer look at the JavaScript code that creates the map. Inside the <script> tags, we have code that: 

1. Creates the map variable (named "```mymap```") 
2. Uses ```L.map()``` to initialize the map object, passing it the id of the div where we want the map to go
3. Uses the ```setView()``` method to center the map on Tacoma (latitude 47.258 and longitude -122.465) and set the zoom level to 12
4. Uses ```L.tileLayer()``` to create the base layer of map tiles, specifying a URL template for the tile images. In this case, we're using tiles map designed by a company called Stamen, but we could use tiles from [many different sources](http://leaflet-extras.github.io/leaflet-providers/preview/), including Open Street Map, ESRI, or Carto. {z}/{x}/{y} is a template that Leaflet uses to find tiles at the correct zoom, x, and y coordinates. The code inside the ```attribution``` option sets the attribution text that appears in the bottom right corner of the map. 
5. Uses the ```addTo()``` method to add the base tile layer to the map
6. Uses ```L.marker()```to create a point marker at lat/long 42.258, -122.465 and ```addTo()``` to add the point marker to the map
7. Uses ```.bindPopup``` to create a popup that appears when the point marker is clicked. We use HTML ```("<b>Hello world!</b><br>I am a popup.")``` to provide the content of the popup. 

## Try it yourself

For the next section of the tutorial, I suggest that you download this starter file [**insert link]**, open it in Atom, and follow along to make the changes yourself. 

You'll note that the starter file (called earthquakes.html) is very similar to the basic Leaflet map we looked at above, except that we've removed the marker and changed the center and zoom level. Next, we'll add data from an external geojson file. 

## Working with GeoJSON data

Adding data with the ```L.marker()``` method is simple, but it can be somewhat inconvenient. If we were mapping hundreds of points, we would have to manually type in the lat/long pairs for every point into our code. No thank you! Can't we just add a Shapefile? 

Shapefiles, as you likely know, are the default vector data format when working with ArcGIS. With web mapping, however, the standard data type for vector data is GeoJSON. Like other formats for geospatial data, GeoJSON stores information about geographic features and their non-spatial attributes (e.g. a line indicating a street and the name of the street). It is based on JavaScript Object Notation, which means it will be more familiar to web developers than GIS professionals, but it's fairly easy to work with and understand.  

Instead of storing data in tables, GeoJSON stores data in "key: value pairs." These are both machine readable and human readable. Here's an example: 
```geojson
{ "type": "FeatureCollection",
  "features": [
    { "type": "Feature",
      "geometry": {"type": "Point", "coordinates": [102.0, 0.5]},
      "properties": {"name": "Example Point"}
      },
    { "type": "Feature",
      "geometry": {
        "type": "LineString",
        "coordinates": [
          [102.0, 0.0], [103.0, 1.0], [104.0, 0.0], [105.0, 1.0]
          ]
        },
      "properties": {
        "name": "example line",
        "number of vertices": 4
        }
      },
    { "type": "Feature",
       "geometry": {
         "type": "Polygon",
         "coordinates": [
           [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0],
             [100.0, 1.0], [100.0, 0.0] ]
           ]

       },
       "properties": {
         "name": "example polygon",
         "number of vertices": 5}
         }
    ]
  }
  ```
In this file, we have a collection of features. Each feature has a geometry and properties. The geometry describes the geospatial attributes. For instance, the first feature is a point located at lat: 0.5 and lon: 102.0. The properties are the non-spatial attributes. In this case, each feature has a name, and the line and polygon features also have a property that lists of the number of vertices in the shape. 

If you want to know more about GeoJSON, a good place to start is its [Wikipedia page](https://en.wikipedia.org/wiki/GeoJSON). Note that many open data portals make data available to download in the GeoJSON format, but it's also possible to convert data in other formats (like Shapefiles, CSVs, KMLs, etc.) into GeoJSON with various tools. 

## Adding GeoJSON data to our Leaflet map

To our map, we're going to add a live GeoJSON feed of all the earthquakes that occurred in the past day. USGS maintains numerous earthquake feeds, and you can see a summary of the information it makes available about these quakes here: [https://earthquake.usgs.gov/earthquakes/feed/v1.0/geojson.php](https://earthquake.usgs.gov/earthquakes/feed/v1.0/geojson.php)

The feed we want is all earthquakes of every magnitude that occurred in the last 24 hours: [https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_day.geojson](https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_day.geojson). If you click on this link, you'll recognize that what you're looking at is a GeoJSON file: a collection of features that have a specified geometry and numerous properties or non-spatial attributes.

Here's the code we use to add the earthquake GeoJSON to our map. If you're following along in Atom, add the lines of code as indicated, then view your changes in a web browser. 

In the ```head``` of the HTML file, after the line of code where you add the leaflet.js script, insert the following: 

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
```

This is a link to the JQuery library. JQuery is a common JavaScript library that makes it easy to make changes to a web page. In this case, we'll use it to load our GeoJSON file. 

Next, in the ```body``` of the HTML file, after the code where you initialize the map but before the ```</script>``` tag closes, add the following: 

```javascript
// load GeoJSON from an external file and add it to the map
$.getJSON("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_day.geojson",function(data){
    L.geoJson(data).addTo(mymap);
});
```

```$.getJSON``` loads the file located at the URL that is specified. Then, ```L.geoJon()``` creates a vector layer from the GeoJSON and adds it to the map with ```.addTo()```. 

Save your work and open it in your web browser and it should look like this: 

[Insert map]

## Let's make those markers clickable!

If we hover over the markers, we should see the cursor change from the panning hand to the pointing finger, suggesting that we can click on the markers. However, clicking doesn't seem to do anything! Let's change that. Let's make it so that when we click the markers, we get a pop-up window that tells us the location and magnitude of the earthquake and get a link we can click for more information. Thankfully each of these things (location, magnitude, and further info link) are available as properties in the earthquake GeoJSON. 

Change the code that creates the GeoJSON layer as follows, adding the ```pointToLayer``` option to the ```L.geoJson()``` method before we add the layer to the map: 

```javascript
$.getJSON("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_day.geojson",function(data){
    L.geoJson(data, {
        pointToLayer: function(feature, latlng){
            var marker = L.marker(latlng);
            marker.bindPopup("hello!");
            return marker;
        }
    }).addTo(mymap);
});
```

```pointToLayer``` is an option built into the ```L.geoJson()``` method that Leaflet uses to determine how to convert a point feature into a map layer. ```pointToLayer``` always accepts two arguments, the GeoJSON ```feature``` and ```latlng```, which indicates the location of the feature. We then return the features as some kind of Leaflet layer, in this case, a marker layer, specified by ```L.marker()```. (Later, we'll experiment with another kind of Leaflet layer, the circle marker, or L.circleMarker.) Each marker appears at the given feature's specified lat,lng location. 

Now, when we click on each earthquake marker, we should get a pop-up that reads "hello!" Let's make that text a little more helpful. Change the ```marker.bindPopup``` line of code as follows: 

```javascript
marker.bindPopup("Location: " + feature.properties.place + "<br>Magnitude: " + feature.properties.mag + "<br><a href =" + feature.properties.url +">More info</a>");
```

This code uses HTML to set the content of the marker pop-up. It selects information from GeoJSON using the ```feature.properties. ``` notation to display the 'place,' 'mag,' and 'url' properties for the selected feature. We can reference what these properties are in the [GeoJSON metadata](https://earthquake.usgs.gov/earthquakes/feed/v1.0/geojson.php).  

## Adding some style: proportional symbols

By default, the points are styled with generic blue markers. This is fine, but what if we wanted to style the markers based on some attribute, such as magnitude. We can use JavaScript to make a proportional symbol map using conditional statements. 

First, let's change our markers to circle markers. Make the following change to your code. JavaScript is case sensitive, so make sure you capitalize correctly!

```javascript
var marker = L.circleMarker(latlng);
```

By default, these are styled as blue circles with a radius of 10 pixels, and a partly transparent fill. We can change these style defaults with options that are specified within the ```L.circleMarker()``` method. Make the following change and view the results: 

```javascript
var marker = L.circleMarker(latlng, {radius: 2, color: 'red'});
```

Here we've made the radius just 2px across and changed the color of the circle to red. In web mapping, you can [define colors](https://www.w3schools.com/html/html_colors.asp) a number of ways, including with hex codes, rgb values, or---for a limited set of colors--- with standard names. Here we've used a standard name. 

Next, let's set the radius of the circle to change based on the magnitude of the earthquake. Make the following change to your code: 

```javascript
var marker = L.circleMarker(latlng, {radius: feature.properties.mag, color: 'red'});
```

Here we're pulling our radius value from the magnitude property of the GeoJSON. At the time of my writing this tutorial, there had been a lot of small earthquakes in North America and a smaller number of larger earthquakes in the Pacific and Indian Oceans. What do the patterns look like on the day you're mapping the data? 

Currently, the data is not classified, as is typical with proportional symbol maps of numeric data. But what if we were working with ordered categorical data? Let's say we wanted to put the quakes into three categories: tiny (quakes under mag 1), small (quakes between mag 1 and 2.5), medium (between mag 2.5 and 4.5), and large (quakes larger than mag 4.5). We can achieve this with an if/else statement: 

```javascript
$.getJSON("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_day.geojson",function(data){
	// add GeoJSON layer to the map once the file is loaded
		L.geoJson(data, {
			pointToLayer: function(feature, latlng){
                var radiusSize,
					mag = feature.properties.mag;
                if (mag > 4.5) radiusSize = 10;
				else if ( mag > 2.5) radiusSize = 5;
                else if (mag > 1) radiusSize = 2;
				else radiusSize = 1;
                var marker = L.circleMarker(latlng, {radius: radiusSize, color: 'red'});
			marker.bindPopup("Location: " + feature.properties.place + "<br>Magnitude: " + feature.properties.mag + "<br><a href =" + feature.properties.url +">More info</a>");
			return marker;
			}
		}).addTo(mymap);
});
```

