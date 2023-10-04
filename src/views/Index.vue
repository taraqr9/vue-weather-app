<script setup>
import { computed, onMounted, ref } from "vue";
import axios from "axios";
import WeatherDetails from "../components/WeatherDetails.vue";
import PlacesNearLocation from "../components/PlacesNearLocation.vue";
import LoadingAnimation from "../components/LoadingAnimation.vue";

const country = ref("");
const cityDetails = ref("");
const regionDetails = ref("");
const searchCountry = ref("");
const searchRegion = ref("");
const searchCity = ref("");
const cities = ref();
const regions = ref();
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
const urlCountry =
  "https://wft-geo-db.p.rapidapi.com/v1/geo/countries?namePrefix=";
const weatherDetails = ref("");
const placesNearLocation = ref("");
const isDropDownOpenForRegion = ref(false);
const isDropDownOpenForCities = ref(false);
const toggledWeatherOrPlaceNear = ref(true);
const offset = ref(0);
const countryId = ref(0);
const loadingIconForCity = ref(false);
const loadingIconForRegion = ref(false);
const loadingIconForDetailsAndPlaces = ref(false);

const filteredCountries = computed(() => {
  const filteredCountries = countries.filter(
    (country) =>
      !searchCountry.value ||
      country.toLowerCase().includes(searchCountry.value.toLowerCase())
  );
  if (filteredCountries.length === 0 && country.value) {
    clearValuesForRegion();
  }

  return filteredCountries;
});

const onCitySearch = async () => {
  if (searchCity.value.length >= 3) {
    await getCities(searchCity.value);
  }
};

const filteredCities = computed(() => cities.value);

function selectCountry(country) {
  searchCountry.value = country;
  clearValuesForRegion();
  showListCountry.value = false;

  getCountry(urlCountry + country);
}

async function getRegions() {
  const options = {
    method: "GET",
    url: `https://wft-geo-db.p.rapidapi.com/v1/geo/countries/${countryId.value}/regions`,
    params: {
      limit: "10",
    },
    headers: {
      "X-RapidAPI-Key": "b7acb9ad46msha339e369a978e13p19d9f0jsn19e09ecd0c54",
      "X-RapidAPI-Host": "wft-geo-db.p.rapidapi.com",
    },
  };

  try {
    await axios
      .request(options)
      .then(async (res) => {
        regions.value = res.data.data;
      })
      .catch((error) => {
        console.log("The error is : ", error);
      });
  } catch (error) {
    console.error(error);
  }
}

function selectRegion(region) {
  loadingIconForCity.value = true;
  if (regionDetails) {
    clearValuesForCities();
  }
  searchRegion.value = region.name;
  regionDetails.value = region;

  toggledDropDownForRegion();

  setTimeout(() => {
    loadingIconForCity.value = false;
  }, 2000);

  getCities();
}

async function getCountry(url) {
  const options = {
    method: "GET",
    url: url,
    params: {
      limit: "10",
    },
    headers: {
      "X-RapidAPI-Key": "b7acb9ad46msha339e369a978e13p19d9f0jsn19e09ecd0c54",
      "X-RapidAPI-Host": "wft-geo-db.p.rapidapi.com",
    },
  };

  try {
    loadingIconForRegion.value = true;

    await axios
      .request(options)
      .then(async (res) => {
        country.value = res.data.data[0];
        return new Promise((resolve) => {
          setTimeout(async () => {
            try {
              country.value = res.data.data[0];
              countryId.value = country.value.wikiDataId;

              await getRegions();

              loadingIconForRegion.value = false;
            } catch (error) {
              console.error("Request failed:", error);
              resolve(null);
            }
          }, 1500);
        });
      })
      .catch((error) => {
        console.log("The error is : ", error);
      });
    loadingIconForRegion.value = false;
  } catch (error) {
    console.error(error);
  }
}

async function getCities(city = "") {
  const options = {
    method: "GET",
    url: `https://wft-geo-db.p.rapidapi.com/v1/geo/countries/${regionDetails.value.countryCode}/regions/${regionDetails.value.isoCode}/cities`,
    params: {
      namePrefix: city ? city : "",
      limit: "10",
      offset: offset.value,
    },
    headers: {
      "X-RapidAPI-Key": "b7acb9ad46msha339e369a978e13p19d9f0jsn19e09ecd0c54",
      "X-RapidAPI-Host": "wft-geo-db.p.rapidapi.com",
    },
  };

  try {
    const res = await axios.request(options);
    if (res.data.data.length > 0) {
      cities.value = res.data.data;

      await new Promise((resolve) => {
        setTimeout(() => {
          resolve();
        }, 1200);
      });
    } else {
      cities.value[0] = { name: "No match found!" };
      cities.value = cities.value.slice(0, 1);
    }
  } catch (error) {}
}

