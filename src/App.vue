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
            <label for="GivenName" class="col-sm-5 col-for-label mt-2">Enter Given Name</label>
            <div class="col-sm-6">
              <input type="text" class="form-control" id="givenName" placeholder="Given Name">
            </div>
            </div>
          <div class="form-group row">
            <label for="FamilyName" class="col-sm-5 col-for-label mt-2">Enter Family Name</label>
            <div class="col-sm-6">
              <input type="text" class="form-control" id="familyName" placeholder="Family Name">
            </div>
          </div>
          <div class="form-group row">
            <label for="ConvictId" class="col-sm-5 col-for-label mt-2">Enter Convict Id</label>
            <div class="col-sm-6">
              <input type="text" class="form-control" id="convictId" placeholder="Convict Id"
                     v-model="convictId">
            </div>
          </div>
          <div class="row justify-content-center">
            <div class="col-sm-6 ml-4">
              <button type="submit" class="btn btn-primary" v-on:click="selectLifeline">Find Lifeline</button>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="row">
      <div class="col-sm-4 p-3 mb-2">
        <Description :specs="specs"></Description>
      </div>
      <div class="col-sm-8 p-3 mb-2">
        <Legend></Legend>
      </div>
    </div>
    <Lifeline @selectVoyage="selectVoyage" @selectPerson="selectPerson" @selectConvict="selectConvict"
              :selectedLifelines="selectedLifelines"></Lifeline>
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
import Legend from './components/Legend.vue';

const factory = new Set([10,13,14]);
const confinement = new Set([30,31]);


