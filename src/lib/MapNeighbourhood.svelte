<script>

    import './ol.css';
    import '../assets/styles.css';

    import { onMount } from 'svelte';

    import notToronto from '../assets/ref/not-toronto.geo.json';
    import majorStreets from '../assets/ref/major-roads.geo.json';
    import labelStreets from '../assets/ref/major-streets-split.geo.json';
    import sixLabels from '../assets/ref/six-labels.geo.json';
    import shoreline from '../assets/ref/shoreline.geo.json';
    import neighbourhoods from '../assets/ref/neighbourhoods.geo.json';

    import {Map, View} from 'ol';
    import XYZ from 'ol/source/XYZ';
	import TileLayer from 'ol/layer/Tile';
    import {useGeographic} from 'ol/proj';
    import {get as getProjection} from 'ol/proj';
	import {getWidth} from 'ol/extent';


    import {Fill, Stroke, Style, Text} from 'ol/style';

    import GeoJSON from 'ol/format/GeoJSON';
	import VectorLayer from 'ol/layer/Vector';
	import VectorSource from 'ol/source/Vector';

	import {defaults as defaultControls} from 'ol/control';
    import {defaults as defaultInteractions} from 'ol/interaction';
	import {ScaleLine} from 'ol/control';
    

    let map = 0;
    let load = 0;
    let year = "1947";
    let streets = "on";

    var neighbourhoodOpacity = 0.8;


    // local streets layer
    var smallStreetsSource = new XYZ({
        url: "./streets-local/{z}/{x}/{y}.png"
    });
    var smallStreetsLayer = new TileLayer({
        minZoom: 14,
        maxZoom: 15,
        opacity: 1,
        source: smallStreetsSource
    });



    // not Toronto layer - include a background pattern with canvas

    const canvas = document.createElement('canvas');
    const context = canvas.getContext('2d');
    canvas.width = 5;
    canvas.height = 5;

    context.fillStyle = 'white'; // Fill color
    context.fillRect(0, 0, 5, 5);
    context.strokeStyle = '#D0D1C9'; // Stroke color
    context.lineWidth = 1;
    context.beginPath();
    context.moveTo(2, 3);
    context.lineTo(3, 2);
    context.stroke();

    const pattern = context.createPattern(canvas, 'repeat');

    

    // streets layer 2022ish
    var features = new GeoJSON().readFeatures(majorStreets, {
		});
	var vectorSource = new VectorSource({
		features
	});
	var textStyle = new Style({
            text: new Text({
                declutter: true,
                font: 'bold 12px "Roboto", "Arial Unicode MS", "sans-serif"',
                placement: 'line',
                scale: 1,
                fill: new Fill({
                    color: '#1E3765',
                }),
                stroke: new Stroke({
                    color: 'white',
                    width: 2
                })
            }),
            stroke: new Stroke({
                color: '#1E3765',
                width: 1
            })
		});
	var streetLayer = new VectorLayer({
		declutter: true,
        opacity: 0.75,
		source: vectorSource,
		style: function (feature) {
			textStyle.getText().setText(feature.get('n'));
        	return textStyle;
      	},		
	});

    // streets layer labels 2022ish
    var features = new GeoJSON().readFeatures(labelStreets, {
		});
	var vectorSource = new VectorSource({
		features
	});
	var textStyleLabels = new Style({
            text: new Text({
                declutter: true,
                font: 'bold 12px "Roboto", "Arial Unicode MS", "sans-serif"',
                placement: 'line',
                scale: 1,
                fill: new Fill({
                    color: '#0D534D',
                }),
                stroke: new Stroke({
                    color: 'white',
                    width: 2
                })
            }),
            stroke: new Stroke({
                color: '#1E3765',
                width: 1.5
            })
		});
	var streetLabelLayer = new VectorLayer({
		declutter: true,
		source: vectorSource,
        maxZoom: 15,
		minZoom: 13,
		style: function (feature) {
			textStyleLabels.getText().setText(feature.get('n'));
        	return textStyleLabels;
      	},		
	});


    


    // neighbourhoods
    var features = new GeoJSON().readFeatures(neighbourhoods, {
		});
	var vectorSource = new VectorSource({
		features
	});
    const neighbourhoodStyles = function(feature) {
        let color = "grey";
        if (feature.get('AREA_SHORT_CODE') >= 50) {color = '#0d534d' ;}
        else if (feature.get('AREA_SHORT_CODE') >= 30 ){color ='#5b8985';}
        else if (feature.get('AREA_SHORT_CODE') >= 10 ){color ='#a0b9b7';}
        else if (feature.get('AREA_SHORT_CODE') >= -10 ){color ='#f2f2f2';}
        else if (feature.get('AREA_SHORT_CODE') >= -30 ){color ='#f1da71';}
        else if (feature.get('AREA_SHORT_CODE') >= -50 ){color ='#f1c500';}
        else {color = '#d18100';}

            return  [
            new Style({
            stroke: new Stroke({
                color: 'white',
                width: 1,
            }),
            fill: new Fill({
                color: color,
            }),
            }),
        ]
        };
    var neighbourhoodsLayer = new VectorLayer({
		// declutter: true,
		source: vectorSource,
        style: neighbourhoodStyles
	});
    neighbourhoodsLayer.setOpacity(neighbourhoodOpacity);


    var features = new GeoJSON().readFeatures(notToronto, {
		});
	var vectorSource = new VectorSource({
		features
	});
	const style = new Style({
		fill: new Fill({
			color: pattern,
		}),
		stroke: new Stroke({
			color: '#1E3765',
			width: 1
		})
	});
	var notTorontoLayer = new VectorLayer({
		source: vectorSource,
		style: style
	});

    // shoreline layer
    var features = new GeoJSON().readFeatures(shoreline, {
		});
	var vectorSource = new VectorSource({
		features
	});
	var shorelineStyle= new Style({
        stroke: new Stroke({
            color: '#1E3765',
            width: 0.5
        })
    });
	var shorelineLayer = new VectorLayer({
		// declutter: true,
		source: vectorSource,
		style: function (feature) {
        	return shorelineStyle;
      	},		
	});


    // six area labels
    var features = new GeoJSON().readFeatures(sixLabels, {
		});
	var vectorSource = new VectorSource({
		features
	});
	var textSixStyleLabels = new Style({
            text: new Text({
                declutter: true,
                font: '17px "TradeGothicBold"',
                placement: 'point',
                scale: 1,
                fill: new Fill({
                    color: '#1E3765',
                }),
                stroke: new Stroke({
                    color: 'white',
                    width: 2
                })
            })
		});
	var sixLabelLayer = new VectorLayer({
		declutter: true,
		source: vectorSource,
		style: function (feature) {
			textSixStyleLabels.getText().setText(feature.get('name'));
        	return textSixStyleLabels;
      	},		
	});





    useGeographic();
	const resolutions = [];
	const matrixIds = [];
	const proj3857 = getProjection('EPSG:3857');
	const maxResolution = getWidth(proj3857.getExtent()) / 256;
	for (let i = 0; i < 20; i++) {
		matrixIds[i] = i.toString();
		resolutions[i] = maxResolution / Math.pow(2, i);
	}

    

    onMount(() => {

        load = 1;

        map = new Map({
			target: 'map2',
			layers: [smallStreetsLayer, streetLayer, streetLabelLayer, neighbourhoodsLayer,  notTorontoLayer, shorelineLayer, sixLabelLayer],
			view: new View({
				center: [-79.38,43.71],
                rotation: 17 * Math.PI / 180,
				zoom: 11.2,
				maxZoom: 15.2,
				minZoom: 10,
				extent: [-79.8302,43.3046,-78.9160,44.0295],
			}),
			controls: defaultControls(),
            interactions: defaultInteractions({
                mouseWheelZoom: false,
            }),
		})
        
        map.addControl(new ScaleLine({units: 'metric', maxWidth: 75}));
    
    });


    // toggling the land cover layer by year


    // toggle streets (2022) on and off


    
    

