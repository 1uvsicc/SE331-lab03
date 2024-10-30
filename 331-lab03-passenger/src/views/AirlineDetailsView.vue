<template>
  <div>
    <h1>Airline Details</h1>
    <div v-if="airline">
      <p>ID: {{ airline._id }}</p>
      <p>Name: {{ airline.name }}</p>
      <p>Country: {{ airline.country }}</p>
      <p>Slogan: {{ airline.slogan }}</p>
      <p>Headquarters: {{ airline.head_quaters }}</p>
      <p>Website: <a :href="airline.website" target="_blank">{{ airline.website }}</a></p>
      <p>Established: {{ airline.established }}</p>
    </div>
    <div v-else>
      Loading...
    </div>
  </div>
</template>

<script>
import { ref, defineComponent } from 'vue';
import axios from 'axios';
import { useRoute } from 'vue-router';
export default defineComponent({
  props: {
    id: {
      type: String,
      required: true
    }
  },
  setup(props) {
    const airline = ref(null);
    const route = useRoute();
    const fetchAirlineDetails = async () => {
      try {
        const response = await axios.get(`https://api.instantwebtools.net/v1/airlines/${route.params.id}`);
        airline.value = response.data;
      } catch (error) {
        console.error('Failed to fetch airline details:', error);
      }
    };

    fetchAirlineDetails();

    return {
      airline
    };
  }
});
</script>