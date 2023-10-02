<script setup>
import {computed, ref} from "vue";
import axios from 'axios';
import WeatherDetails from '../components/WeatherDetails.vue';
import PlacesNearLocation from '../components/PlacesNearLocation.vue';

const country = ref('');
const cityDetails = ref('');
const searchCountry = ref('');
const searchCity = ref('');
const cities = ref();
const countries = [
  "Afghanistan",
  "Albania",
  "Algeria",
  "Andorra",
  "Angola",
  "Antigua and Barbuda",
  "Argentina",
  "Armenia",
  "Australia",
  "Austria",
  "Azerbaijan",
  "Bahamas",
  "Bahrain",
  "Bangladesh",
  "Barbados",
  "Belarus",
  "Belgium",
  "Belize",
  "Benin",
  "Bhutan",
  "Bolivia",
  "Bosnia and Herzegovina",
  "Botswana",
  "Brazil",
  "Brunei",
  "Bulgaria",
  "Burkina Faso",
  "Burundi",
  "Cabo Verde",
  "Cambodia",
  "Cameroon",
  "Canada",
  "Central African Republic",
  "Chad",
  "Chile",
  "China",
  "Colombia",
  "Comoros",
  "Congo (Brazzaville)",
  "Congo (Kinshasa)",
  "Costa Rica",
  "Croatia",
  "Cuba",
  "Cyprus",
  "Czech Republic",
  "Denmark",
  "Djibouti",
  "Dominica",
  "Dominican Republic",
  "East Timor",
  "Ecuador",
  "Egypt",
  "El Salvador",
  "Equatorial Guinea",
  "Eritrea",
  "Estonia",
  "Eswatini",
  "Ethiopia",
  "Fiji",
  "Finland",
  "France",
  "Gabon",
  "Gambia",
  "Georgia",
  "Germany",
  "Ghana",
  "Greece",
  "Grenada",
  "Guatemala",
  "Guinea",
  "Guinea-Bissau",
  "Guyana",
  "Haiti",
  "Honduras",
  "Hungary",
  "Iceland",
  "India",
  "Indonesia",
  "Iran",
  "Iraq",
  "Ireland",
  "Israel",
  "Italy",
  "Ivory Coast",
  "Jamaica",
  "Japan",
  "Jordan",
  "Kazakhstan",
  "Kenya",
  "Kiribati",
  "Korea, North",
  "Korea, South",
  "Kosovo",
  "Kuwait",
  "Kyrgyzstan",
  "Laos",
  "Latvia",
  "Lebanon",
  "Lesotho",
  "Liberia",
  "Libya",
  "Liechtenstein",
  "Lithuania",
  "Luxembourg",
  "Macedonia",
  "Madagascar",
  "Malawi",
  "Malaysia",
  "Maldives",
  "Mali",
  "Malta",
  "Marshall Islands",
  "Mauritania",
  "Mauritius",
  "Mexico",
  "Micronesia",
  "Moldova",
  "Monaco",
  "Mongolia",
  "Montenegro",
  "Morocco",
  "Mozambique",
  "Myanmar",
  "Namibia",
  "Nauru",
  "Nepal",
  "Netherlands",
  "New Zealand",
  "Nicaragua",
  "Niger",
  "Nigeria",
  "North Macedonia",
  "Norway",
  "Oman",
  "Pakistan",
  "Palau",
  "Palestinian Territories",
  "Panama",
  "Papua New Guinea",
  "Paraguay",
  "Peru",
  "Philippines",
  "Poland",
  "Portugal",
  "Qatar",
  "Romania",
  "Russia",
  "Rwanda",
  "Saint Kitts and Nevis",
  "Saint Lucia",
  "Saint Vincent and the Grenadines",
  "Samoa",
  "San Marino",
  "Sao Tome and Principe",
  "Saudi Arabia",
  "Senegal",
  "Serbia",
  "Seychelles",
  "Sierra Leone",
  "Singapore",
  "Slovakia",
  "Slovenia",
  "Solomon Islands",
  "Somalia",
  "South Africa",
  "South Sudan",
  "Spain",
  "Sri Lanka",
  "Sudan",
  "Suriname",
  "Sweden",
  "Switzerland",
  "Syria",
  "Taiwan",
  "Tajikistan",
  "Tanzania",
  "Thailand",
  "Timor-Leste",
  "Togo",
  "Tonga",
  "Trinidad and Tobago",
  "Tunisia",
  "Turkey",
  "Turkmenistan",
  "Tuvalu",
  "Uganda",
  "Ukraine",
  "United Arab Emirates",
  "United Kingdom",
  "United States",
  "Uruguay",
  "Uzbekistan",
  "Vanuatu",
  "Vatican City",
  "Venezuela",
  "Vietnam",
  "Yemen",
  "Zambia",
  "Zimbabwe",
];
const showListCountry = ref(false);
const urlCountry = "https://wft-geo-db.p.rapidapi.com/v1/geo/countries?namePrefix=";
const weatherDetails = ref('');
const placesNearLocation = ref('');
const isDropDownOpen = ref(false);
const toggledWeatherOrPlaceNear = ref(true);
const offset = ref(0);
const countryId = ref(0);
const loadingIconForCity = ref(false);
const loadingIconForDetailsAndPlaces = ref(false);

