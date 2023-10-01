<script setup>
import {computed, ref} from "vue";
import axios from 'axios';

const country = ref('');
const cityName = ref('');
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
const urlCity = "https://wft-geo-db.p.rapidapi.com/v1/geo/cities?countryIds=";
const weatherDetails = ref('');
const isDropDownOpen = ref(false);

const filteredCountries = computed(() => {
  return countries.filter((country) => !searchCountry.value || country.toLowerCase().includes(searchCountry.value.toLowerCase()));
});

function selectCountry(country) {
  searchCountry.value = country;
  clearValues();

  getCountry(urlCountry + country);
}

function selectCity(city) {
  searchCity.value = city.name;
  cityName.value = city.name;

  toggledDropDown();
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
                await getCities(urlCity + country.value.wikiDataId);
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

function toggledDropDown() {
  isDropDownOpen.value = !isDropDownOpen.value;
}

function clearValues() {
  showListCountry.value = false;
  cityName.value = '';
  isDropDownOpen.value = false;
  weatherDetails.value = '';
  cities.value = '';
}
</script>

<template>
  <div class="h-screen flex items-center justify-center bg-gray-400">
    <div
        class="max-w-sm w-1/3 mx-auto p-8 bg-white rounded-xl shadow-2xl h-3/4"
    >
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

      <div v-if="cities">
        <div class="relative mt-10">
          <div>
            <button @click="toggledDropDown" type="button"
                    class="inline-flex w-full justify-center gap-x-1.5 rounded-md bg-white px-3 py-2 text-sm font-semibold text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 hover:bg-gray-50"
                    id="menu-button" aria-expanded="true" aria-haspopup="true">
              {{ cityName ? cityName : "Select City" }}
              <svg class="-mr-1 h-5 w-5 text-gray-400" viewBox="0 0 20 20" fill="currentColor" aria-hidden="true">
                <path fill-rule="evenodd"
                      d="M5.23 7.21a.75.75 0 011.06.02L10 11.168l3.71-3.938a.75.75 0 111.08 1.04l-4.25 4.5a.75.75 0 01-1.08 0l-4.25-4.5a.75.75 0 01.02-1.06z"
                      clip-rule="evenodd"/>
              </svg>
            </button>
          </div>

          <div v-if="isDropDownOpen"
               class="mt-2 origin-top-right rounded-md bg-white shadow-lg ring-1 ring-black ring-opacity-5 focus:outline-none"
               role="menu" aria-orientation="vertical" aria-labelledby="menu-button" tabindex="-1">
            <div class="py-1" role="none">
              <li v-for="city in cities" :key="city" @click="selectCity(city)"
                  class="text-gray-700 block px-4 py-2 text-sm cursor-pointer hover:bg-sky-100 rounded-2xl"
                  role="menuitem" tabindex="-1" id="menu-item-0">
                {{ city.name }}
              </li>
            </div>
          </div>
        </div>
      </div>

      <div v-if="weatherDetails" class="mt-8">
        <div>
          <div class="px-4 sm:px-0 flex justify-center">
            <h3 class="text-base font-semibold leading-7 text-gray-900">Weather Details</h3>
          </div>
          <div class="mt-4 border-2 rounded-xl p-4">
            <div class="flex items-center justify-center">
              <img :src="weatherDetails.data.current.condition.icon" alt="weather"/>
            </div>
            <dl class="divide-y divide-gray-100">
              <div class="px-2 py-2 sm:grid sm:grid-cols-3 sm:gap-4 sm:px-0">
                <dt class="text-sm font-medium leading-6 text-gray-900">Condition</dt>
                <dd class="mt-1 text-sm leading-6 text-gray-700 sm:col-span-2 sm:mt-0">
                  {{ weatherDetails.data.current.condition.text }}
                </dd>
              </div>
              <div class="px-2 py-2 sm:grid sm:grid-cols-3 sm:gap-4 sm:px-0">
                <dt class="text-sm font-medium leading-6 text-gray-900">Temperature</dt>
                <dd class="mt-1 text-sm leading-6 text-gray-700 sm:col-span-2 sm:mt-0">
                  {{ weatherDetails.data.current.temp_c }}
                </dd>
              </div>
              <div class="px-2 py-2 sm:grid sm:grid-cols-3 sm:gap-4 sm:px-0">
                <dt class="text-sm font-medium leading-6 text-gray-900">Day/Night</dt>
                <dd class="mt-1 text-sm leading-6 text-gray-700 sm:col-span-2 sm:mt-0">
                  {{ weatherDetails.data.current.is_day === 1 ? "Day" : "Night" }}
                </dd>
              </div>
              <div class="px-2 py-2 sm:grid sm:grid-cols-3 sm:gap-4 sm:px-0">
                <dt class="text-sm font-medium leading-6 text-gray-900">Cloud</dt>
                <dd class="mt-1 text-sm leading-6 text-gray-700 sm:col-span-2 sm:mt-0">
                  {{ weatherDetails.data.current.cloud }}
                </dd>
              </div>
              <div class="px-2 py-2 sm:grid sm:grid-cols-3 sm:gap-4 sm:px-0">
                <dt class="text-sm font-medium leading-6 text-gray-900">Feels-like</dt>
                <dd class="mt-1 text-sm leading-6 text-gray-700 sm:col-span-2 sm:mt-0">
                  {{ weatherDetails.data.current.feelslike_c }}
                </dd>
              </div>
            </dl>
          </div>
        </div>


      </div>
    </div>
  </div>
</template>