function selectCity(city) {
  showListCity.value = false;
  clearValuesForWeather();
  loadingIconForDetailsAndPlaces.value = true;
  searchCity.value = city.name;
  cityDetails.value = city;

  setTimeout(() => {
    getWeatherDetails(city.latitude.toFixed(2), city.longitude.toFixed(2));
    getPlacesNearLocation(city);

    loadingIconForDetailsAndPlaces.value = false;
  }, 2000);
}

async function getWeatherDetails(lat, lon) {
  const options = {
    method: "GET",
    url: "https://weatherapi-com.p.rapidapi.com/current.json",
    params: { q: `${lat}, ${lon}` },
    headers: {
      "X-RapidAPI-Key": "b7acb9ad46msha339e369a978e13p19d9f0jsn19e09ecd0c54",
      "X-RapidAPI-Host": "weatherapi-com.p.rapidapi.com",
    },
  };

  try {
    weatherDetails.value = await axios.request(options);
  } catch (error) {
    console.error(error);
  }
}

async function getPlacesNearLocation(city) {
  const options = {
    method: "GET",
    url: `https://wft-geo-db.p.rapidapi.com/v1/geo/cities/${city.wikiDataId}/nearbyCities`,
    params: { radius: "100" },
    headers: {
      "X-RapidAPI-Key": "b7acb9ad46msha339e369a978e13p19d9f0jsn19e09ecd0c54",
      "X-RapidAPI-Host": "wft-geo-db.p.rapidapi.com",
    },
  };

  try {
    placesNearLocation.value = await axios.request(options);
  } catch (error) {
    console.error(error);
  }
}

function toggledDropDownForRegion() {
  isDropDownOpenForRegion.value = !isDropDownOpenForRegion.value;
  isDropDownOpenForCities.value = false;
}

function toggledWeatherOrPlaceNearLocation(value) {
  toggledWeatherOrPlaceNear.value = value;
}

function clearValuesForRegion() {
  cityDetails.value = "";
  weatherDetails.value = "";
  cities.value = "";
  regions.value = "";
  regionDetails.value = "";
}

function clearValuesForCities() {
  isDropDownOpenForCities.value = false;
  weatherDetails.value = "";
  cities.value = "";
  regionDetails.value = "";
  searchCity.value = "";
}

function clearValuesForWeather() {
  isDropDownOpenForCities.value = false;
  weatherDetails.value = "";
}
</script>

