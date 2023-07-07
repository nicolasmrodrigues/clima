<script setup>
import { onMounted, reactive } from 'vue';
import Loader from './components/Loader.vue';
import Form from './components/Form.vue';
import WeatherCard from './components/WeatherCard.vue';
import Graph from './components/Graph.vue';
import Footer from './components/Footer.vue';
import Chart from 'chart.js/auto';
import ChartDataLabels from 'chartjs-plugin-datalabels';

const iconBaseUrl = 'https://cdn.weatherapi.com/weather/128x128/';

const DaysOfTheWeek = ['Dom', 'Seg', 'Terç', 'Qua', 'Qui', 'Sex', 'Sáb'];
const months = [
	'Jan',
	'Fev',
	'Mar',
	'Abr',
	'Maio',
	'Jun',
	'Jul',
	'Ago',
	'Set',
	'Out',
	'Nov',
	'Dez'
];

const info = reactive({
	city: '',
	requestedCity: '',
	forecastMaxAndMinTemperatures: [
		{
			max: 0,
			min: 0
		},
		{
			max: 0,
			min: 0
		},
		{
			max: 0,
			min: 0
		}
	],
	currentHumidity: 0,
	currentTemperature: 0,
	currentRealFeel: 0,
	iconUrl: '',
	isReadyToShowUp: false,
	localDate: '',
	localDateFormatted: '',
	weatherDescription: '',
	windSpeed: 0,
	autocomplete: []
});

const createsLocalDateString = () => {
	let localDate = new Date(info.localDate);
	let localDateYear = localDate.getFullYear();
	let localDateMonth = months[localDate.getMonth()];
	let localDateDate = localDate.getDate();
	let localDateDay = DaysOfTheWeek[localDate.getDay()];
	let localDateHours = localDate.getHours();
	let localDateMinutes = localDate.getMinutes();

	if (localDateMinutes < 10) {
		localDateMinutes = `0${localDateMinutes}`;
	}

	if (localDateHours === 0) {
		localDateHours = 12;
		info.localDateFormatted += `${localDateHours}:${localDateMinutes} AM`;
	} else if (localDateHours < 12) {
		info.localDateFormatted += `${localDateHours}:${localDateMinutes} AM`;
	} else if (localDateHours === 12) {
		info.localDateFormatted += `${localDateHours}:${localDateMinutes} PM`;
	} else if (localDateHours > 12) {
		info.localDateFormatted += `${localDateHours - 12}:${localDateMinutes} PM`;
	}

	info.localDateFormatted += `, ${localDateDay}, ${localDateDate} ${localDateMonth}, ${localDateYear}`;
};

const getWeatherInfo = (data) => {
	info.weatherDescription = data.current.condition.text;
	info.localDate = data.location.localtime;
	info.currentRealFeel = Math.round(data.current.feelslike_c);
	info.currentTemperature = Math.round(data.current.temp_c);
	info.windSpeed = Math.round(data.current.wind_kph);
	info.currentHumidity = data.current.humidity;
	info.iconUrl = `${iconBaseUrl}${data.current.condition.icon.slice(
		35,
		data.current.condition.icon.length
	)}`;
};

const getForecastInfo = (data) => {
	let forecast = [...data.forecast.forecastday];

	for (let i = 0; i < forecast.length; i++) {
		info.forecastMaxAndMinTemperatures[i].max = Math.round(
			forecast[i].day.maxtemp_c
		);
		info.forecastMaxAndMinTemperatures[i].min = Math.round(
			forecast[i].day.mintemp_c
		);
	}
};

const getMaxAndMinTemperatures = () => {
	let maxTemperatures = [];
	let minTemperatures = [];

	for (let i = 0; i < info.forecastMaxAndMinTemperatures.length; i++) {
		maxTemperatures.push(info.forecastMaxAndMinTemperatures[i].max);
		minTemperatures.push(info.forecastMaxAndMinTemperatures[i].min);
	}
	return [maxTemperatures, minTemperatures];
};

const getNextDaysOfTheWeek = () => {
	const date = new Date(info.localDate);
	let addToIndex = 0;
	const dayToday = DaysOfTheWeek[date.getDay()];
	let indexOfDayToday = DaysOfTheWeek.indexOf(dayToday);
	let nextDays = [];

	for (let i = 0; i < 3; i++) {
		if (indexOfDayToday + addToIndex === 7) {
			indexOfDayToday = 0;
			addToIndex = 0;
		}
		nextDays.push(`${DaysOfTheWeek[indexOfDayToday + addToIndex]}.`);
		addToIndex += 1;
	}

	return nextDays;
};

