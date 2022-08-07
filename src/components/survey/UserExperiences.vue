<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="loadData">Load Submitted Experiences</base-button>
      </div>
      <p v-if="isLoading">Loading ....</p>
      <p v-else-if="!isLoading && error ">
        Error: {{ error }}
      </p>
      <p v-else-if="!isLoading &&  (results.length === 0 || !results)">No results to display.</p>

      <ul v-else-if="!isLoading && results && results.length  > 0 ">

        <survey-result
            v-for="result in results"
            :key="result.id"
            :name="result.name"
            :rating="result.rating"
        ></survey-result>
      </ul>
    </base-card>
  </section>
</template>

<script>
import SurveyResult from './SurveyResult.vue';
import axios from "axios";

export default {
  components: {
    SurveyResult,
  },
  data() {
    return {
      results: [],
      isLoading: false,
    }
  },
  methods: {

    loadData() {
      this.isLoading = true;
      this.error = null;
      // get axios to get data from server https://vue-demi-736ba-default-rtdb.firebaseio.com/survey.json returns json data
      axios.get('https://vue-demi-736ba-default-rtdb.firebaseio.com/survey.json')
          // in json data, loop through each result and create a new SurveyResult component for each result
          .then(response => {
            this.isLoading = false;
            const data = response.data;
            const results = [];
            for (const key in data) {
              results.push({
                id: key,
                name: data[key].name, // data[key].name is the name of the key in the json data
                rating: data[key].rating, // data[key].rating is the rating of the key in the json data
              });
            }
            this.results = results;
            console.log(results);
          })
          .catch(error => {
            this.isLoading = false;

            console.log(error);
            this.error = 'Error loading data';
          });
    }
  },
  mounted() {
    this.loadData();  //
  }
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>