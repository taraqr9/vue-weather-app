<script setup>
</script>

<template>
  <title>Weather App</title>

  <div class="h-screen mx-auto bg-gray-400">

    <div class="w-1/4 mx-auto p-8 bg-white rounded-xl shadow-2xl h-full">
      <h3 class="text-center mx-auto w-2/3 text-xl mb-2 bg-green-400 rounded-full p-2 text-white">Weather App</h3>
      <img
          class="h-24 mx-auto m-5 rounded-full ring-4 ring-green-400"
          src="../assets/weather.svg"
          alt="Weather"
      />

      <div>
        <input
            type="text"
            class="w-full p-2 pl-10 text-sm text-gray-900 border border-gray-300 rounded-lg bg-gray-50 focus:ring-blue-500 focus:border-blue-500"
            placeholder="Search Country"
        />

        <div class="mt-2 w-60 rounded-md bg-white shadow-lg ring-1 ring-black ring-opacity-10 focus:outline-none z-50">
          <ul class="overflow-y-auto max-h-40 scroll-smooth focus:scroll-auto" aria-labelledby="dropdownSearchButton">
            <li class="flex items-center pl-4 hover:bg-sky-100 rounded-2xl">
              <input readonly value="country 1" class="w-full py-2 text-sm font-medium hover:bg-sky-100 text-black cursor-pointer rounded-xl"
              />
            </li>
            <li class="flex items-center pl-4 hover:bg-sky-100 rounded-2xl">
              <input readonly value="country 1" class="w-full py-2 text-sm font-medium hover:bg-sky-100 text-black cursor-pointer rounded-xl"
              />
            </li>
            <li class="flex items-center pl-4 hover:bg-sky-100 rounded-2xl">
              <input readonly value="country 1" class="w-full py-2 text-sm font-medium hover:bg-sky-100 text-black cursor-pointer rounded-xl"
              />
            </li>
            <li class="flex items-center pl-4 hover:bg-sky-100 rounded-2xl">
              <input readonly value="country 1" class="w-full py-2 text-sm font-medium hover:bg-sky-100 text-black cursor-pointer rounded-xl"
              />
            </li>
            <li class="flex items-center pl-4 hover:bg-sky-100 rounded-2xl">
              <input readonly value="country 1" class="w-full py-2 text-sm font-medium hover:bg-sky-100 text-black cursor-pointer rounded-xl"
              />
            </li>
            <li class="flex items-center pl-4 hover:bg-sky-100 rounded-2xl">
              <input readonly value="country 1" class="w-full py-2 text-sm font-medium hover:bg-sky-100 text-black cursor-pointer rounded-xl"
              />
            </li>
            <li class="flex items-center pl-4 hover:bg-sky-100 rounded-2xl">
              <input readonly value="country 1" class="w-full py-2 text-sm font-medium hover:bg-sky-100 text-black cursor-pointer rounded-xl"
              />
            </li>
            <li class="flex items-center pl-4 hover:bg-sky-100 rounded-2xl">
              <input readonly value="country 1" class="w-full py-2 text-sm font-medium hover:bg-sky-100 text-black cursor-pointer rounded-xl"
              />
            </li>
            <li class="flex items-center pl-4 hover:bg-sky-100 rounded-2xl">
              <input readonly value="country 1" class="w-full py-2 text-sm font-medium hover:bg-sky-100 text-black cursor-pointer rounded-xl"
              />
            </li>
            <li class="flex items-center pl-4 hover:bg-sky-100 rounded-2xl">
              <input readonly value="country 1" class="w-full py-2 text-sm font-medium hover:bg-sky-100 text-black cursor-pointer rounded-xl"
              />
            </li>
            <li class="flex items-center pl-4 hover:bg-sky-100 rounded-2xl">
              <input readonly value="country 1" class="w-full py-2 text-sm font-medium hover:bg-sky-100 text-black cursor-pointer rounded-xl"
              />
            </li>
            <li class="flex items-center pl-4 hover:bg-sky-100 rounded-2xl">
              <input readonly value="country 1" class="w-full py-2 text-sm font-medium hover:bg-sky-100 text-black cursor-pointer rounded-xl"
              />
            </li>
          </ul>
        </div>
      </div>
    </div>

  </div>

</template>
