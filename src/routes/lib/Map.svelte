<script>

    import { onMount } from 'svelte';

    import notToronto from '../assets/ref/not-toronto.geo.json';
    import majorStreets from '../assets/ref/major-roads.geo.json';

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

    import ZoomSlider from 'ol/control/ZoomSlider';
	import {defaults as defaultControls} from 'ol/control';
	import {ScaleLine} from 'ol/control';

    // initial land cover layer
    var landCoverSource = new XYZ({
        url: "./historical-land-cover-toronto/1973/tiles/{z}/{x}/{y}.png"
        // url: 'https://maps.library.utoronto.ca/tiles1947/{z}/{x}/{y}.png'
    });
    var landCoverLayer = new TileLayer({
        opacity: 1,
        source: landCoverSource
    });

    // not Toronto layer
    var features = new GeoJSON().readFeatures(notToronto, {
		});
	var vectorSource = new VectorSource({
		features
	});
	const style = new Style({
		fill: new Fill({
			color: '#ffffff',
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
                // padding: [540,540,540,540],
                // overflow: true,
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
			// layers: [leftLayer, rightLayer, notTorontoLayer, streetLayer],
			layers: [landCoverLayer, notTorontoLayer, streetLayer],
			view: new View({
				center: [-79.38,43.70],
				zoom: 12.5,
				maxZoom: 15,
				minZoom: 12,
				extent: [-79.8302,43.3046,-78.9160,44.0295],
			}),
			controls: defaultControls().extend([new ZoomSlider()])
		});
    
    });

</script>



<div id="map"></div>




<style>

    #map {
        height: 100%;
        width: 100%;
    }


</style>


