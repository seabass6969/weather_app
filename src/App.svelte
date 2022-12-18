<script>
	import {onMount} from 'svelte'
	import Graph from './lib/Graph.svelte';
	
	let support_or_not = false 
	let loading = false
	let Latitude
	let Longitude
	let weather_info
	
	function getLocation() {
		if (navigator.geolocation && !(localStorage.getItem("had_address") == "true")) {
			support_or_not = true
			loading = true
			navigator.geolocation.getCurrentPosition(showPosition);
		} else if (localStorage.getItem("had_address") == "true") {
			Latitude = localStorage.getItem("Latitude")
			Longitude = localStorage.getItem("Longitude")
			support_or_not = true 
		} else {
			support_or_not = false
		}
	}
	
    
	async function getWeather(){
        const timezone = "GMT"
        let url = `https://api.open-meteo.com/v1/forecast?latitude=`+Latitude+`&longitude=`+Longitude+`&hourly=temperature_2m,relativehumidity_2m,precipitation,rain,showers,snowfall,snow_depth,freezinglevel_height,pressure_msl,surface_pressure,cloudcover,cloudcover_low,cloudcover_mid,cloudcover_high,visibility,windspeed_10m,windspeed_80m,winddirection_10m,winddirection_80m,windgusts_10m,temperature_80m,soil_temperature_0cm,soil_temperature_6cm,soil_moisture_0_1cm,soil_moisture_1_3cm&daily=temperature_2m_max,temperature_2m_min,sunrise,sunset&current_weather=true&windspeed_unit=mph&timeformat=unixtime&timezone=`+ timezone
		let out = await fetch(url)
		return out.json()
	}
	
	function showPosition(position) {
		console.log("some data")
		loading = false
		Latitude = position.coords.latitude
		Longitude = position.coords.longitude
		localStorage.setItem("had_address", "true")
		localStorage.setItem("Latitude", Latitude)
		localStorage.setItem("Longitude", Longitude)
	}
	onMount(()=> {
		console.clear()
		getLocation()
	})
</script>

<main>
	<div class="card">
		<h1>The weather is:</h1>
	</div>
	{#if support_or_not == false}
	<h1>Not supported or press the button thats say allow</h1>
	{:else if support_or_not == true}
	{#if loading == true}
	<h1>loading...</h1>
	{:else}
	{#await getWeather()}
	<h1>loading...</h1>
	{:then wh} 
	<div class="card">
		<Graph data={wh} data-showing="temperature_2m" data_title="temperature ℃"/>
	</div>
	<div class="card">
		<Graph data={wh} data_showing="relativehumidity_2m" data_title="Humidity %" />
	</div>
	<div class="card">
		<span>Your elevation: {wh["elevation"]}</span>
	</div>
	{/await}
	{/if}
	{/if}
</main>
<style lang="scss">
	.card{
        @include glassmorphism;
		margin: 5px;
		padding: 10px;
		width: 900px;
		color: $text-color;
	}
</style>