const filteredCountries = computed(() => {
  return countries.filter((country) => !searchCountry.value || country.toLowerCase().includes(searchCountry.value.toLowerCase()));
});

function selectCountry(country) {
  searchCountry.value = country;
  clearValues();

  getCountry(urlCountry + country);
}

function selectCity(city) {
  loadingIconForDetailsAndPlaces.value = true;
  searchCity.value = city.name;
  cityDetails.value = city;

  toggledDropDown();

  setTimeout(() => {
    getWeatherDetails(city.latitude.toFixed(2), city.longitude.toFixed(2));
    getPlacesNearLocation(city);

    loadingIconForDetailsAndPlaces.value = false;
  }, 2000);
}

async function getCountry(url) {
  const options = {
    method: 'GET',
    url: url,
    params: {
      limit: '10',
    },
    headers: {
      'X-RapidAPI-Key': 'b7acb9ad46msha339e369a978e13p19d9f0jsn19e09ecd0c54',
      'X-RapidAPI-Host': 'wft-geo-db.p.rapidapi.com'
    }
  };

  try {
    loadingIconForCity.value = true;
    await axios.request(options)
        .then(async (res) => {
          country.value = res.data.data[0];
          return new Promise((resolve) => {
            setTimeout(async () => {
              try {
                country.value = res.data.data[0];
                countryId.value = country.value.wikiDataId;

                await getCities();

                loadingIconForCity.value = false;
              } catch (error) {
                console.error("Request failed:", error);
                resolve(null);
              }
            }, 1500);
          });
        })
        .catch(error => {
          console.log("The error is : ", error);
        });
    loadingIconForCity.value = false;
  } catch (error) {
    console.error(error);
  }

}

async function getCities() {
  const options = {
    method: 'GET',
    url: "https://wft-geo-db.p.rapidapi.com/v1/geo/cities?countryIds=" + countryId.value,
    params: {
      limit: '10',
      offset: offset.value
    },
    headers: {
      'X-RapidAPI-Key': 'b7acb9ad46msha339e369a978e13p19d9f0jsn19e09ecd0c54',
      'X-RapidAPI-Host': 'wft-geo-db.p.rapidapi.com'
    }
  };

  try {
    await axios.request(options)
        .then(async (res) => {
          cities.value = res.data.data;
        })
        .catch(error => {
          console.log("The error is : ", error);
        });
  } catch (error) {
    console.error(error);
  }
}

async function getWeatherDetails(lat, lon) {
  const options = {
    method: 'GET',
    url: 'https://weatherapi-com.p.rapidapi.com/current.json',
    params: {q: `${lat}, ${lon}`},
    headers: {
      'X-RapidAPI-Key': 'b7acb9ad46msha339e369a978e13p19d9f0jsn19e09ecd0c54',
      'X-RapidAPI-Host': 'weatherapi-com.p.rapidapi.com'
    }
  };

  try {
    weatherDetails.value = await axios.request(options);
  } catch (error) {
    console.error(error);
  }
}

async function getPlacesNearLocation(city) {
  const options = {
    method: 'GET',
    url: `https://wft-geo-db.p.rapidapi.com/v1/geo/cities/${city.wikiDataId}/nearbyCities`,
    params: {radius: '100'},
    headers: {
      'X-RapidAPI-Key': 'b7acb9ad46msha339e369a978e13p19d9f0jsn19e09ecd0c54',
      'X-RapidAPI-Host': 'wft-geo-db.p.rapidapi.com'
    }
  };

  try {
    placesNearLocation.value = await axios.request(options);
  } catch (error) {
    console.error(error);
  }
}

function toggledDropDown() {
  isDropDownOpen.value = !isDropDownOpen.value;
}

