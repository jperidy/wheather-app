<script>

	import { onMount } from 'svelte';
	import dateformat from 'dateformat';
	import List from './List.svelte';
	import Search from './Search.svelte';
	import { Container, Row } from 'sveltestrap';

	const baseURL = `https://api.openweathermap.org/data/2.5/forecast`;
	const API_KEY = `1f117e94254efca9fabbfc91b101cf2e`;

	let city = 'paris';
	let cityFound = 'paris'
	let items = [];

	onMount(() => {
		getWeather(city);
	});

	const transformWeatherData = (weather) => {
		return weather.list.map((item, index) => {
			const data = {};
			const date = new Date(item.dt * 1000);
			const iconUrl = `http://openweathermap.org/img/w/${item.weather[0].icon}.png`;

			data.date = dateformat(date, "mmm dS, yyyy, h TT");
			data.id = index;
			data.iconUrl = iconUrl;
			data.desc = item.weather[0].description;
			data.temp = item.main.temp;
			data.min = item.main.temp_min;
			data.max = item.main.temp_max;

			return data;
		});
	};

	const handleCall = (event) => {
		//debugger;
		getWeather(event.detail.city);
	};
	

	const getWeather = async (city) => {

		const params = {
			q: city,
			units: 'metric',
		};

		const queryString = new URLSearchParams({
			...params,
			appid: API_KEY,
		});
		const url = `${baseURL}?${queryString}`; 

		if(city?.length) {
			try {
				const res = await fetch(url);

				if (res.ok) {
					try {
						const weather = await res.json(); 
						items = transformWeatherData(weather);
						cityFound = city;
						// console.log(items);
					} catch (error) {
						console.log(error);
					}
				}
			} catch (error) {
				console.log(error);
			}
		}	
	}
</script>

<Container>
	<Row class="mt-3">
		<Search bind:city on:message={handleCall}/>
	</Row>
	<Row class="mt-3">
		<h1>{cityFound}</h1>
	</Row>
	<Row class="mt-3">
		<List {items} />
	</Row>
</Container>
