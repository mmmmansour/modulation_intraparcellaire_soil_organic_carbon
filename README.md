# modulation_intraparcellaire_soil_organic_carbon



//var sand_mean = ee.Image('projects/soilgrids-isric/sand_mean').clip(roi)
var sand_mean = ee.Image("projects/soilgrids-isric/soc_mean").clip(roi)
//print example image metadata and description
print(sand_mean)
var sand_mean = sand_mean.select('soc_0-5cm_mean')
                        .reproject({crs: 'EPSG:4326', scale: 5});

var palettes = require('users/gena/packages:palettes');

// Choose and define a palette
var palette = palettes.colorbrewer.YlOrBr[7];
//Add all layer to Map
Map.addLayer(sand_mean.select('soc_0-5cm_mean').focal_median(50,'circle','meters'),{min: 50, max: 1000,palette: palette},'SoilGrids250m 2.0 - Sand content ISRIC_0_5cm')

//Set basemap to Hybrid view
Map.setOptions('HYBRID')
Map.centerObject(roi, 12)






///////////////////////:legende///////////////////
var palettes = require('users/gena/packages:palettes');

// Choose and define a palette
var palette = palettes.colorbrewer.YlOrBr[7];
var viz = {palette: palette, min: 50.5, max: 98.5};

var legend = ui.Panel({
  style: {
    position: 'bottom-left',
    padding: '8px 15px'
  }
});

// Create and add the legend title.
var legendTitle = ui.Label({
  value: 'Organic_carbon (g/kg)',
  style: {
    fontWeight: 'bold',
    fontSize: '18px',
    margin: '0 0 4px 0',
    padding: '0'
  }
});

legend.add(legendTitle);

// create the legend image
var lon = ee.Image.pixelLonLat().select('latitude');
var gradient = lon.multiply((viz.max-viz.min)/100.0).add(viz.min);
var legendImage = gradient.visualize(viz);
 
// create text on top of legend
var panel = ui.Panel({
widgets: [
ui.Label(viz['max'])
],
});
 
legend.add(panel);
 
// create thumbnail from the image
var thumbnail = ui.Thumbnail({
image: legendImage,
params: {bbox:'0,0,10,100', dimensions:'10x200'},
style: {padding: '1px', position: 'bottom-center'}
});
 
// add the thumbnail to the legend
legend.add(thumbnail);
 
// create text on top of legend
var panel = ui.Panel({
widgets: [
ui.Label(viz['min'])
],
});

legend.add(panel);
Map.add(legend);
