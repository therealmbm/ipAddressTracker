<script setup>
import { ref, onMounted } from "vue";
import axios from "axios";
import L from "leaflet";

const ipAddress = ref('');
const apiKey = ref('at_EGucdGRd0Y31oUaGuSJSMa4DMeSu6');
const response = ref({});
const map = ref(null);

const getResponse = async () => {
  try {
    const getResponse = await axios.get(`https://geo.ipify.org/api/v2/country,city?apiKey=${apiKey.value}&ipAddress=${ipAddress.value}`);
    response.value = getResponse.data;

    // Update map location
    if (response.value.location) {
      const { lat, lng } = response.value.location;
      if (map.value) {
        map.value.setView([lat, lng], 13);
        L.marker([lat, lng]).addTo(map.value);
      }
    }
  } catch (error) {
    console.error('Error fetching the response', error);
  }
};

// Initialize the map on component mount
onMounted(() => {
  map.value = L.map("map").setView([0, 0], 2); // Default to world view
  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '© OpenStreetMap contributors'
  }).addTo(map.value);
});
</script>

<template>
  <div class="w-full bg-[url('../assets/pattern-bg-desktop.png')] bg-cover bg-center h-72">
    <div class="grid place-items-center w-full pt-10 px-4">
      <h1 class="text-3xl text-white mb-10 font-semibold text-center">
        IP Address Tracker
      </h1>
      <form class="flex flex-col md:flex-row md:w-1/2 w-full items-center justify-center">
        <input type="text"
          class="py-5 px-4 text-base md:text-xl rounded-tl-xl rounded-bl-xl w-full md:w-3/5 mb-4 md:mb-0 md:mr-2 lg:mr-0"
          placeholder="Search for any IP address or domain" v-model="ipAddress" />
        <button
          class="text-white text-base md:text-2xl font-bold bg-black py-5 md:py-4 px-4 md:px-5 rounded-tr-xl rounded-br-xl w-full md:w-auto"
          @click.prevent="getResponse">
          &gt;
        </button>
      </form>
    </div>
    <div
      class="responseData grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4 bg-white w-full lg:w-4/5 p-5 mx-auto mt-4 rounded-lg shadow-xl">
      <div class="m-2 md:m-4 text-center">
        <p class="text-xs md:text-sm text-gray-400 font-bold mb-3 tracking-widest">IP ADDRESS</p>
        <h2 class="text-xl md:text-3xl font-bold text-gray-800">
          {{ response?.ip ?? '*****' }}
        </h2>
      </div>
      <div class="m-2 md:m-4 text-center">
        <p class="text-xs md:text-sm text-gray-400 font-bold mb-3 tracking-widest">LOCATION</p>
        <h2 class="text-xl md:text-3xl font-bold text-gray-800">
          {{ response?.location?.city ?? '*****' }},{{ response?.location?.region ?? '*****' }}, {{
            response?.location?.country ?? '*****' }}
        </h2>
      </div>
      <div class="m-2 md:m-4 text-center">
        <p class="text-xs md:text-sm text-gray-400 font-bold mb-3 tracking-widest">TIMEZONE</p>
        <h2 class="text-xl md:text-3xl font-bold text-gray-800">
          UTC {{ response?.location?.timezone ?? '*****' }}
        </h2>
      </div>
      <div class="m-2 md:m-4 text-center">
        <p class="text-xs md:text-sm text-gray-400 font-bold mb-3 tracking-widest">ISP</p>
        <h2 class="text-xl md:text-3xl font-bold text-gray-800">
          {{ response?.isp ?? '*****' }}
        </h2>
      </div>
    </div>
    <div class="shadow-lg w-full" id="map"></div>
  </div>
</template>

<style scoped>
#map {
  margin-top: -49px;
  height: calc(100vh - 288px);
  z-index: -1;
}

@media (max-width: 768px) {
  .responseData {
    padding: 3rem 1rem;
    /* Adjust padding for mobile view */
  }

  /* Center content for mobile */
  .responseData .text-center {
    text-align: center;
  }

  /* Button and input on the same line for mobile */
  form {
    flex-direction: row;
    /* On mobile, display input and button side by side */
    justify-content: center;
    /* Center them */
  }

  input {
    margin-bottom: 0;
  }

  button {
    width: auto;
    /* Remove full width for the button on mobile */
  }
}
</style>
