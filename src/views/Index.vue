<script setup>
import {computed, reactive, ref} from "vue";
import axios from 'axios';

const country = ref('');
const city = ref('');
const searchCountry = ref('');
const searchCity = ref('');
const cities = reactive([]);
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
const showListCity = ref(false);
const urlCountry = "https://wft-geo-db.p.rapidapi.com/v1/geo/countries?namePrefix=";
const urlCity = "https://wft-geo-db.p.rapidapi.com/v1/geo/cities?countryIds=";

const filteredCountries = computed(() => {
  return countries.filter((country) => !searchCountry.value || country.toLowerCase().includes(searchCountry.value.toLowerCase()));
});

const filteredCities = computed(() => {
  return cities[0].filter((city) => !searchCity.value || city.name.toLowerCase().includes(searchCity.value.toLowerCase()));
});

function selectCountry(country) {
  searchCountry.value = country;
  showListCountry.value = false;

  getCountry(urlCountry+country);
}

function selectCity(city) {
  searchCity.value = city.name;
  showListCity.value = false;

  getWeatherDetails(city.latitude.toFixed(2), city.longitude.toFixed(2));
}

async function getCountry(url) {
  const options = {
    method: 'GET',
    url: url,
    headers: {
      'X-RapidAPI-Key': 'b7acb9ad46msha339e369a978e13p19d9f0jsn19e09ecd0c54',
      'X-RapidAPI-Host': 'wft-geo-db.p.rapidapi.com'
    }
  };

  try {
    await axios.request(options)
        .then(async (res) => {
          country.value = res.data.data[0];
          return new Promise((resolve) => {
            setTimeout(async () => {
              try {
                country.value = res.data.data[0];
                await getCities(urlCity+country.value.wikiDataId);
              } catch (error) {
                console.error("Request failed:", error);
                resolve(null);
              }
            }, 1500);
          });
        })
        .catch(error =>{
          console.log("The error is : ", error);
        });
  } catch (error) {
    console.error(error);
  }

}

async function getCities(url) {
  const options = {
    method: 'GET',
    url: url,
    params: {
      limit: '10',
      offset: '10'
    },
    headers: {
      'X-RapidAPI-Key': 'b7acb9ad46msha339e369a978e13p19d9f0jsn19e09ecd0c54',
      'X-RapidAPI-Host': 'wft-geo-db.p.rapidapi.com'
    }
  };

  try {
    await axios.request(options)
        .then(async (res) => {
          cities.push(res.data.data);
          console.log("showing cities : ", cities);
        })
        .catch(error =>{
          console.log("The error is : ", error);
        });
  } catch (error) {
    console.error(error);
  }
}

async function getWeatherDetails(lat, lon) {
  console.log("lat : ", lat);
  console.log("lon : ", lon);
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
    const response = await axios.request(options);
    console.log(response.data);
  } catch (error) {
    console.error(error);
  }
}
</script>

<template>
  <div class="h-screen flex items-center justify-center bg-gray-100">
    <div
      class="max-w-sm w-1/3 mx-auto p-8 bg-white rounded-xl shadow-2xl h-2/3"
    >
      <img
        class="h-24 mx-auto rounded-full ring-4 ring-green-500"
        src="../assets/weather.svg"
        alt="Weather"
      />

      <div class="text-center mt-8">
      </div>

      <hr />

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
          <div v-show="searchCountry.length > 0 && showListCountry" class="absolute left-0 mt-2 w-60 bg-white rounded-lg">
            <ul class="px-3 pb-3 overflow-y-auto text-sm text-gray-700 dark:text-gray-200" aria-labelledby="dropdownSearchButton">
              <li v-for="country in filteredCountries" :key="country">
                <div class="flex items-center pl-2 rounded hover:bg-gray-100">
                  <input
                      @click="selectCountry(country)"
                      id="checkbox-item-11"
                      readonly
                      :value="country"
                      class="w-full py-2 ml-2 text-sm font-medium text-gray-900 rounded dark:text-gray-300 cursor-pointer"
                  />
                </div>
              </li>
            </ul>
          </div>
        </div>
      </div>

      <div v-if="cities.length > 0">
        <div class="relative mt-10">
          <input
              type="text"
              id="input-group-search"
              class="block w-full p-2 pl-10 text-sm text-gray-900 border border-gray-300 rounded-lg bg-gray-50 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-600 dark:border-gray-500 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
              placeholder="Search City"
              v-model="searchCity"
              @focus="showListCity = true"
          />

          <div v-show="searchCity.length > 0 && showListCity" class="absolute left-0 mt-2 w-60 bg-white rounded-lg">
            <ul class="px-3 pb-3 overflow-y-auto text-sm text-gray-700 dark:text-gray-200" aria-labelledby="dropdownSearchButton">
              <li v-for="city in filteredCities" :key="city">
                <div class="flex items-center pl-2 rounded hover:bg-gray-100">
                  <input
                      @click="selectCity(city)"
                      id="checkbox-item-11"
                      readonly
                      :value="city.name"
                      class="w-full py-2 ml-2 text-sm font-medium text-gray-900 rounded dark:text-gray-300 cursor-pointer"
                  />
                </div>
              </li>
            </ul>
          </div>
        </div>
      </div>

<!--      <div class="z-10 bg-white rounded-lg shadow w-60 dark:bg-gray-700">-->
<!--        <div class="p-3">-->
<!--          <label for="input-group-search" class="sr-only">Search</label>-->
<!--          <div class="relative">-->
<!--            <div class="absolute inset-y-0 left-0 flex items-center pl-3 pointer-events-none">-->
<!--              <svg class="w-4 h-4 text-gray-500 dark:text-gray-400" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 20 20">-->
<!--                <path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="m19 19-4-4m0-7A7 7 0 1 1 1 8a7 7 0 0 1 14 0Z"/>-->
<!--              </svg>-->
<!--            </div>-->
<!--            <input type="text" id="input-group-search" class="block w-full p-2 pl-10 text-sm text-gray-900 border border-gray-300 rounded-lg bg-gray-50 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-600 dark:border-gray-500 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500" placeholder="Search Country" v-model="search" >-->
<!--          </div>-->
<!--        </div>-->
<!--        <ul v-if="search.length > 0" class="h-48 px-3 pb-3 overflow-y-auto text-sm text-gray-700 dark:text-gray-200" aria-labelledby="dropdownSearchButton">-->
<!--          <li v-for="country in filteredCountry" :key="country">-->
<!--            <div class="flex items-center pl-2 rounded hover:bg-gray-100">-->
<!--              <input @click="selectCountry(country)" id="checkbox-item-11" readonly :value="country" class="w-full py-2 ml-2 text-sm font-medium text-gray-900 rounded dark:text-gray-300 bg-gray-6=700 cursor-pointer">-->
<!--            </div>-->
<!--          </li>-->
<!--        </ul>-->
<!--      </div>-->


      <!-- <div>
        <p class="text-2xl text-black font-bold text-center mt-2">Weather Details</p>
      </div> -->
    </div>
  </div>
</template>
