# Google-Earth-Engine
Javascript code for Google Earth Engine. The code below was written to extract and map data for my ROI.

The first image illustrates the treatment area, in pink (Payment for Ecosystem Services area), and the control area, in green. 

![Screen Shot 2021-04-28 at 11 20 42](https://user-images.githubusercontent.com/78986090/116380015-de28e880-a813-11eb-9893-254ca5001f0b.png)

The second image illustrates the area when the outcome variable is added (Hansen deforestation dataset)

![Screen Shot 2021-04-28 at 11 23 26](https://user-images.githubusercontent.com/78986090/116380579-61e2d500-a814-11eb-87d9-88954237bce8.png)

The third image illustrates the area when, on top of the first two images, the covariates are added (urban areas and roads). 

![Screen Shot 2021-04-28 at 11 23 41](https://user-images.githubusercontent.com/78986090/116380618-6a3b1000-a814-11eb-9e05-ea0dfd12ff2b.png)

The fourth and fifth images are charts I generated to explore the data. 

4. Chart for deforestation per year (2000-2020) in the PES area
![ee-chartPES](https://user-images.githubusercontent.com/78986090/116381007-cb62e380-a814-11eb-93be-e838b0dd1724.png)

5. Chart for deforestation per year (2000-2020) in the Control area
![ee-chartControl](https://user-images.githubusercontent.com/78986090/116381026-cef66a80-a814-11eb-9849-449dc055960e.png)




```

// --------- calling the vectors

var SP = ee.FeatureCollection('users/juliaabdalla/SaoPaulo-area');

var PB = ee.FeatureCollection('users/juliaabdalla/paraiba_do_sul_limite');

// --------- trasnforming SP to feature

var geometrySP = SP.geometry();

var featureSP = ee.Feature(geometrySP);

// --------- trasnforming PB to feature

var geometryPB = PB.geometry();

var featurePB = ee.Feature(geometryPB); 


// --------- intersecting SP and PB: treatment and control groups

var intersection = featurePB.intersection(geometrySP);

print(intersection);

// --------- filtering intersction to Mata Atlantica

var ROI = ee.FeatureCollection(intersection); 



/* / --------- ajudsting MA

var MA = ee.FeatureCollection('users/juliaabdalla/SP_mataatlas18_19');

var geometryMA = MA.geometry();

var MAfilter = exportROI.filterBounds(geometryMA);

//Map.addLayer(MAfilter)

on hold */ 

// // --------- creating control group: (ROI) - (PES)

var PES = ee.FeatureCollection('users/juliaabdalla/PES_ROI');

var PES_geometry = PES.geometry();

var feature_PES = ee.Feature(PES_geometry);

/* var filter1 = ee.Filter.bounds(PES);

var filter2 = filter1.not();

var control = ROI.filter(filter2);

*/ 

var PES2 = intersection.difference(feature_PES);

var control = ee.FeatureCollection(PES2);

//Map.addLayer(SP);
Map.centerObject(ROI);


Map.addLayer(ROI);
Map.addLayer(PES, {'color':'magenta'});

Map.addLayer(PES2, {'color': 'green'});


// Export an ee.FeatureCollection as an Earth Engine asset.


// ------- Export
/*
Map.addLayer(PAS, {'color':'green'});
Map.addLayer(intersection);
Map.addLayer(interPAS_ROI, {'color':'pink'});

*/ 

var hansenPES = ee.Image("UMD/hansen/global_forest_change_2020_v1_8")
  .select(['lossyear'])
  .clip(PES)

var hansenControl = ee.Image("UMD/hansen/global_forest_change_2020_v1_8")
  .select(['lossyear'])
  .clip(control)

var urban = ee.FeatureCollection("users/juliaabdalla/UrbanAreas300000hab")
  .filterBounds(ROI)
  
var roads = ee.FeatureCollection('users/juliaabdalla/roads')
  .filterBounds(ROI);
    
Map.addLayer(hansenPES)
Map.addLayer(hansenControl)
Map.addLayer(roads, {'color':'blue'})
Map.addLayer(urban, {'color':'yellow'})

var chartPES = ui.Chart.image
  .histogram({
    image: lossyearPES,
    region: PES,
    maxPixels: 1e9
  });
  
print(chartPES);

var chartControl = ui.Chart.image
  .histogram({
    image: lossyearCoontrol,
    region: control,
    maxPixels: 1e9
  });
  
print(chartPES);

```
