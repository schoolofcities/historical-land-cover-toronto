<script>

    import './ol.css';

    import { onMount } from 'svelte';

    import notToronto from '../assets/ref/not-toronto.geo.json';
    import majorStreets from '../assets/ref/major-roads.geo.json';
    import labelStreets from '../assets/ref/major-streets-split.geo.json';
    import sixLabels from '../assets/ref/six-labels.geo.json';

    import {Map, View} from 'ol';
    import XYZ from 'ol/source/XYZ';
	import TileLayer from 'ol/layer/Tile';
    import {useGeographic} from 'ol/proj';
    import {get as getProjection} from 'ol/proj';
	import {getWidth} from 'ol/extent';


    import {Fill, Stroke, Style, Text, Circle} from 'ol/style';

    import GeoJSON from 'ol/format/GeoJSON';
	import VectorLayer from 'ol/layer/Vector';
	import VectorSource from 'ol/source/Vector';

	import {defaults as defaultControls} from 'ol/control';
	import {ScaleLine} from 'ol/control';

    // initial land cover layer
    var landCoverSource = new XYZ({
        url: "./historical-land-cover-toronto/1973/tiles/{z}/{x}/{y}.png"
    });
    var landCoverLayer = new TileLayer({
        opacity: 1,
        source: landCoverSource
    });

    // local streets layer
    var smallStreetsSource = new XYZ({
        url: "./historical-land-cover-toronto/streets-local/{z}/{x}/{y}.png"
    });
    var smallStreetsLayer = new TileLayer({
        minZoom: 14,
        maxZoom: 15,
        opacity: 1,
        source: smallStreetsSource
    });

    // not Toronto layer
    var features = new GeoJSON().readFeatures(notToronto, {
		});
	var vectorSource = new VectorSource({
		features
	});
	const style = new Style({
		fill: new Fill({
			color: '#FCFCFC',
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


    // six area labels
    var features = new GeoJSON().readFeatures(sixLabels, {
		});
	var vectorSource = new VectorSource({
		features
	});
	var textSixStyleLabels = new Style({
            text: new Text({
                declutter: true,
                font: 'bold italic 20px "Roboto", "Arial Unicode MS", "sans-serif"',
                placement: 'point',
                scale: 1,
                fill: new Fill({
                    color: '#1E3765',
                }),
                stroke: new Stroke({
                    color: 'white',
                    width: 3
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

        map = new Map({
			target: 'map',
			layers: [landCoverLayer, notTorontoLayer, smallStreetsLayer, streetLayer, streetLabelLayer, sixLabelLayer],
			view: new View({
				center: [-79.38,43.70],
                rotation: 17 * Math.PI / 180,
				zoom: 12.5,
				maxZoom: 15,
				minZoom: 12,
				extent: [-79.8302,43.3046,-78.9160,44.0295],
			}),
			controls: defaultControls()
		}).addControl(new ScaleLine({units: 'metric', maxWidth: 75}));
    
    });

</script>





<div id="legend">

    <h3>Historical Land-Cover Toronto</h3>

    <div id="land-cover">

        <svg class="" height="45" width="300">

            <rect id="land-built" width="15" height="15" x="1" y="1"/>
            <text x="19" y="15" class="land-cover-text">Built</text>

            <rect id="land-grass" width="15" height="15" x="61" y="1"/>
            <text x="79" y="15" class="land-cover-text">Grass</text>

            <rect id="land-forest" width="15" height="15" x="130" y="1"/>
            <text x="148" y="15" class="land-cover-text">Forest</text>

            <rect id="land-bare" width="15" height="15" x="1" y="22"/>
            <text x="19" y="36" class="land-cover-text">Bare</text>

            <rect id="land-water" width="15" height="15" x="61" y="22"/>
            <text x="79" y="36" class="land-cover-text">Water</text>

            <rect id="land-nodata" width="15" height="15" x="130" y="22"/>
            <text x="148" y="36" class="land-cover-text">No Data</text>

        </svg>

    </div>

</div>

<div id="map"></div>


<style>

    #map {
        height: 100%;
        width: 100%;
    }

    #legend {
        position: absolute;
        top: calc(100vh - 250px);
        right: 10px;
        height: 213px;
        width: 300px;
        background-color: rgba(255, 255, 255, 0.90);
        border: solid 1px var(--brandDarkBlue);
        border-top-left-radius: 0px;
        z-index: 1;
        cursor: default;
    }

    h3 {
        font-family: TradeGothicBold;
        font-size: 22px;
        background-color: var(--brandDarkBlue);;
        color: var(--brandWhite);
        padding-left: 10px;
        margin-top: 3px;
        margin-bottom: 5px;
        border-bottom: 1px solid var(--brandDarkBlue);
        cursor: text;
    }

    #land-cover {
        padding-left: 10px;
        padding-right: 10px;
        padding-top: 5px;
        border-bottom: 1px solid var(--brandDarkBlue);
    }
    #land-built {
        fill: var(--brandRed);
        stroke: var(--brandGray);
        stroke-width: 1px;
    }
    #land-grass {
        fill: var(--brandLightGreen);
        stroke: var(--brandGray);
        stroke-width: 1px;
    }
    #land-forest {
        fill: var(--brandDarkGreen);
        stroke: var(--brandGray);
        stroke-width: 1px;
    }
    #land-bare {
        fill: var(--brandYellow);
        stroke: var(--brandGray);
        stroke-width: 1px;
    }
    #land-water {
        fill: var(--brandLightBlue);
        stroke: var(--brandGray);
        stroke-width: 1px;
    }
    #land-nodata {
        fill: var(--brandWhite);
        stroke: var(--brandGray);
        stroke-width: 1px;
    }

    .land-cover-text {
        font-size: 16px;
        fill: var(--brandDarkBlue);
        font: 'Roboto', sans-serif;
        cursor: text;
    }
    
</style>


