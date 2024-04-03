<script>

	import { afterUpdate } from 'svelte';

	import Top from "../lib/TopSofC.svelte";
	import Construction from "../lib/Construction.svelte";
	import MapLand from "../lib/Map.svelte";
	import MapNeighbourhood from '../lib/MapNeighbourhood.svelte';
	import SankeyChart from '../lib/SankeyChart.svelte';
	import GiniChart from "../lib/Gini.svelte";
	import OneKmChart from "../lib/kmChart.svelte";
	
	import '../assets/styles.css';

	import topImage from '../assets/top-image.png';

	// function shiftDown() {
	// 	window.scrollTo({
	// 	top: window.pageYOffset + 300,
	// 	behavior: 'smooth'
	// 	});
	// }

	// afterUpdate(() => {
	// 	window.scrollTo(0, window.pageYOffset);
	// });

</script>



<svelte:head>

	<meta
		name="viewport"
		content="width=device-width, initial-scale=1, minimum-scale=1"
	/>
	
</svelte:head>



<Top/>


<main>

	<div class="top-image">
		<img src="{topImage}" alt="Your Image Description">
	</div>
	
	
	<div class="title">

		<h1>
			Evolving City
		</h1>
		<h2>
			Tracking access to nature over time in Toronto
		</h2>
		<p>
			<a href="https://francoisecardou.wixsite.com/site" target="_blank">Françoise Cardou</a>, <a href="https://github.com/nsookhan" target="_blank">Nicholas Sookhan</a>, <a href="https://jamaps.github.io/" target="_blank">Jeff Allen</a>, <a href="https://www.researchgate.net/profile/Daniel-Silver-4" target="_blank">Dan Silver</a>, <a href="https://www.macivorlab.ca/" target="_blank">Scott MacIvor</a>, <a href="https://cubes-labs.com/" target="_blank">Marc Cadotte</a>
			<br>
			<br>
			Funded by a <a href="https://schoolofcities.utoronto.ca/programs-opportunities/urban-challenge-grant/" target="blank">School of Cities Urban Challenge Grant</a>, Toronto and Region Conservation Authority, and Mitacs.
		</p>

	</div>


	<div class="text">
		<p>
			If you were headed home to one of Toronto’s downtown neighbourhoods after a walk in the Greenbelt on a hot summer day, you would most likely also brace for the heat of the city core. Because concrete traps heat during the day and releases it during the night, cities tend to be several degrees warmer than surrounding natural areas. Though this temperature difference is small, it is enough to affect the health and wellbeing of those who live in the city. 
		</p>
		<p>
			Trees, whether along streets or in forest fragments, can help. They reflect sunlight, provide shade and convert heat to water vapour. However, benefits from this cooling require one lives and/or works near greenspaces - the closer the better. What happens to neighbourhoods that do not have ready access to green spaces? With climate change, we are experiencing longer and more intense summer heat waves, and it is critical we understand the many ways in which access to nature will shape city life in the future. One way we can begin to do this is by first looking into the past. 
		</p>
		
	</div>

	<MapLand/>

	<div class="text">
		<p>
			Cities change over time. As land-use demand ebbs and flows, farm fields are converted into residential neighbourhoods, industrial parks and commercial areas. Marginal spaces like those not amenable to construction constitute the remnant greenspaces of a time before urbanisation was the dominant feature. These are often transformed into parks which, left to their own devices, regenerate into forests that provide ecosystem services to the new urban area. Think of Toronto’s ravine system. 
		</p>
		<p>
			If access to nature is something that residents want and are willing to pay for (and <a href="http://community-voices.report/a-survey-experiment-to-identify-neighbourhood-priorities/" target="blank">there is evidence that this is true in Toronto</a>, especially among higher income residents), and if supply of greenspace remains fixed, it can affect not just health and wellbeing but lead to growth in nearby property values. There is good reason then to wonder whether access to nature is involved in feedback that leads to unequal distribution of services across different segments of the population, specifically those health benefits that will become critical under climate change. 
		</p>
		<p>
			One challenge to the study of such feedback has been the lack of information on urban nature during the process of city growth. Where were the forests and how large were they? Were they always close to where people lived, or far? Our team combined emerging methods in machine learning and historical photographic material to begin answering some of these questions. 
		</p>
		<p>
			Nowadays, satellites provide detailed imagery covering the full spectrum of colours we can see, and some we can’t, making it easier to map natural areas. Yet satellite imaging is still a relatively new technology with the oldest images from the late 70s, long after the establishment and growth of most north american cities. Luckily, aeroplanes have been around for longer, and we have been flying airborne cameras to document city shape going back to the 1930s. Taken from a low altitude, these black and white photographs contain a treasure trove of information, but it needs to be extracted. 
		</p>
		<p>
			Going from a black and white images to land cover categories (whether a particular area is wooded, grassy, built-up, bare or, well, watery) has up until now involved painstaking visual interpretation by experts - a time-consuming task that limits the number of photographs that can be analysed. We developed a model that breaks down images into smaller areas of manageable size and identifies wooded areas, grass, built up land, bare ground and water bodies in a fraction of the time. 
		</p>
		<p>
			Convolutional neural networks (CNN) are a state-of-the-art technique used in image analysis that generate a label for each pixel in input imagery. To achieve accurate label individual pixels, we trained a CNN model using a subset of historical imagery for which we manually mapped land cover. The process of training is called supervised learning. This is because during training, the CNN model learns the pixel properties of input imagery that relate to the user-provided categories (e.g. manually mapped land cover associated with an input aerial photograph). Following training, our CNN model provides accurate and scalable classification of wooded areas, grass, built up land, bare ground and water bodies in input aerial photography. We then deployed our CNN model to map the whole city of Toronto from a <a href="https://schoolofcities.github.io/historical-aerial-imagery-toronto/" target="_blank">series of aerial photographs</a> spanning decades in a relatively short time, allowing us to quantify citywide change over time.
		</p>
	</div>

	<SankeyChart/>

	<div class="text">
		<p>
			We found that since 1939, Toronto development has gone through two distinct phases, from expansion (1950-1973) to infill (1970-1990). During the postwar period, agricultural land was rapidly converted into largely residential developments, reaching an average rate of <b>~1800 ha/yr between 1954 and 1973</b>. During this time neighbourhoods including Don Mills and Flemingdon Park were developed, and there was significant residential expansion in Etobicoke and North York. In comparison, the infill period saw the development of small but important remnant spaces within the city matrix, resulting in the loss of many nearby green spaces. 

		</p>
		<p>
			Importantly, we found that the total amount of forest at the city scale has been relatively stable since the pre-war period, despite a continuous turnover. This means that while Toronto as a whole has maintained approximately the same total forest area, individual tracts of forest have been lost in certain neighbourhoods while others regenerated elsewhere. Thus, Toronto neighbourhoods vary widely in how much forest area they have lost or gained in the past decades.
		</p>
	</div>

	<MapNeighbourhood/>

	<div class="text">
		<p>
			Of course, the population of Toronto has also changed during this time. Total population has risen from approximately 1.1 M to 2.8M and median household income has gone <a href="https://www150.statcan.gc.ca/t1/tbl1/en/tv.action?pid=1110023901&pickMembers%5B0%5D=1.17&pickMembers%5B1%5D=2.1&pickMembers%5B2%5D=3.1&pickMembers%5B3%5D=4.1&cubeTimeFrame.startYear=1976&cubeTimeFrame.endYear=2021&referencePeriods=19760101%2C20210101" target="blank">from 38k to 39k annually  between 1976 and 2021</a> (in constant dollars). Because population growth has occurred proportionally across neighbourhoods, there has not been marked changes in the distribution of nature between different parts of the city, or among residents. 
		</p>
		<p>
			The following chart shows small changes in the inequality of forest distribution between different areas in the city and between residents, as measured by the Gini coefficient of forest access inequality (calculated based on forest cover in a 1km radius). Despite turnover in forest cover from development (forest tract disappearance) and regeneration and restoration (forest increase), changes have not led to a marked change in the distribution of forest cover between places or people.
		</p>
	</div>

	<GiniChart/>

	<div class="text">
		<p>
			On the other hand, the distribution of forest cover relative to resident’s income has changed in important ways. In 1970, one could not have predicted a neighbourhood median income by simply looking at how much forest cover there was, but this appears to no longer be true. Preliminary results show that the relationship between forest cover and income is becoming stronger, with more affluent neighbourhoods increasingly predictably having more forest cover. The following chart shows a change in the relationship between median income (not adjusted for inflation) and forest cover within a 1 km radius.
		</p>
	</div>

	<OneKmChart/>

	<div class="text">
		<p>
			Urban forest restoration and tree planting have become a tool of choice for increasing environmental equity in cities. Because forest amount in Toronto has remained relatively stable, changes in the relationship between forest cover and income are more likely due to other processes. For instance, because access to nature is desirable to wealthier households, upper-income homeowners and renters are willing to pay a premium for access to this amenity. At the same time, lower income immigrant settlement communities may tend to be located in areas with less access to forest areas. Depending on which mechanism is operating, planting more trees may contribute little to environmental equity if it is not also accompanied by mechanisms to increase affordability and improve transit. 
		</p>
		<p>
			As we attempt to chart just futures for increasingly warm and dense cities, it is critical that we understand how people and nature have interacted in the past. By combining historical data on access to nature and its relationship with population and wealth, we can make educated predictions on how access to nature might change in the city as a result of expected demographic and economic change. Agglomerated cities like Toronto also offer diverse management histories that act like natural experiments that we can learn from by looking at their outcomes today. Finally, once trained, analytical tools like convolutional neural networks can be used to classify aerial imagery for entire regions, like the Greater Toronto Area, or for other cities. 
		</p>
	</div>

	<div class="text">
		<br>

		<h2>More Information</h2>

		<p>
			The code to create this page and it's encompassing maps and charts are on <a href="https://github.com/schoolofcities/historical-land-cover-toronto" target="blank">GitHub</a>. 
			Some of the source aerial imagery used in the machine learning models can be viewed on <a href="https://schoolofcities.github.io/historical-aerial-imagery-toronto/" target="blank">this interactive website</a>.
		</p>

		<br><br><br>
	</div>



</main>