const drawGraph = () => {
	const canvasContainer = document.getElementById('canvas-container');
	canvasContainer.removeChild(canvasContainer.children[0]);

	const canvas = document.createElement('canvas');
	canvas.id = 'canvas';
	canvasContainer.appendChild(canvas);

	const graph = document.getElementById('canvas');

	const maxAndMinTemperatures = getMaxAndMinTemperatures();

	const xValues = getNextDaysOfTheWeek();
	const yMaxTemperatures = maxAndMinTemperatures[0];
	const yMinTemperatures = maxAndMinTemperatures[1];

	Chart.register(ChartDataLabels);
	Chart.defaults.set('plugins.datalabels', {
		color: '#000',
		anchor: 'end',
		align: 'bottom'
	});
	Chart.defaults.font.size = 16;

	new Chart(graph, {
		type: 'bar',
		data: {
			labels: xValues,
			datasets: [
				{
					label: 'Máxima',
					data: yMaxTemperatures,
					backgroundColor: '#3AC8E1',
					borderColor: '#000',
					borderWidth: 1.5
				},
				{
					label: 'Mínima',
					data: yMinTemperatures,
					backgroundColor: '#9ECAE1',
					borderColor: '#000',
					borderWidth: 1.5
				}
			]
		},
		options: {
			animation: {
				duration: 0
			},
			plugins: {
				title: {
					display: true,
					text: 'Temperatura máximas e mínimas',
					font: {
						size: 28,
						weight: 'normal'
					}
				}
			},
			scales: {
				y: {
					beginAtZero: true
				}
			},
			responsive: true,
			maintainAspectRatio: false
		}
	});
};

const GetWeatherAndForecast = () => {
	info.isReadyToShowUp = false;
	info.localDateFormatted = '';

	let weatherAndForecastUrl = `https://clima-backend.vercel.app/weather?city=${info.city}&days=3`;

	fetch(weatherAndForecastUrl).then((response) => {
		response.json().then((jsonData) => {
			getWeatherInfo(jsonData);
			getForecastInfo(jsonData);
			createsLocalDateString();
			drawGraph();
			info.isReadyToShowUp = true;
		});
	});
};

const getCurrentLocationInfo = () => {
	const geolocationUrl = 'https://clima-backend.vercel.app/weather';
	let cityInput = document.getElementById('city-input');

	fetch(geolocationUrl).then((response) => {
		response.json().then((jsonData) => {
			info.city = info.requestedCity = jsonData.location.name;

			if (sessionStorage.getItem('requestedCity')) {
				info.city = info.requestedCity =
					sessionStorage.getItem('requestedCity');
			}

			GetWeatherAndForecast();
			cityInput.value = jsonData.location.name;
			if (sessionStorage.getItem('requestedCity')) {
				cityInput.value = sessionStorage.getItem('requestedCity');
			}
		});
	});
};

window.addEventListener('load', () => {
	getCurrentLocationInfo();
});

onMounted(() => {
	const cityInput = document.getElementById('city-input');
	const cities = document.getElementsByClassName('city');

	for (let i = 0; i < cities.length; i++) {
		cities[i].addEventListener('click', (e) => {
			info.autocomplete = [];
			info.city = cityInput.value = e.target.value.slice(
				0,
				e.target.value.indexOf(',')
			);
			updatesRequestedCity();
		});
	}
});

const changesCity = (e) => {
	info.city = e.target.value.charAt(0).toUpperCase() + e.target.value.slice(1);
	info.city = info.city.replace('ç', 'c');
	info.city = info.city.replace(' ', '%20');

	const url = `https://clima-backend.vercel.app/search?city=${info.city}&days=1`;

	fetch(url).then((response) => {
		response.json().then((jsonData) => {
			info.autocomplete = [];
			for (let i = 0; i < jsonData.length; i++) {
				info.autocomplete.push(`${jsonData[i].name}, ${jsonData[i].country}`);
			}
		});
	});
};

const updatesRequestedCity = () => {
	info.requestedCity = info.city;
	sessionStorage.setItem('requestedCity', info.requestedCity);
};
</script>

<template>
	<div class="dashboard-container container rounded-3">
		<Form
			:submit="GetWeatherAndForecast"
			:input-change="changesCity"
			:button-click="updatesRequestedCity"
			:cities="info.autocomplete"
		/>
		<div v-show="!info.isReadyToShowUp" class="loader-container">
			<Loader />
		</div>
		<div class="forecast-container row" v-show="info.isReadyToShowUp">
			<WeatherCard :info="info" />
			<Graph />
		</div>
	</div>
	<Footer />
</template>

<style scoped lang="scss">
.dashboard-container {
	background-color: #fff;
	height: 80dvh;
	max-width: 90dvw;
	overflow: hidden;
	box-shadow: rgba(149, 157, 165, 0.2) 0px 8px 24px;
	padding: 48px 24px 24px 24px;
	margin-top: 48px;

	@media (max-width: 991px) or (max-height: 550px) {
		padding: 32px 12px 24px 12px;
		margin-top: 24px;
	}

	@media (orientation: portrait) and (min-width: 768px) {
		height: 85dvh;
	}

	@media (max-height: 600px) {
		padding: 24px 24px 24px 24px;
	}

	@media (max-width: 767px) {
		width: 90dvw;
	}
}

.forecast-container {
	height: 93%;

	@media (orientation: portrait) and (min-height: 992px) {
		display: block;
	}
}

.loader-container {
	display: flex;
	height: 80%;
	align-items: center;
	justify-content: center;
}
</style>
