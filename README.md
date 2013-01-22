# SVG-to-WKT.js

SVG-to-WKT converts [SVG](https://developer.mozilla.org/en-US/docs/SVG) (Scalable Vector Graphics) geometry data and XML documents into [WKT](http://en.wikipedia.org/wiki/Well-known_text) (Well-Known Text), a markup language for representing vector geometry on maps implemented by spatially-enabled databases like PostGIS and MySQL.

SVG-to-WKT supports all SVG graphic elements that directly encode geometric data: ```<circle>```, ```<ellipse>```, ```<line>```, ```<path>```, ```<polygon>```, ```<polyline>```, and ```<rect>```. SVG styles are ignored, since WKT has no notion of presentation, only shape.

SVG paths are converted to "frozen" WKT ```POLYGON```'s using the ```getPointAtLength``` method on ```<path>``` elements - curves are interpolated at a customizable density level and written as a series of fixed points.

## Quick Example

```js
SVGtoWKT.convert('<svg><polygon points="1,2 3,4 5,6" /><line x1="7" y1="8" x2="9" y2="10" /></svg>');

>>> "GEOMETRYCOLLECTION(POLYGON((1 2,3 4,5 6,1 2)),LINESTRING(7 8,9 10))"
```

## In the Browser

SVG-to-WKT uses jQuery (for XML parsing) and Underscore (for sanity).

```html
<script type="text/javascript" src="jquery.js"></script>
<script type="text/javascript" src="underscore.js"></script>
<script type="text/javascript" src="svg-to-wkt.js"></script>
```

## Documentation

* **[convert](#convert)**
* [line](#line)
* [polyline](#polyline)
* [rect](#rect)
* [circle](#circle)
* [ellipse](#ellipse)
* [path](#path)

---

<a name="convert" />
### SVGtoWKT.convert(svg)

Top-level routine that takes a raw SVG document and returns a WKT string.

__Arguments__

* {String} **svg** - A valid SVG document.

__Returns__

* {String} **wkt** - A WKT ```GEOMETRYCOLLECTION```.

__Example__

```js
SVGtoWKT.convert('<svg><polygon points="1,2 3,4 5,6" /></svg>');
>>> "GEOMETRYCOLLECTION(POLYGON((1 2,3 4,5 6,1 2)))"
```

---

<a name="line" />
### SVGtoWKT.line(x1, y1, x2, y2)

Constructs a WKT ```LINESTRING``` element from two points.

__Arguments__

* {Number} **x1** - The X coordinate of the start point.
* {Number} **y1** - The Y coordinate of the start point.
* {Number} **x2** - The X coordinate of the end point.
* {Number} **y2** - The Y coordinate of the end point.

__Returns__

* {String} **wkt** - A WKT ```LINESTRING```.

__Example__

```js
SVGtoWKT.line(1, 2, 3, 4);
>>> "LINESTRING(1 2,3 4)"
```

---

<a name="polyline" />
### SVGtoWKT.polyline()

Constructs a WKT ```LINESTRING``` element from the value of the ```points``` attribute on a SVG ```polyline``` element.

__Arguments__

* {String} **points** - The value of the ```points``` attribute on a SVG ```polyline``` element.

__Returns__

* {String} **wkt** - A WKT ```LINESTRING```.

__Example__

```js
SVGtoWKT.polyline('1,2 3,4');
>>> "LINESTRING(1 2,3 4)"
```

---

<a name="rect" />
### SVGtoWKT.rect()

Constructs a WKT ```POLYGON``` element from the coordinates of the top-left corner of a rectangle, the height/width, and (**TODO**) border radii.

__Arguments__

* {Number} **x** - The X coordinate of the top-left corner.
* {Number} **y** - The Y coordinate of the top-left corner.
* {Number} **width** - The width of the rectangle.
* {Number} **height** - The height of the rectangle.
* {Number} **rx** (TODO) - The horizontal border radius.
* {Number} **ry** (TODO) - The vertical border radius.

__Returns__

* {String} **wkt** - A WKT ```POLYGON```.

__Example__

```js
SVGtoWKT.rect(1, 2, 3, 4);
>>> "POLYGON((1 2,4 2,4 6,1 6,1 2))"
```

---

<a name="circle" />
### SVGtoWKT.circle()

Constructs a WKT ```POLYGON``` element from a circle center point and radius.

__Arguments__

* {Number} **cx** - The center X coordinate.
* {Number} **cy** - The center Y coordinate.
* {Number} **r** - The radius.
* {Number} **density** (optional, defaults to 1) - The number of ```POLYGON``` points that should be rendered per unit of linear pixel length. Higher density results in more points, higher resolution.

__Returns__

* {String} **wkt** - A WKT ```POLYGON```.

__Example__

```js
SVGtoWKT.circle(0, 0, 10);
>>> "POLYGON((10 0,9.950307753654014 0.9956784659581666,9.801724878485437 1.9814614319939758,9.555728057861407 2.947551744109042,9.214762118704076 3.8843479627469475,8.782215733702285 4.782539786213182,8.262387743159948 5.63320058063622,7.66044443118978 6.4278760968653925,6.982368180860728 7.158668492597185,6.2348980185873355 7.818314824680298,5.425462638657594 8.400259231507714,4.562106573531629 8.898718088114686,3.65341024366395 9.308737486442043,2.7084046814300518 9.62624246950012,1.7364817766693041 9.84807753012208,0.7473009358642417 9.972037971811801,-0.24930691738072913 9.996891820008162,-1.2434370464748516 9.92239206600172,-2.2252093395631434 9.749279121818237,-3.1848665025168432 9.479273461671319,-4.112871031306115 9.115058523116732,-4.999999999999998 8.660254037844387,-5.837436722347899 8.119380057158564,-6.6168583759685955 7.497812029677341,-7.330518718298263 6.801727377709193,-7.9713250722292255 6.038044103254774,-8.532908816321555 5.214352033794981,-9.00968867902419 4.338837391175582,-9.396926207859083 3.420201433256689,-9.69077286229078 2.4675739769029343,-9.888308262251286 1.4904226617617429,-9.987569212189223 0.4984588566069748,-9.987569212189223 -0.4984588566069723,-9.888308262251286 -1.4904226617617446,-9.69077286229078 -2.467573976902936,-9.396926207859085 -3.4202014332566866,-9.009688679024192 -4.33883739117558,-8.532908816321555 -5.214352033794984,-7.971325072229226 -6.038044103254771,-7.330518718298262 -6.801727377709195,-6.616858375968594 -7.497812029677342,-5.837436722347898 -8.119380057158565,-5.000000000000004 -8.660254037844384,-4.112871031306116 -9.115058523116732,-3.184866502516841 -9.479273461671319,-2.225209339563137 -9.749279121818237,-1.2434370464748494 -9.92239206600172,-0.24930691738073157 -9.996891820008162,0.7473009358642436 -9.972037971811801,1.7364817766692997 -9.848077530122081,2.708404681430051 -9.62624246950012,3.653410243663954 -9.308737486442041,4.562106573531628 -8.898718088114686,5.425462638657597 -8.400259231507713,6.234898018587334 -7.818314824680299,6.98236818086073 -7.158668492597183,7.660444431189778 -6.427876096865396,8.262387743159948 -5.6332005806362195,8.782215733702287 -4.782539786213178,9.214762118704076 -3.8843479627469475,9.555728057861408 -2.9475517441090386,9.801724878485437 -1.9814614319939772,9.950307753654013 -0.9956784659581729))"
```

---

<a name="ellipse" />
### SVGtoWKT.ellipse()

Constructs a WKT ```POLYGON``` element from a ellipse center point, horizontal radius, and vertical radius.

__Arguments__

* {Number} **cx** - The center X coordinate.
* {Number} **cy** - The center Y coordinate.
* {Number} **rx** - The horizontal radius.
* {Number} **ry** - The vertical radius.
* {Number} **density** (optional, defaults to 1) - The number of ```POLYGON``` points that should be rendered per unit of linear pixel length. Higher density results in more points, higher resolution.

__Returns__

* {String} **wkt** - A WKT ```POLYGON```.

__Example__

```js
SVGtoWKT.ellipse(0, 0, 10, 20);
>>> "POLYGON((10 0,9.979866764718844 1.2684783931312902,9.919548128307953 2.5318490714749853,9.819286972627067 3.7850248872082037,9.679487013963563 5.022959743621584,9.500711177409455 6.240668913969742,9.283679330160727 7.43324911320655,9.029265382866212 8.595898241783432,8.738493770697849 9.723934722009375,8.412535328311812 10.812816349111952,8.052702575310587 11.858158581092809,7.66044443118978 12.855752193730785,7.237340381050702 13.801580229642239,6.785094115571322 14.691834173150667,6.305526670845225 15.522929285835136,5.800569095711982 16.291519041006715,5.272254676105024 16.994508598990286,4.722710747726827 17.62906726895164,4.154150130018865 18.192639907090367,3.5688622159187187 18.682957205302134,2.9692037532827493 19.098044828881477,2.357589355094273 19.43623136647083,1.7364817766693041 19.69615506024416,1.1083819990101087 19.87676928922508,0.47581915823742404 19.97734678366016,-0.15865963834807806 19.9974825534775,-0.7924995685678854 19.93709551903885,-1.4231483827328502 19.796428837618656,-2.048066680651905 19.576048924295574,-2.6647381369003504 19.27684317119884,-3.2706796331742165 18.90001637429337,-3.8634512569312847 18.447085882091628,-4.440666126057741 17.91987548582672,-4.999999999999998 17.320508075688775,-5.539200638661103 16.65139709269543,-6.056096871376665 15.915236810616646,-6.54860733945285 15.114991487085165,-7.0147488770632105 14.25388342757726,-7.4526444967575465 13.335380010325835,-7.860530947427873 12.36317972441211,-8.236765814298327 11.341197277255418,-8.57983413234977 10.273547831468125,-8.888354486549234 9.16453043454821,-9.161084574320695 8.01861070813228,-9.396926207859083 6.840402866513378,-9.594929736144973 5.634651136828602,-9.75429786885407 4.406210655730807,-9.874388886763942 3.160027919466998,-9.954719225730846 1.9011208660836576,-9.99496542383185 0.6345586699613531,-9.994965423831852 -0.6345586699613482,-9.954719225730846 -1.9011208660836436,-9.874388886763942 -3.1600279194670016,-9.75429786885407 -4.406210655730812,-9.594929736144975 -5.634651136828587,-9.396926207859085 -6.840402866513373,-9.161084574320697 -8.018610708132268,-8.888354486549236 -9.164530434548197,-8.57983413234977 -10.273547831468129,-8.236765814298328 -11.341197277255413,-7.860530947427874 -12.363179724412106,-7.452644496757548 -13.33538001032583,-7.014748877063216 -14.253883427577252,-6.548607339452852 -15.114991487085163,-6.056096871376666 -15.915236810616642,-5.539200638661105 -16.651397092695426,-5.000000000000004 -17.320508075688767,-4.440666126057748 -17.91987548582671,-3.863451256931287 -18.447085882091628,-3.2706796331742187 -18.900016374293365,-2.664738136900355 -19.276843171198838,-2.0480666806519054 -19.576048924295574,-1.4231483827328524 -19.796428837618652,-0.792499568567888 -19.937095519038845,-0.15865963834808497 -19.9974825534775,0.47581915823741494 -19.97734678366016,1.1083819990101087 -19.87676928922508,1.7364817766692997 -19.696155060244163,2.357589355094266 -19.43623136647084,2.9692037532827404 -19.09804482888148,3.568862215918717 -18.682957205302134,4.154150130018868 -18.192639907090363,4.722710747726829 -17.62906726895164,5.272254676105024 -16.994508598990286,5.800569095711979 -16.291519041006715,6.305526670845221 -15.522929285835144,6.785094115571316 -14.691834173150674,7.237340381050701 -13.80158022964224,7.660444431189778 -12.855752193730792,8.052702575310583 -11.85815858109282,8.412535328311808 -10.812816349111964,8.738493770697845 -9.723934722009393,9.029265382866214 -8.595898241783424,9.283679330160727 -7.433249113206545,9.500711177409455 -6.240668913969741,9.679487013963563 -5.022959743621588,9.819286972627065 -3.7850248872082126,9.919548128307952 -2.5318490714749986,9.979866764718844 -1.2684783931312904))"
```

---

<a name="path" />
### SVGtoWKT.path()

Constructs a WKT ```POLYGON``` element from a SVG path string. If the path has "holes" - closed paths inside of closed paths (eg, letters) - they are translated to the WKT subtracted-polygon syntax (```POLYGON((outer1 outer1,...),(inner1 inner2,...))```).

__Arguments__

* {Number} **d** - A SVG path string, perhaps from the ```d``` attribute on a ```<path>``` element.
* {Number} **density** (optional, defaults to 1) - The number of ```POLYGON``` points that should be rendered per unit of linear pixel length. Higher density results in more points, higher resolution.

__Returns__

* {String} **wkt** - A WKT ```POLYGON```.

__Example__

```js
SVGtoWKT.path('M10 10 C 20 20, 40 20, 50 10');
>>> "POLYGON((10 10,10.721701622009277 10.688758850097656,11.474231719970703 11.343710899353027,12.255242347717285 11.964431762695312,13.06243896484375 12.55070686340332,13.8936185836792 13.102474212646484,14.746672630310059 13.619773864746094,15.619556427001953 14.10286808013916,16.51036262512207 14.5520601272583,17.417299270629883 14.967721939086914,18.338693618774414 15.350268363952637,19.272987365722656 15.700133323669434,20.21872901916504 16.017763137817383,21.174562454223633 16.30359649658203,22.13922882080078 16.55805015563965,23.111543655395508 16.781517028808594,24.090402603149414 16.9743595123291,25.074804306030273 17.136581420898438,26.0637149810791 17.268537521362305,27.056142807006836 17.370677947998047,28.051172256469727 17.44318389892578,29.047916412353516 17.486186981201172,30.045499801635742 17.49976348876953,31.04305648803711 17.483816146850586,32.03968048095703 17.43796730041504,33.03450393676758 17.36260414123535,34.02663040161133 17.257596969604492,35.01514434814453 17.122758865356445,35.999088287353516 16.957855224609375,36.97746658325195 16.76259994506836,37.94917297363281 16.536439895629883,38.91305923461914 16.279020309448242,39.86800003051758 15.990194320678711,40.812721252441406 15.669539451599121,41.74586486816406 15.316620826721191,42.66596984863281 14.93099308013916,43.571468353271484 14.51222038269043,44.460670471191406 14.059896469116211,45.331787109375 13.573655128479004,46.18289566040039 13.05319595336914,47.011962890625 12.498309135437012,47.816856384277344 11.908907890319824,48.595340728759766 11.285054206848145,49.34511947631836 10.626984596252441,49.908992767333984 9.999994277954102,48.9112663269043 9.999994277954102,47.913543701171875 9.999994277954102,46.91581726074219 9.999994277954102,45.918094635009766 9.999994277954102,44.92036819458008 9.999994277954102,43.92264175415039 9.999995231628418,42.92491912841797 9.999995231628418,41.92719268798828 9.999995231628418,40.92947006225586 9.999995231628418,39.93174362182617 9.999995231628418,38.93402099609375 9.999995231628418,37.93629455566406 9.999996185302734,36.938568115234375 9.999996185302734,35.94084548950195 9.999996185302734,34.943119049072266 9.999996185302734,33.945396423339844 9.999996185302734,32.947669982910156 9.999996185302734,31.949947357177734 9.99999713897705,30.952220916748047 9.99999713897705,29.95449447631836 9.99999713897705,28.956771850585938 9.99999713897705,27.95904541015625 9.99999713897705,26.961318969726562 9.99999713897705,25.963600158691406 9.99999713897705,24.96587371826172 9.999998092651367,23.96814727783203 9.999998092651367,22.970420837402344 9.999998092651367,21.972694396972656 9.999998092651367,20.9749755859375 9.999998092651367,19.977249145507812 9.999998092651367,18.979522705078125 9.999999046325684,17.981796264648438 9.999999046325684,16.98406982421875 9.999999046325684,15.986351013183594 9.999999046325684,14.988624572753906 9.999999046325684,13.990898132324219 9.999999046325684,12.993171691894531 10,11.995452880859375 10,10.997726440429688 10))"
```

---

## Credits

The ```path``` method follows the approach described by Guilherme Mussi on his blog: "[Converting SVG paths to polygons](http://whaticode.com/2012/02/01/converting-svg-paths-to-polygons)."

This document follows the layout used by Caolan McMahon (@caolan) in projects like @caolan/async.
