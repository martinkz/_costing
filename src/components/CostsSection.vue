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
              <!-- <v-select :items="regions" filled label="Region" @change="selectChange($event)" v-model="currentRegion"></v-select> -->
              <v-select :items="regions" filled label="Multiple Regions" multiple v-model="currentRegions"></v-select>
            </v-container>
            <v-container class="summary" v-if="getDataForCurrentRegions">
              <h2>Summary for the selected regions</h2>
              <h3>Monthly Home Page Views: {{ getDataForCurrentRegions['Monthly Home Page Views'] }}</h3> 
              <h3>Daily Home Page Views: {{ getDataForCurrentRegions['Daily Home Page Views'] }}</h3> 
              <h3>Daily Cost: £{{ getDataForCurrentRegions['Daily Cost'] }}</h3> 
              <h3>7 day cost: £{{ getDataForCurrentRegions['7 day cost'] }}</h3> 
              <h3 v-if="getDataForCurrentRegions['UK Monthly full site views']">UK Monthly full site views: {{ getDataForCurrentRegions['UK Monthly full site views'] }}</h3> 
              <h3 v-if="getDataForCurrentRegions['UK Monthly users']">UK Monthly users: {{ getDataForCurrentRegions['UK Monthly users'] }}</h3> 
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
        // currentRegion: undefined,
        currentRegions: [],
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
        // getDataForCurrentRegion() {
        //   return this.currentBrand && this.currentRegion ? this.brandData[this.currentBrand].find(el => el['Heart Regions'] === this.currentRegion) : undefined;
        // },
        getDataForCurrentRegions() {
          if(this.currentRegions.length > 0) {
            let combinedData = [];
            if(this.currentBrand && this.currentRegions.length > 0) {
              this.currentRegions.forEach((region) => {
                combinedData.push(this.brandData[this.currentBrand].find(el => el['Heart Regions'] === region));
              });
            }
            return combinedData.reduce((prev, current) => {
              return { 
                'Monthly Home Page Views': prev['Monthly Home Page Views']*1 + current['Monthly Home Page Views']*1,
                'Daily Home Page Views': prev['Daily Home Page Views']*1 + current['Daily Home Page Views']*1,
                'Daily Cost': prev['Daily Cost']*1 + current['Daily Cost']*1,
                '7 day cost': prev['7 day cost']*1 + current['7 day cost']*1, 
                }  
            });
          }
        }
    },

    watch: {
      currentBrand() {
        if(this.currentBrand && this.currentRegions.length === 0) this.currentRegions.push("NETWORK");
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