</script>



<div id="legend2">
    <svg width="280px" height="70px">
        <text id="legend-title" x="0" y="18">% Change in Forest (1954-2017)</text>
        <rect x="240" y="25" width="39" height="15" fill="#0d534d" stroke="#FFFFFF" fill-opacity={neighbourhoodOpacity} stroke-width="1"/>
        <rect x="200" y="25" width="39" height="15" fill="#5b8985" stroke="#FFFFFF" fill-opacity={neighbourhoodOpacity} stroke-width="1"/>
        <rect x="160" y="25" width="39" height="15" fill="#a0b9b7" stroke="#FFFFFF" fill-opacity={neighbourhoodOpacity} stroke-width="1"/>
        <rect x="120" y="25" width="39" height="15" fill="#f2f2f2" stroke="#FFFFFF" fill-opacity={neighbourhoodOpacity} stroke-width="1"/>
        <rect x="80" y="25" width="39" height="15" fill="#f1da71" stroke="#FFFFFF" fill-opacity={neighbourhoodOpacity} stroke-width="1"/>
        <rect x="40" y="25" width="39" height="15" fill="#f1c500" stroke="#FFFFFF" fill-opacity={neighbourhoodOpacity} stroke-width="1"/>
        <rect x="0" y="25" width="39" height="15" fill="#d18100" stroke="#FFFFFF" fill-opacity={neighbourhoodOpacity} stroke-width="1"/>
        <text id="legend-label" x="24" y="55">-50%</text>
        <text id="legend-label" x="64" y="55">-30%</text>
        <text id="legend-label" x="104" y="55">-10%</text>
        <text id="legend-label" x="150" y="55">10%</text>
        <text id="legend-label" x="190" y="55">30%</text>
        <text id="legend-label" x="230" y="55">50%</text>
    </svg>
</div>

<div id="map2">

</div>



<style>

    #map2 {
        margin: 0 auto;
        max-width: 1000px;
        width: 100%;
        max-height: calc(100vh - 200px);
        height: 500px;
        border-bottom: solid 1px var(--brandDarkBlue);
        border-top: solid 1px var(--brandDarkBlue);
        width: 100%;
        z-index: 3;
        cursor: move;
    }

    #legend2 {
        margin: 0 auto;
        margin-top: 10px;
        margin-bottom: 10px;
        width: 280px;
        height: 60px;
        padding: 0px;
    }

    #legend-title {
        font-family: 'TradeGothicBold', sans-serif;
        font-weight: normal;
        height: 25px;
        font-size: 20px;
        background-color:  var(--brandWhite);
        fill: var(--brandDarkBlue);
    }

    #legend-label {
        font-family: 'Roboto', sans-serif;
        font-weight: normal;
        font-size: 15px;
        background-color:  var(--brandWhite);
        fill: var(--brandDarkBlue);
    }

</style>
