# Shapefiles in Leaflet verwenden

Manchmal muss man mit Datensätzen arbeiten, die nicht als GeoJSON vorliegen.

Lösung: Konvertierung der Daten mit [QGis](https://www.qgis.org/).

Quelldatensatz: [Viertelgrenzen in Oberösterreich](https://www.data.gv.at/katalog/dataset/d90f10f3-2a91-4324-9d88-65e7e2e1caac)

[Online converter](https://mygeodata.cloud/conversion) funktionieren auch, aber
keine Möglichkeit die Genauigkeit beim Export einzustellen. Folglich werden aus
1.7MB Shapefile 3MB GeoJSON.

Wikipedia zur Genauigkeit von WGS84: <https://wiki.openstreetmap.org/wiki/Precision_of_coordinates>

* Herunterladen Datensatz im Shape-Format
* QGis öffnen, Datensatz laden (CRS GK)
* ev. Hintergrund-Layer OSM in QGis zur optischen Referenzierung
* Export Layer in GeoJSON
  * *RFC7946* optional (neuer Standard, beide funktionieren)
  * 5 Kommastellen für +/- 1m Genauigkeit
  * *persist layer metadata* für die Popup-Anzeige der Viertel-Namen

[Template zur Visualisierung](https://webmapping22s.github.io/templates/ooe.zip)

Javascript Code in `main.js`

```javascript
async function loadBorders(url) {
    let response = await fetch(url);    
    let geojson = await response.json();
    // console.log(geojson);

    let overlay = L.featureGroup();
    layerControl.addOverlay(overlay, "Viertelgrenzen OOE");
    overlay.addTo(map);

    L.geoJSON(geojson, {
        onEachFeature: function (feature, layer) {
            // console.log('FT: ', feature);
            layer.bindPopup(`<strong>${feature.properties.VIERTEL_NA}</strong>`);
        },
    }).addTo(overlay);
}
loadBorders("data/borders.geojson");
```