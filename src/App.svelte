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
		let out = await fetch("https://api.open-meteo.com/v1/forecast?latitude="+Latitude+"&longitude="+Longitude+"&hourly=temperature_2m&windspeed_unit=mph&timeformat=unixtime")
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
		<Graph data={wh}/>
	</div>
	<div class="card">
		<h1>Your elevation: {wh["elevation"]}</h1>
	</div>
	{/await}
	{/if}
	{/if}
</main>
<style>
	.card{
		background: rgba( 231, 153, 153, 0.25 );
		box-shadow: 0 8px 32px 0 rgba( 31, 38, 135, 0.37 );
		backdrop-filter: blur( 4px );
		-webkit-backdrop-filter: blur( 4px );
		border-radius: 10px;
		border: 1px solid rgba( 255, 255, 255, 0.18 );
		margin: 10px;
		padding: 10px;
		width: 900px;
		color:white
	}
</style>
