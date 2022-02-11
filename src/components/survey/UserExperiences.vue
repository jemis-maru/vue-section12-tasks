<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="fetchExps">Load Submitted Experiences</base-button>
      </div>
      <p v-if="isLoading">Loading....</p>
      <p class="errorMsg" v-else-if="!isLoading && anyError">{{ errMsg }}</p>
      <ul v-else-if="!isLoading && !anyError && results && results.length>0">
        <survey-result
          v-for="result in results"
          :key="result.id"
          :name="result.name"
          :rating="result.rating"
        ></survey-result>
      </ul>
      <p v-else>No data found!</p>
    </base-card>
  </section>
</template>

<script>
import SurveyResult from './SurveyResult.vue';

export default {
  // props: ['results'],
  components: {
    SurveyResult,
  },
  data(){
    return{
      results: [],
      isLoading: false,
      anyError: false,
      errMsg: '',
    };
  },
  methods:{
    fetchExps(){
      this.isLoading = true;
      fetch(process.env.VUE_APP_SERVER_URL)
      .then(res => {
        if(res.ok){
          return res.json();
        }
      }).then(data => {
        this.isLoading = false;
        // console.log(data);
        const results = [];
        for(const id in data){
          // console.log(data[id]);
          results.push({
              id: id,
              name: data[id].name,
              rating: data[id].rating
          });
        }
        this.results = results;
      })
      .catch(err => {
        // console.log(err.message);
        this.isLoading = false;
        this.anyError = true;
        this.errMsg = err.message + '!';
      });
    },
  },
  mounted(){
    this.fetchExps();
  },
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}

.errorMsg{
  color: red;
}
</style>