<template>
  <title>Weather App</title>

  <div class="h-screen flex items-center justify-center bg-gray-400">
    <div
      class="max-w-sm w-1/3 mx-auto p-8 bg-white rounded-xl shadow-2xl h-full"
    >
      <h3
        class="text-center text-2xl mb-2 bg-green-400 rounded-full p-2 text-white"
      >
        Weather App
      </h3>
      <img
        class="h-24 mx-auto rounded-full ring-4 ring-green-500"
        src="../assets/weather.svg"
        alt="Weather"
      />
      <div class="text-center mt-8"></div>

      <div
        class="absolute inset-0 z-50"
        v-if="
          loadingIconForRegion ||
          loadingIconForCity ||
          loadingIconForDetailsAndPlaces
        "
      >
        <LoadingAnimation />
      </div>
      <div class="relative z-40">
        <input
          type="text"
          id="input-group-search"
          class="block w-full p-2 pl-10 text-sm text-gray-900 border border-gray-300 rounded-lg bg-gray-50 focus:ring-blue-500 focus:border-blue-500"
          placeholder="Search Country"
          v-model="searchCountry"
          @focus="showListCountry = true"
        />
        <div
          v-show="searchCountry.length > 0 && showListCountry"
          class="absolute left-0 mt-2 w-60 rounded-2xl bg-white shadow-lg ring-1 ring-black ring-opacity-5 focus:outline-none"
        >
          <ul aria-labelledby="dropdownSearchButton">
            <li
              v-show="searchCountry.length > 0 && showListCountry"
              class="flex items-center pl-4 overflow-y-auto hover:bg-sky-100 rounded-2xl"
              v-for="country in filteredCountries"
              :key="country"
            >
              <input
                @click="selectCountry(country)"
                id="checkbox-item-11"
                readonly
                :value="country"
                class="w-full py-2 text-sm font-medium hover:bg-sky-100 text-black cursor-pointer rounded-xl"
              />
            </li>

            <li
              v-if="showListCountry && filteredCountries.length === 0"
              class="flex items-center pl-4 overflow-y-auto hover:bg-sky-100 rounded-2xl"
            >
              <input
                id="checkbox-item-11"
                readonly
                value="No match found!"
                class="w-full py-2 text-sm font-medium hover:bg-sky-100 text-black cursor-pointer rounded-xl"
              />
            </li>
          </ul>
        </div>
      </div>

      <div v-if="regions && regions.length > 0">
        <div class="relative mt-10 z-30">
          <div>
            <button
              @click="toggledDropDownForRegion"
              type="button"
              class="inline-flex w-full p-2 pl-10 gap-x-1.5 rounded-md bg-white px-3 py-2 text-sm text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 hover:bg-gray-50"
              id="menu-button"
              aria-expanded="true"
              aria-haspopup="true"
            >
              {{ regionDetails ? regionDetails.name : "Select Regions" }}
              <svg
                class="-mr-1 h-5 w-5 text-gray-400"
                viewBox="0 0 20 20"
                fill="currentColor"
                aria-hidden="true"
              >
                <path
                  fill-rule="evenodd"
                  d="M5.23 7.21a.75.75 0 011.06.02L10 11.168l3.71-3.938a.75.75 0 111.08 1.04l-4.25 4.5a.75.75 0 01-1.08 0l-4.25-4.5a.75.75 0 01.02-1.06z"
                  clip-rule="evenodd"
                />
              </svg>
            </button>
          </div>

          <ul
            v-if="isDropDownOpenForRegion"
            class="absolute mt-2 w-60 origin-top-right rounded-2xl bg-white shadow-lg ring-1 ring-black ring-opacity-5 focus:outline-none"
          >
            <li
              v-for="region in regions"
              :key="region"
              @click="selectRegion(region)"
              class="text-gray-700 block px-4 py-2 text-sm cursor-pointer hover:bg-sky-100 rounded-2xl"
              role="menuitem"
              id="menu-item-0"
            >
              {{ region.name }}
            </li>
          </ul>
        </div>
      </div>

      <div v-if="cities && cities.length > 0">
        <div class="relative mt-10 z-20">
          <input
            type="text"
            id="input-group-search"
            class="block w-full p-2 pl-10 text-sm text-gray-900 border border-gray-300 rounded-lg bg-gray-50 focus:ring-blue-500 focus:border-blue-500"
            placeholder="Search City"
            v-model="searchCity"
            @input="onCitySearch"
            @focus="showListCity = true"
          />
          <div
            v-show="searchCity && showListCity"
            class="absolute left-0 mt-2 w-60 rounded-2xl bg-white shadow-lg ring-1 ring-black ring-opacity-5 focus:outline-none"
          >
            <ul aria-labelledby="dropdownSearchButton">
              <li
                class="flex items-center pl-4 overflow-y-auto hover:bg-sky-100 rounded-2xl"
                v-for="city in filteredCities"
                :key="city"
              >
                <input
                  @click="selectCity(city)"
                  id="checkbox-item-11"
                  readonly
                  :value="city.name"
                  class="w-full py-2 text-sm font-medium hover:bg-sky-100 text-black cursor-pointer rounded-xl"
                />
              </li>
            </ul>
          </div>
        </div>
      </div>

      <div v-if="weatherDetails" class="relative mt-4">
        <div class="flex items-center justify-between">
          <button
            @click="toggledWeatherOrPlaceNearLocation(true)"
            class="btn-green"
          >
            Weather Details
          </button>
          <button
            @click="toggledWeatherOrPlaceNearLocation(false)"
            class="btn-green"
          >
            Places Near
          </button>
        </div>

        <div v-if="toggledWeatherOrPlaceNear">
          <WeatherDetails :weatherDetails="weatherDetails" />
        </div>
        <div v-if="!toggledWeatherOrPlaceNear">
          <PlacesNearLocation :placesNearLocation="placesNearLocation" />
        </div>
      </div>
    </div>
  </div>
</template>
