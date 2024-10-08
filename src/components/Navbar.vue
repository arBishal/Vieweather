<template>
  <header class="sticky top-0 bg-white border mb-6 z-10">
    <nav class="container max-w-screen-md flex items-center justify-between gap-4 py-4 text-black shadow-sm">
      <!-- left component -->
      <RouterLink :to="{ name: 'home' }">
        <div class="flex justify-center items-center gap-3 hover:text-neutral-600 duration-300">
          <i class="fa-solid fa-paw text-3xl"></i>
          <span class="text-2xl font-bold">Meoweather</span>
        </div>
      </RouterLink>

      <!-- right component -->
      <div class="flex gap-4">
        <i v-if="route.query.preview" @click="addCity"
          class="fa-solid fa-circle-plus text-2xl sm:text-3xl hover:text-neutral-700 duration-300 cursor-pointer"></i>
        <i v-if="route.name === 'cityView' && !route.query.preview" @click="removeCity"
          class="fa-solid fa-circle-minus text-2xl sm:text-3xl hover:text-neutral-700 duration-300 cursor-pointer"></i>
        <i @click="toggleModal"
          class="fa-solid fa-circle-question text-2xl sm:text-3xl hover:text-neutral-700 duration-300 cursor-pointer"></i>
      </div>

      <!-- modal -->
      <Modal :modalActive="modalActive" @closeModal="toggleModal">
        <h1 class="w-full text-black text-2xl font-bold">About the App</h1>
        <hr class="w-full" />

        <p class="w-full">
          The Local Weather allows you to track the current and future weather of cities of your
          choosing.
        </p>

        <h2 class="w-full text-xl font-bold">How it works</h2>
        <ol class="w-full list-disc list-inside">
          <li>Search for your city by entering the name into the search bar.</li>
          <li>
            Select a city within the results, this will take you to the current weather for your
            selection.
          </li>
          <li>
            Track the city by clicking on the "+" icon in the top right. This will save the city to
            view at a later time on the home page.
          </li>
        </ol>

        <h2 class="w-full text-xl font-bold">Removing a city</h2>
        <p>
          If you no longer wish to track a city, simply select the city within the home page. At the
          bottom of the page, there will be am option to delete the city.
        </p>
      </Modal>
    </nav>
  </header>
</template>

<script setup>
import { RouterLink, useRoute, useRouter } from 'vue-router';
import { uid } from 'uid';
import { ref } from 'vue';
import Modal from './Modal.vue';

const route = useRoute();
const router = useRouter();

const modalActive = ref(false);

const toggleModal = () => {
  modalActive.value = !modalActive.value;
}

const savedCities = ref([]);

const addCity = () => {
  if (localStorage.getItem('savedCities')) {
    savedCities.value = JSON.parse(localStorage.getItem('savedCities'));
  }

  const locationObj = {
    id: uid(),
    state: route.params.state,
    city: route.params.city,
    coords: {
      lat: route.query.lat,
      lng: route.query.lng,
    },
  };

  savedCities.value.push(locationObj);
  localStorage.setItem('savedCities', JSON.stringify(savedCities.value));

  let query = Object.assign({}, route.query);
  delete query.preview;
  query.id = locationObj.id;
  router.replace({ query });
  console.log(route.name);
};


const removeCity = () => {
  const cities = JSON.parse(localStorage.getItem('savedCities'));
  const updatedCities = cities.filter(city => city.id !== route.query.id);
  localStorage.setItem('savedCities', JSON.stringify(updatedCities));
  router.push({
    name: 'home',
  });
  console.log(route.name);
}

</script>