export default {
  name: 'App',
  components: {
    Description,
    Lifeline,
    Legend
  },
  data() {
    return {
      nominal: [],
      lifelines: {},
      sentences: {},
      voyages: {},
      persons: {},
      given_to_family_name: {},
      family_to_given_name: {},
      convictId: '',
      // Elizabeth Studham and Elizabeth Parker
      // selectedConvictIds: ['8717', '5304'],
      // Insubordination charge of 6/5/1839 Cascades Factory
      // selectedConvictIds: ["9615","9614","9900","9284","6782","8647","13065","12904","9552","9525","12782","12846","7924","6071","9070","H299At","12842","1210","10023","M269At","1976","1163","12840","12789","12870","9542","6772","4074","12906","6064","12776","9847","1911","679","T135At","12856","8624","S328At","S329At","9967","1910","1901"],
      // Collective Resistance of 18/10/1837 Launceston FF
      // selectedConvictIds: ['5280', '6689', '13293', '4233', '9834', '4041', '4008', '8710', '6782', '9857', '5319', '4057', '4127', '4063', '1954'],
      // selectedConvictIds: [ '4233', '5280'],
      selectedConvictIds: [ '6772', '12906'],
      specs: [],
      selectedLifelines: [],
      selectedSentences: [],
    }
  },
  methods: {
    selectLifeline: function() {
      this.selectedConvictIds = [this.convictId];
      this.findLifeline();
    },
    findLifeline: function() {
      // this.selectedLifelines = this.selectedConvictIds.map(cid =>
      //         this.lifelines.filter((x) => x.ConvictId === cid));
      let sel = [];
      this.selectedConvictIds.forEach(cid => {
        sel.push(this.lifelines[cid]);
      });
      this.selectedLifelines = sel;

      this.specs = this.nominal.filter((x) => x.ConvictId === this.selectedConvictIds[0]);
    },
    selectVoyage: function(vid) {
      // const allConvictIds = this.lifelines.filter((x) => x.VoyageId === vid)
      //         .map((x) => x.ConvictId);
      // this.selectedConvictIds = allConvictIds.sort().filter(function(item, pos, ary) {
      //   return pos===0 || item != ary[pos - 1];
      // });
      this.selectedConvictIds = Array.from(this.voyages[vid]);
      this.findLifeline();
    },
    selectPerson: function(pid) {
      // const allConvictIds = this.lifelines.filter((x) => x.Person === pid)
      //         .map((x) => x.Person);
      // this.selectedConvictIds = allConvictIds.sort().filter(function(item, pos, ary) {
      //   return pos===0 || item != ary[pos - 1];
      // });
      this.selectedConvictIds = Array.from(this.persons[pid]);
      this.findLifeline();
    },
    selectConvict: function(cid) {
      this.selectedConvictIds = [cid];
      this.findLifeline();
    }
  },
  mounted() {
    //load data
    var loadLifelines = data => {
      for (var i = 0; i < data.length; i++) {
        data[i].newDate = new Date(data[i].Date);
        data[i].eventDescription = data[i].Event;

      }
      //sets on voyages and persons for network structure:
      this.lifelines = {};
      this.voyages = {};
      this.persons = {};
      data.forEach((d) => {
        if (d.ConvictId !== "") {
          if (!(d.ConvictId in this.lifelines)) {
            this.lifelines[d.ConvictId] = { "nominal_data" : null, "events" : [] , "sentences" : [], "confinement" : []}
          }
          // this.lifelines[d.ConvictId].push(d);
          // June 21: temporarily take out death event from lifeline, instead added death year in the description
          if (d.Source !== 'DeathsBurialsV4') {
            this.lifelines[d.ConvictId]["events"].push(d);
          }

          if (d.VoyageId !== '') {
            if (!(d.VoyageId in this.voyages)) {
              this.voyages[d.VoyageId] = new Set();
            }
            this.voyages[d.VoyageId].add(d.ConvictId);
          }
          if (d.Person!== '') {
            if (!(d.Person in this.persons)) {
              this.persons[d.Person] = new Set();
            }
            this.persons[d.Person].add(d.ConvictId);
          }
        }

      });



      // this.selectedLifelines = this.events.filter((x) => x.ConvictId === this.selectedConvictIds[0]);
      // this.selectedLifelines = this.selectedConvictIds.map(cid =>
      //         this.lifelines.filter((x) => x.ConvictId === cid));
        d3.csv(process.env.BASE_URL + "nominal.csv").then(loadNominal).catch(console.log.bind(console));
    }

    var loadNominal = data => {
      this.nominal = data;
      // mapping given name, family name and convictid for search filter
      this.given_to_family_name = {};
      this.family_to_given_name = {};
      this.nominal.forEach((d) => {
          if (d.ConvictId in this.lifelines) {
              this.lifelines[d.ConvictId]["nominal_data"] = d;
          }

        if (!(d.GivenNames in this.given_to_family_name)) {
          this.given_to_family_name[d.GivenNames] = {};
        }
        if (!(d.FamilyName in this.given_to_family_name[d.GivenNames])) {
          this.given_to_family_name[d.GivenNames][d.FamilyName] = [];
        }
        this.given_to_family_name[d.GivenNames][d.FamilyName].push(d.ConvictId);

        // { "Anna" : { "Smith" : [1,2,3], "Jones" : [4,5,6] } }

        if (!(d.FamilyName in this.family_to_given_name)) {
          this.family_to_given_name[d.FamilyName] = {};
        }
        if (!(d.GivenNames in this.family_to_given_name[d.FamilyName])) {
          this.family_to_given_name[d.FamilyName][d.GivenNames] = [];
        }
        this.family_to_given_name[d.FamilyName][d.GivenNames].push(d.ConvictId);

        // { "Smith" : { "Anna" : [1,2,3], "Maria" : [4,5,6] } }
      });



      d3.csv(process.env.BASE_URL + "colonialSentences.csv").then(loadSentences).catch(console.log.bind(console));
    }

    var loadSentences = data => {
      for (var i = 0; i < data.length; i++) {
        if (data[i].alternativeStartdate !== "") {
          data[i].startDate = data[i].alternativeStartdate;
        } else {
          data[i].startDate = data[i].Date;
        }
        data[i].cleanStartDate = new Date(data[i].startDate);
        data[i].cleanEndDate = new Date(data[i].Enddate);
        data[i].numericCode = +data[i].Code;
      }

      data.forEach((d) => {
        if (d.ConvictId in this.lifelines) {
          if (factory.has(d.numericCode)){
            this.lifelines[d.ConvictId]["sentences"].push(d);
          }
          if (confinement.has(d.numericCode)){
            this.lifelines[d.ConvictId]["confinement"].push(d);
          }

        }


      });

      let sel = [];
      this.selectedConvictIds.forEach(cid => {
        sel.push(this.lifelines[cid]);
      });
      this.selectedLifelines = sel;

      // console.log("lll", this.lifelines, this.selectedLifelines);
      this.specs = data.filter((x) => x.ConvictId === this.selectedConvictIds[0]);

    }



    d3.csv(process.env.BASE_URL + "lifelines.csv").then(loadLifelines);

  }
}
</script>

<style>
#app {
  background-color: ivory;
}



</style>
