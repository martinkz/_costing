<template>
  <v-container>
    <v-card>
      <v-toolbar color="cyan" dark flat>

        <v-toolbar-title>Dashboard</v-toolbar-title>

        <template v-slot:extension>
          <v-tabs v-model="tab" align-with-title>
            <v-tabs-slider color="cyan darken-3"></v-tabs-slider>

            <v-tab v-for="item in items2" :key="item">
              {{ item }}
            </v-tab>
          </v-tabs>
        </template>
      </v-toolbar>

      <v-tabs-items v-model="tab">
        <v-tab-item v-for="item in items2" :key="item">
          <v-card flat>
            <!-- <v-card-text v-text="text"></v-card-text> -->
            <v-container>
              <v-select :items="brands" filled label="Radio Brand" @change="selectChange($event)" v-model="currentBrand"></v-select>
              <v-select :items="regions" filled label="Region" @change="selectChange($event)" v-model="currentRegion"></v-select>
              <v-select :items="regions" filled label="Multiple Regions" multiple></v-select>
            </v-container>
            <v-container class="summary" v-if="getDataForCurrentRegion">
              <h2>Summary for  {{ getDataForCurrentRegion['Heart Regions'] }}</h2>
              <h3>Monthly Home Page Views: {{ getDataForCurrentRegion['Monthly Home Page Views'] }}</h3> 
              <h3>Daily Home Page Views: {{ getDataForCurrentRegion['Daily Home Page Views'] }}</h3> 
              <h3>Daily Cost: {{ getDataForCurrentRegion['Daily Cost'] }}</h3> 
              <h3>7 day cost: {{ getDataForCurrentRegion['7 day cost'] }}</h3> 
            </v-container>
          </v-card>
        </v-tab-item>
      </v-tabs-items>
    </v-card>

  </v-container>
</template>

<script>
import costData from '../data/costData.js';
  export default {
    // name: 'HelloWorld',

    data: () => ({
        currentBrand: undefined,
        currentRegion: undefined,
        brands: ['Heart'],
        brandData: costData,
        tab: null,
        items2: [ 'Pages', ],
        // text: 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.',
    }),

    computed: {
        regions() {
          return this.brandData[this.currentBrand] ? this.brandData[this.currentBrand].map((item)=>item['Heart Regions']) : [];
        },
        getDataForCurrentRegion() {
          return this.currentBrand && this.currentRegion ? this.brandData[this.currentBrand].find(el => el['Heart Regions'] === this.currentRegion) : undefined;
        }
    },

    methods: {
      selectChange(evt){
        console.log(evt);
      },
    }
  }
</script>

<style scoped>
  /* .summary {
    max-width: 550px;
    margin: 0 auto;
  } */
</style>