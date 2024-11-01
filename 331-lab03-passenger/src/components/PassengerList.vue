<template>
  <div>
    <h1>Passenger List</h1>
    <ul>
      <li v-for="passenger in passengers" :key="passenger._id"
      @click="openPassengerDetails(passenger._id)">
        {{ passenger.name }} - Trip Amounts: {{ passenger.trips }}
      </li>
    </ul>
    <button @click="handlePageChange(-1)" :disabled="currentPage === 0">prev page</button>
    <button @click="handlePageChange(1)" :disabled="currentPage >= totalPages - 1">next page</button>
    <input type="number" v-model="jumpPage" min="0" :max="totalPages - 1" />
    <button @click="jumpToPage">jump to</button>
    <div v-if="selectedPassenger">
      <h2>Passenger Details:</h2>
      <p>ID: {{ selectedPassenger._id }}</p>
      <p>Name: {{ selectedPassenger.name }}</p>
      <p>Trips: {{ selectedPassenger.trips }}</p>
      <h3>Airlines Details:</h3>
  <ul>
    <li v-for="airline in selectedPassenger.airline" :key="airline._id">
      <strong>ID:</strong> {{ airline._id }}
      <br>
      <strong>Name:</strong> {{ airline.name }}
      <br>
      <strong>Country:</strong> {{ airline.country }}
      <br>
      <strong>Slogan:</strong> {{ airline.slogan }}
      <br>
      <strong>Headquarters:</strong> {{ airline.head_quaters }}
      <br>
      <strong>Website:</strong> <a :href="airline.website" target="_blank">{{ airline.website }}</a>
      <br>
      <strong>Established:</strong> {{ airline.established }}
      <br>
      <strong>__v:</strong> {{ airline.__v }}
    </li>
  </ul>
  <button @click="selectedPassenger = null">Close</button>
  </div>
  </div>
</template>

<script>
import axios from 'axios';
import { ref } from 'vue';
import { useRouter } from 'vue-router';
export default {
  setup() {
    const passengers = ref([]);
    const currentPage = ref(0);
    const totalPages = ref(0);
    const jumpPage = ref(0);
    const selectedPassenger = ref(null);
    const router = useRouter();
   

    const fetchPassengers = async (page) => {
      try {
        const response = await axios.get(`https://api.instantwebtools.net/v1/passenger?page=${page}&size=10`);
        passengers.value = response.data.data;
        totalPages.value = response.data.totalPages; 
      } catch (error) {
        console.error('Failed to fetch passengers:', error);
      }
    };

    const handlePageChange = (offset) => {
      const nextPage = currentPage.value + offset;
      if (nextPage >= 0 && nextPage < totalPages.value) { 
        currentPage.value = nextPage;
        fetchPassengers(nextPage); 
      }
    };
    const jumpToPage = () => {
      if (jumpPage.value >= 0 && jumpPage.value < totalPages.value) {
        currentPage.value = jumpPage.value;
        fetchPassengers(jumpPage.value);
      } else {
        alert('plz enter right number');
        jumpPage.value = 0; 
      }
    };

   const openPassengerDetails = (id) => {
      // 使用 router.push 方法导航到 PassengerDetailsView 组件，并传递乘客 ID 作为参数
      
      router.push({ name: 'passengerDetails', params: { id } });
      
     };
  
    fetchPassengers(currentPage.value);

    return {
      passengers,
      currentPage,
      totalPages,
      handlePageChange,
      jumpPage,
      jumpToPage,
      openPassengerDetails,
      selectedPassenger
    };
  },
};

</script>

<style>

</style>