function toggledWeatherOrPlaceNearLocation(value) {
  toggledWeatherOrPlaceNear.value = value;
}

function clearValues() {
  showListCountry.value = false;
  cityDetails.value = '';
  isDropDownOpen.value = false;
  weatherDetails.value = '';
  cities.value = '';
}

function getMoreCities() {
  offset.value += 10;
  getCities();
}
</script>

<template>
  <title>Weather App</title>

  <div class="h-screen flex items-center justify-center bg-gray-400">
    <div
        class="max-w-sm w-1/3 mx-auto p-8 bg-white rounded-xl shadow-2xl h-full"
    >
      <h3 class="text-center text-2xl mb-2 bg-green-400 rounded-full p-2 text-white">Weather App</h3>
      <img
          class="h-24 mx-auto rounded-full ring-4 ring-green-500"
          src="../assets/weather.svg"
          alt="Weather"
      />

      <div class="text-center mt-8">
      </div>

      <div>
        <div class="relative">
          <input
              type="text"
              id="input-group-search"
              class="block w-full p-2 pl-10 text-sm text-gray-900 border border-gray-300 rounded-lg bg-gray-50 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-600 dark:border-gray-500 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
              placeholder="Search Country"
              v-model="searchCountry"
              @focus="showListCountry = true"
          />
          <div v-show="searchCountry.length > 0 && showListCountry"
               class="absolute left-0 mt-2 w-60 bg-white rounded-lg">
            <ul class="px-3 pb-3 overflow-y-auto text-sm text-gray-700 dark:text-gray-200"
                aria-labelledby="dropdownSearchButton">
              <li v-for="country in filteredCountries" :key="country">
                <div class="flex items-center pl-2 rounded hover:bg-gray-100">
                  <input
                      @click="selectCountry(country)"
                      id="checkbox-item-11"
                      readonly
                      :value="country"
                      class="w-full py-2 ml-2 text-sm font-medium text-black rounded dark:text-gray-300 cursor-pointer"
                  />
                </div>
              </li>
            </ul>
          </div>
        </div>
      </div>

      <div v-if="loadingIconForCity">
        <div class="relative mt-10">
          <div>
            <button type="button"
                    class="w-full gap-x-1.5 rounded-md bg-white px-3 py-2 text-sm font-semibold text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 hover:bg-gray-50"
                    id="menu-button" aria-expanded="true" aria-haspopup="true">

              <div role="status" class="flex">
                <svg aria-hidden="true" class="w-5 h-5 mr-2 text-gray-200 animate-spin dark:text-gray-600 fill-blue-600"
                     viewBox="0 0 100 101" fill="none" xmlns="http://www.w3.org/2000/svg">
                  <path
                      d="M100 50.5908C100 78.2051 77.6142 100.591 50 100.591C22.3858 100.591 0 78.2051 0 50.5908C0 22.9766 22.3858 0.59082 50 0.59082C77.6142 0.59082 100 22.9766 100 50.5908ZM9.08144 50.5908C9.08144 73.1895 27.4013 91.5094 50 91.5094C72.5987 91.5094 90.9186 73.1895 90.9186 50.5908C90.9186 27.9921 72.5987 9.67226 50 9.67226C27.4013 9.67226 9.08144 27.9921 9.08144 50.5908Z"
                      fill="currentColor"/>
                  <path
                      d="M93.9676 39.0409C96.393 38.4038 97.8624 35.9116 97.0079 33.5539C95.2932 28.8227 92.871 24.3692 89.8167 20.348C85.8452 15.1192 80.8826 10.7238 75.2124 7.41289C69.5422 4.10194 63.2754 1.94025 56.7698 1.05124C51.7666 0.367541 46.6976 0.446843 41.7345 1.27873C39.2613 1.69328 37.813 4.19778 38.4501 6.62326C39.0873 9.04874 41.5694 10.4717 44.0505 10.1071C47.8511 9.54855 51.7191 9.52689 55.5402 10.0491C60.8642 10.7766 65.9928 12.5457 70.6331 15.2552C75.2735 17.9648 79.3347 21.5619 82.5849 25.841C84.9175 28.9121 86.7997 32.2913 88.1811 35.8758C89.083 38.2158 91.5421 39.6781 93.9676 39.0409Z"
                      fill="currentFill"/>
                </svg>
                Loading...
              </div>

            </button>
          </div>
        </div>
      </div>

      <div v-if="cities">
        <div class="relative mt-10">
          <div>
            <button @click="toggledDropDown" type="button"
                    class="inline-flex w-full p-2 pl-10 gap-x-1.5 rounded-md bg-white px-3 py-2 text-sm text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 hover:bg-gray-50"
                    id="menu-button" aria-expanded="true" aria-haspopup="true">
              {{ cityDetails ? cityDetails.name : "Select City" }}
              <svg class="-mr-1 h-5 w-5 text-gray-400" viewBox="0 0 20 20" fill="currentColor" aria-hidden="true">
                <path fill-rule="evenodd"
                      d="M5.23 7.21a.75.75 0 011.06.02L10 11.168l3.71-3.938a.75.75 0 111.08 1.04l-4.25 4.5a.75.75 0 01-1.08 0l-4.25-4.5a.75.75 0 01.02-1.06z"
                      clip-rule="evenodd"/>
              </svg>
            </button>
          </div>

          <div v-if="isDropDownOpen"
               class="mt-2 origin-top-right rounded-md bg-white shadow-lg ring-1 ring-black ring-opacity-5 focus:outline-none"
               role="menu" aria-orientation="vertical" aria-labelledby="menu-button">
            <div class="py-1" role="none">
              <li v-for="city in cities" :key="city" @click="selectCity(city)"
                  class="text-gray-700 block px-4 py-2 text-sm cursor-pointer hover:bg-sky-100 rounded-2xl"
                  role="menuitem" id="menu-item-0">
                {{ city.name }}
              </li>
              <li @click="getMoreCities"
                  class="text-blue-700 block bg-sky-100 px-4 py-2 text-sm cursor-pointer hover:bg-sky-200 rounded-2xl"
                  role="menuitem" id="menu-item-0">
                More
              </li>

            </div>
          </div>
        </div>
      </div>

      <div v-if="loadingIconForDetailsAndPlaces">
        <div role="status" class="h-96 flex items-center justify-center">
          <svg aria-hidden="true" class="w-5 h-5 mr-2 text-gray-200 animate-spin dark:text-gray-600 fill-blue-600"
               viewBox="0 0 100 101" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path
                d="M100 50.5908C100 78.2051 77.6142 100.591 50 100.591C22.3858 100.591 0 78.2051 0 50.5908C0 22.9766 22.3858 0.59082 50 0.59082C77.6142 0.59082 100 22.9766 100 50.5908ZM9.08144 50.5908C9.08144 73.1895 27.4013 91.5094 50 91.5094C72.5987 91.5094 90.9186 73.1895 90.9186 50.5908C90.9186 27.9921 72.5987 9.67226 50 9.67226C27.4013 9.67226 9.08144 27.9921 9.08144 50.5908Z"
                fill="currentColor"/>
            <path
                d="M93.9676 39.0409C96.393 38.4038 97.8624 35.9116 97.0079 33.5539C95.2932 28.8227 92.871 24.3692 89.8167 20.348C85.8452 15.1192 80.8826 10.7238 75.2124 7.41289C69.5422 4.10194 63.2754 1.94025 56.7698 1.05124C51.7666 0.367541 46.6976 0.446843 41.7345 1.27873C39.2613 1.69328 37.813 4.19778 38.4501 6.62326C39.0873 9.04874 41.5694 10.4717 44.0505 10.1071C47.8511 9.54855 51.7191 9.52689 55.5402 10.0491C60.8642 10.7766 65.9928 12.5457 70.6331 15.2552C75.2735 17.9648 79.3347 21.5619 82.5849 25.841C84.9175 28.9121 86.7997 32.2913 88.1811 35.8758C89.083 38.2158 91.5421 39.6781 93.9676 39.0409Z"
                fill="currentFill"/>
          </svg>
          Loading...
        </div>
      </div>

      <div v-if="weatherDetails" class="relative mt-4">
        <div class="flex items-center justify-between">
          <button @click="toggledWeatherOrPlaceNearLocation(true)"
                  class="btn-green">
            Weather Details
          </button>
          <button @click="toggledWeatherOrPlaceNearLocation(false)"
                  class="btn-green">
            Places Near
          </button>
        </div>

        <div v-if="toggledWeatherOrPlaceNear">
          <WeatherDetails
              :weatherDetails="weatherDetails"
          />
        </div>
        <div v-if="!toggledWeatherOrPlaceNear">
          <PlacesNearLocation
              :placesNearLocation="placesNearLocation"
          />
        </div>

      </div>
    </div>
  </div>
</template>
