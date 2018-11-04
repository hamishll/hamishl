---
layout: post
title: findapcfor.me
cover: cover_findapcforme.png
date:   2017-03-19 12:00:00
categories: posts
---

## Introduction

[findapcfor.me](http://findapcfor.me) is a web app for finding available computers at The University of Manchester. It uses AJAX alongside the Google Maps API in order to map...

The university tracks the status of PC clusters in an XML table, hosted [here](http://www.itservices.manchester.ac.uk/clusteravailability/avail.php). Each location is described something like this:

{% highlight XML %}
<PLPlace>
   <name>AGLC Floor -1</name>
   <description />
   <open>true</open>
   <latitude>53.464630</latitude>
   <longitude>-2.233479</longitude>
   ...
   <locationCode>010AA</locationCode>
   <locationName>Alan Gilbert Learning Commons</locationName>
   <availability>29 seats taken, 31 seats available</availability>
</PLPlace>
{% endhighlight %}

## Pulling the XML document (AJAX)

We'll use AJAX to request this file. Below is a basic function to achieve this:

{% highlight js %}
function downloadUrl(url,callback) {
    var request = window.ActiveXObject ?
         new ActiveXObject('Microsoft.XMLHTTP') :
         new XMLHttpRequest;
     
    request.onreadystatechange = function() {
        if (request.readyState == 4) {
            //request.onreadystatechange = doNothing;
            callback(request, request.status);
        }
    };
     
    request.open('GET', url, true);
    request.send(null);
}
{% endhighlight %}
<br />  
We can then call each row in the table by refering to the DOM and using the getElementsByTagName method. For example:

{% highlight js %}
// store the elements for each place
markers = xml.documentElement.getElementsByTagName("PLPlace");

// loop through each location (and plot markers)
for (var i = 0; i < markers.length; i++) {
	var name = markers[i].getElementsByTagName("name")[0].innerHTML;
	...
	// rest of the code
}
{% endhighlight %}

## Initialising our google map

Let's create a new map with coordinates centered on University Place:

{% highlight js %}
  var map = new google.maps.Map(document.getElementById('map'), {
    zoom: 15,
    center: {lat: 53.469369, lng: -2.233706}
{% endhighlight %}

## Styling markers based on availability

What we want to achieve is a gradient that goes from green, to yellow, to red as availability decreases. We'll set a threshold value of 50, above which markers will simply appear green as there is plenty of space.

Navigating through this colour space isn't as simple as a linear descent. In RGB values:

 - Green = 0 , 255 , 0
 - Yellow = 255, 255, 0
 - Red = 255, 0, 0

 So first we'll increase the red values, until we're halfway below our threshold, and then decrease the green values after this point:

{% highlight js %}
var dangerZone = 50;
if (free < (dangerZone/2)) {
    rCol = 255;
    gCol = Math.round(200 * (2 * free/dangerZone));
} else if (free < dangerZone) {
    rCol = Math.round(255 * (1 - (free - dangerZone/2)/(dangerZone/2)));
    gCol = 200;
} else {
  rCol = 0;
  gCol = 200;
}
var markerColor = rgbToHex(rCol, gCol, 0);
{% endhighlight %}

<br />
The rgbToHex function at the end converts this RGB value to a hex (which is the color format used by the google maps API).

## Determining marker size

We'll set the scale of the marker to be a proportion of the max number of computers, which is 182 in the George Begg cluster.

{% highlight js %}
var circle = {
  path: google.maps.SymbolPath.CIRCLE,
  fillColor: markerColor,
  fillOpacity: .8,
  scale: 10 + 30*(free/maxSpaces),
  strokeColor: 'white',
  strokeWeight: 1.5
};
{% endhighlight %}

## Getting coordinates

{% highlight js %}
 var point = new google.maps.LatLng(
    parseFloat(markers[i].getElementsByTagName("latitude")[0].innerHTML) + deltax,
    parseFloat(markers[i].getElementsByTagName("longitude")[0].innerHTML) + deltay);
{% endhighlight %}

## Placing markers and infoboxes

The google maps API have two objects for displaying content - markers and info boxes. We'll format them as such:

{% highlight js %}
var html = "<div class='infowindow'><b>" + name + "</b><br/>" + address  + "</b><br/>" + availability + '<br/></div>';
var marker = new google.maps.Marker({
  map: map,
  position: point,
  icon: circle,
  title: name,
  label: {
    text: free,
    color: 'white',
  },
  // clusters with greater availability have a higher zIndex
  zIndex: parseInt(free),
});
{% endhighlight %}

## Pushing objects to the map

We'll push the markers to the map as such:

{% highlight js %}
map.markers.push(marker);
{% endhighlight %}
<br />
HTML boxes are a little trickier, as we need to listen for interaction through clicking. We'll use the following function:

{% highlight js %}
function bindInfoWindow(marker, map, infoWindow, html) {
  google.maps.event.addListener(marker, 'click', function() {
    infoWindow.setContent(html);
    infoWindow.open(map, marker);
  });
}
{% endhighlight %}
           
## Conclusion

Check out the code on GitHub:
[findapcfor.me on GitHub](http://github.com/hamishll/findapcfor.me)
