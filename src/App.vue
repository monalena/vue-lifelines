<template>
  <div id="app">
    <div class="container-fluid  bg-secondary text-white rounded-0">
      <div class="row">
        <div class="col-sm-8 p-3 mb-2">
          <h1 class="Font-weight-bold">LifeLines</h1>
          <h4 class="font-italic font-weight-bold">Explore Convict Lives</h4>
        </div>
        <div class="col-sm-4 p-3 mb-2">
          <div class="form-group row">
            <label for="GivenName" class="col-sm-5 col-for-label">Enter Given Name</label>
            <div class="col-sm-6">
              <input type="text" class="form-control" id="givenName" placeholder="Given Name">
            </div>
            </div>
          <div class="form-group row">
            <label for="FamilyName" class="col-sm-5 col-for-label">Enter Family Name</label>
            <div class="col-sm-6">
              <input type="text" class="form-control" id="familyName" placeholder="Family Name">
            </div>
          </div>
          <div class="form-group row">
            <label for="ConvictId" class="col-sm-5 col-for-label">Enter Convict Id</label>
            <div class="col-sm-6">
              <input type="text" class="form-control" id="convictId" placeholder="Convict Id"
                     v-model="convictId[0]">
            </div>
          </div>
          <div class="row justify-content-center">
            <div class="col-sm-4 ">
              <button type="submit" class="btn btn-primary" v-on:click="findLifeline">Find Lifeline</button>
            </div>
          </div>
        </div>
      </div>
    </div>
    <Description :specs="specs"></Description>
    <Lifeline :lifeline="lifeline" :specs="specs"></Lifeline>
  </div>
</template>

<script>
import Vue from 'vue'
import { BootstrapVue, IconsPlugin } from 'bootstrap-vue'

// Import Bootstrap an BootstrapVue CSS files (order is important)
import 'bootstrap/dist/css/bootstrap.css'
import 'bootstrap-vue/dist/bootstrap-vue.css'

// Make BootstrapVue available throughout your project
Vue.use(BootstrapVue)
// Optionally install the BootstrapVue icon components plugin
Vue.use(IconsPlugin)

import * as d3 from 'd3';
import Description from './components/Description.vue';
import Lifeline from './components/Lifeline.vue';


export default {
  name: 'App',
  components: {
    Description,
    Lifeline,
  },
  data() {
    return {
      nominal: [],
      lifelines: [],
      convictId: ['9742', '8178'],
      specs: [],
      lifeline: [],
    }
  },
  methods: {
    findLifeline: function() {
      this.lifeline = this.lifelines.filter((x) => x.ConvictId === this.convictId[0]);
      this.specs = this.nominal.filter((x) => x.ConvictId === this.convictId[0]);
    }
  },
  mounted() {
    //load data
    var loadLifelines = data => {
      for (var i = 0; i < data.length; i++) {
        data[i].newDate = new Date(data[i].Date);
        data[i].eventDescription = data[i].Event;
      }
      this.lifelines = data;
      this.lifeline = this.lifelines.filter((x) => x.ConvictId === this.convictId[0]);

      // this.lifelines = this.convictId.map(cid =>
      //         this.lifelines.filter((x) => x.ConvictId === cid));

    }

    var loadNominal = data => {
      this.nominal = data;
      this.specs = data.filter((x) => x.ConvictId === this.convictId[0]);

    }

    d3.csv(process.env.BASE_URL + "lifelines.csv").then(loadLifelines);
    d3.csv(process.env.BASE_URL + "nominal.csv").then(loadNominal).catch(console.log.bind(console));

  }
}
</script>

<style>
#app {
  background-color: ivory;
}



</style>
