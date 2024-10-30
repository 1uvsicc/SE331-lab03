<template>
  <div>
    <h1>Passenger Details</h1>
   
    <div v-if="passenger">
      <p>ID: {{ passenger._id }}</p>
      <p>Name: {{ passenger.name }}</p>
      <p>Trips: {{ passenger.trips }}</p>
      <button @click="goToAirlineDetails">View Airline Details</button>
     
    </div>
    
    <p v-else-if="loading">Loading...</p>
   
    <p v-else>Resource not found. Please check the ID and try again.</p>
    
  </div>
</template>

<script>
import { ref, defineComponent, onMounted } from 'vue';
import axios from 'axios';
import { useRoute,useRouter } from 'vue-router';
export default defineComponent({
  props: {
    id: {
      type: String,
      required: true
    }
  },
  setup(props) {
    const route = useRoute();
    const router = useRouter();
    console.log("Received ID:", props.id);
    const passenger = ref(null);
    const loading = ref(true);
    const error = ref(false);

   
    const fetchPassengerDetails = async () => {
      try {
        const response = await axios.get(`https://api.instantwebtools.net/v1/passenger/${route.params.id}`);
        passenger.value = response.data;
        loading.value = false; 
      } catch (error) {
        console.error('Failed to fetch passenger details:', error);

        if (error.response && error.response.status === 404) {
      
      error.value = "not-found";
    } else {
      
      error.value = true;
    }
    loading.value = false;
  
      }
    };

    const goToAirlineDetails = () => {
      if (passenger.value && passenger.value.airline.length > 0) {
        const airlineId = passenger.value.airline[0]._id;
        router.push({ name: 'airlineDetails', params: { id: airlineId } });
      }
    };

    onMounted(() => {
      fetchPassengerDetails();
    });
   
    return {
      passenger,
      loading,
      error,
      goToAirlineDetails
    };
  }
});
</script>

<style>

</style>