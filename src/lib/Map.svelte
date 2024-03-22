<script>

	import './ol.css';

	import { onMount } from 'svelte';

	import notToronto from '../assets/ref/not-toronto.geo.json';
	import majorStreets from '../assets/ref/major-roads.geo.json';
	import labelStreets from '../assets/ref/major-streets-split.geo.json';
	import sixLabels from '../assets/ref/six-labels.geo.json';
	import shoreline from '../assets/ref/shoreline.geo.json';

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
	let year = "1954";
	let streets = "on";


	// initial land cover layer
	var landCoverSource = new XYZ({
		url: "./" + year + "/tiles/{z}/{x}/{y}.png"
	});
	var landCoverLayer = new TileLayer({
		opacity: 1,
		source: landCoverSource
	});

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
				font: '20px "TradeGothicBold"',
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

		load = 1;

		map = new Map({
			target: 'map',
			layers: [landCoverLayer, notTorontoLayer, smallStreetsLayer, streetLayer, streetLabelLayer, shorelineLayer, sixLabelLayer],
			view: new View({
				center: [-79.371,43.713],
				rotation: 17 * Math.PI / 180,
				zoom: 12,
				maxZoom: 15,
				minZoom: 12,
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

	function yearSelect(y) {
		year = y
	};

	function layerSwitch() {
		if (load > 0) {
			landCoverLayer.setSource(new XYZ({
				url: "./" + year + "/tiles/{z}/{x}/{y}.png"
			}))
		}
	}

	$: year && layerSwitch()


	// toggle streets (2022) on and off

	function streetSelect() {
		if (streets === "on") {
			streets = "off"
		} else {
			streets = "on"
		}
	}

	function toggleStreets() {
		if (load > 0) {
			if (streets === "on") {
				streetLayer.setOpacity(0.75);
				smallStreetsLayer.setOpacity(1);
				streetLabelLayer.setOpacity(1);
			} else {
				streetLayer.setOpacity(0);
				smallStreetsLayer.setOpacity(0);
				streetLabelLayer.setOpacity(0);
			}
		}
		
	}

	$: streets && toggleStreets()

	

</script>



<div id="map">

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

				<circle id="north-arrow-circle" cx="225" cy="28" r="10" />
				<line id="north-arrow-line" x1='225' y1='28' x2='227' y2='22' transform='rotate' ></line>
				<text x="225" y="14" rotate="17" class="land-cover-text">N</text>

			</svg>

		</div>

		<div id="years">
			
			<button class:selected="{year === '1939'}"  on:click={() => yearSelect("1939")}>1939</button>
			
			<button class:selected="{year === '1947'}" on:click={() => yearSelect("1947")}>1947</button>

			<button class:selected="{year === '1954'}" on:click={() => yearSelect("1954")}>1954</button>

			<!-- <button class:selected="{year === '1966'}"  on:click={() => yearSelect("1966")}>1966</button> -->
			
			<button class:selected="{year === '1973'}" on:click={() => yearSelect("1973")}>1973</button>

			<!-- <button class:selected="{year === '1982'}" on:click={() => yearSelect("1982")}>1982</button> -->

			<button class:selected="{year === '1995'}"  on:click={() => yearSelect("1995")}>1995</button>
			
			<!-- <button class:selected="{year === '2006'}" on:click={() => yearSelect("2006")}>2006</button> -->

			<button class:selected="{year === '2017'}" on:click={() => yearSelect("2017")}>2017</button>

		</div>

		<div id="toggle-streets-wrapper">
			<label>
				<input type=checkbox on:click={streetSelect} checked>
				Toggle Streets (2022 Data)
			</label>
		</div>

	</div>

</div>




<style>

	#map {
		position: relative;
		width: 100%;
		height: calc(100vh - 200px);
		max-height: 830px;
		border-bottom: solid 1px var(--brandDarkBlue);
		border-top: solid 1px var(--brandDarkBlue);
		width: 100%;
		z-index: 1;
		cursor: move;
	}

	#legend {
		position: absolute;
		bottom: 10px;
		right: 10px;
		height: 170px;
		width: 300px;
		background-color: rgba(255, 255, 255, 0.95);
		border: solid 1px var(--brandDarkBlue);
		border-radius: 3px;
		z-index: 2;
		cursor: default;
	}

	h3 {
		font-family: 'TradeGothicBold', sans-serif;
		font-weight: normal;
		height: 25px;
		font-size: 21px;
		background-color:  var(--brandWhite);
		color: var(--brandDarkBlue);
		padding-left: 10px;
		margin-top: 5px;
		margin-bottom: 5px;
		border-bottom: 2px solid var(--brandDarkBlue);
		border-top: 2px solid var(--brandDarkBlue);
		cursor: text;
		padding-bottom: 1px;
	}

	#land-cover {
		padding-left: 10px;
		padding-right: 10px;
		padding-top: 5px;
		border-bottom: 1px solid var(--brandDarkBlue);
	}
	#land-built {
		fill: var(--brandYellow);
		stroke: var(--brandGray);
		stroke-width: 1px;
	}
	#land-grass {
		fill: var(--brandMedGreen);
		stroke: var(--brandGray);
		stroke-width: 1px;
	}
	#land-forest {
		fill: var(--brandDarkGreen);
		stroke: var(--brandGray);
		stroke-width: 1px;
	}
	#land-bare {
		fill: var(--brandGray);
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
		font-size: 15px;
		fill: var(--brandDarkBlue);
		font-family: 'Roboto', sans-serif;
		cursor: text;
	}

	#north-arrow-circle {
		stroke: var(--brandDarkBlue);
		stroke-width: 2px;
		fill: none;
	}
	#north-arrow-line {
		stroke: var(--brandDarkBlue);
		stroke-width: 2px;
		fill: none;
	}

	#years {
		margin: 8px;
	}

	.selected {
		opacity: 1;
		border: solid 2px var(--brandLightBlue);
		border-radius: 0px;
	}

	button {
		background-color: var(--brandDarkBlue);
		color: var(--brandWhite);
		font-family: 'Roboto', sans-serif;
		font-size: 13px;
		height: 25px;
		width: 44px;
		border: solid 2px var(--brandWhite);
		border-radius: 0px;
		cursor: pointer;
		margin-bottom: 4px;
		opacity: 0.5;
	}

	button:hover {
		border: solid 2px var(--brandLightBlue);
		opacity: 1;
	}

	#toggle-streets-wrapper {
		border-top: solid 1px var(--brandDarkBlue);
		width: 300px;
		padding-top: 3px;
	}

	#toggle-streets-wrapper:hover {
		opacity: 0.84;
	}

	label {
		color: var(--brandDarkBlue);
		font-family: 'Roboto', sans-serif;
		font-size: 15px;
		padding-left: 5px;
		cursor: pointer;
	}

	input[type='checkbox']:checked {
		accent-color: var(--brandDarkBlue);
		border-color: white;
	}

</style>
