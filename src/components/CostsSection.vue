<template>
  <v-container class="calculator-wrap">
    <v-sheet color="white">
      <!-- <v-container> -->
        <!-- <v-select :items="brands" outlined label="Radio brand" @change="selectChange($event)" v-model="currentBrand"></v-select> -->
        <v-tabs height="65px" class="radio-tabs" v-model="radioBrandTab" background-color="deep-purple" fixed-tabs dark>
          <v-tab v-for="(item, idx) in radioBrands" :key="item">
            <img class="radio-logo" :src="'img/'+radioLogos[idx]" alt="">
          </v-tab>
        </v-tabs>
      <!-- </v-container> -->
    </v-sheet>
    <v-container></v-container>
    <v-card>
      <v-sheet color="cyan" dark>
        <!-- <template v-slot:extension> -->
          <v-tabs v-model="tab" background-color="deep-purple" fixed-tabs>
            <v-tab v-for="item in items2" :key="item">
              {{ item }}
            </v-tab>
          </v-tabs>
        <!-- </template> -->
      </v-sheet>

      <v-tabs-items class="tabs" v-model="tab">
        <v-tab-item>
          <v-card flat>
            <!-- <v-card-text v-text="text"></v-card-text> -->
            <v-container>
              <h2>Competition page</h2>
              <!-- <v-select :items="regions" filled label="Region" @change="selectChange($event)" v-model="currentRegion"></v-select> -->
              <v-select :items="regions" filled label="Regions" multiple v-model="currentRegions"></v-select>
              <v-select :items="pageTypes" filled label="Page Type" v-model="currentPageType"></v-select>
              <v-text-field label="Campaign Days" type="number" filled v-model="pageDays"/>
            </v-container>
          </v-card>
        </v-tab-item>

        <v-tab-item>
          <v-container>
            <h2>CRM</h2>
            <v-select :items="regions" filled label="Regions" multiple v-model="crmRegions"></v-select>
          </v-container>
        </v-tab-item>
        
        <v-tab-item>
          <v-container>
            <h2>Video</h2>
            <v-slider
              v-model="videoViews"
              label="Video views"
              thumb-color="#ff5252"
              thumb-label="always"
              max="10000"
              step="100"
              thumb-size="36"
            ></v-slider>
          </v-container>
        </v-tab-item>
      </v-tabs-items>
    </v-card>

    <v-container class="summary" v-if="getDataForCurrentRegions">
      <h2>Summary for the selected regions</h2>
      <h3>Monthly Home Page Views: {{ getDataForCurrentRegions['Monthly Home Page Views'] }}</h3> 
      <h3>Daily Home Page Views: {{ getDataForCurrentRegions['Daily Home Page Views'] }}</h3> 
      <h3>Daily Cost: £{{ getDataForCurrentRegions['Daily Cost'].toFixed(2) }}</h3> 
      <h3>7 day cost: £{{ getDataForCurrentRegions['7 day cost'].toFixed(2) }}</h3> 
      <h3 v-if="getDataForCurrentRegions['UK Monthly full site views']">UK Monthly full site views: {{ getDataForCurrentRegions['UK Monthly full site views'] }}</h3> 
      <h3 v-if="getDataForCurrentRegions['UK Monthly users']">UK Monthly users: {{ getDataForCurrentRegions['UK Monthly users'] }}</h3> 
    </v-container>
  </v-container>
</template>

<script>
  import costData from '../data/costData.js';
  let sheetData = {};
  let brandList = [];
  let pageTypes = ["Enhanced Competition Page", "Budget Competition Page"];
  let radioBrands = ['Heart', 'Capital', 'Smooth', 'CapitalXTRA', 'Classic', 'RadioX', 'LBC', 'Gold'];
  let radioLogos = ['Heart_White_Nostrap.svg', 'Capital_White.png', 'smooth-white.png', 'CapitalXtra_White.png', 'Classic_white_min.svg', 'RadioX_Landscape_White.svg', 'LBC_White_Strap.svg', 'Gold.svg'];

  export default {

    data: () => ({
        // currentBrand: 'Heart',
        // currentRegion: undefined,
        currentRegions: [],
        crmRegions: [],
        pageTypes: pageTypes,
        currentPageType: undefined,
        brands: brandList,
        brandData: sheetData,
        tab: null,
        radioBrandTab: 0,
        radioBrands: radioBrands,
        radioLogos: radioLogos,
        pageDays: 7,
        videoViews: 5000,
        items2: [ 'Pages', 'CRM', "Video" ],
    }),

    computed: {
        currentBrand() {
          return this.radioBrands[this.radioBrandTab];
        },
        regions() {
          return this.brandData[this.currentBrand] ? this.brandData[this.currentBrand].map((item)=>item['Regions']) : [];
        },
        // getDataForCurrentRegion() {
        //   return this.currentBrand && this.currentRegion ? this.brandData[this.currentBrand].find(el => el['Regions'] === this.currentRegion) : undefined;
        // },
        getDataForCurrentRegions() {
          if(this.currentRegions.length > 0) {
            let combinedData = [];
            if(this.currentBrand && this.currentRegions.length > 0) {
              // console.log(this.brandData,this.currentBrand, this.brandData['Heart']);
              this.currentRegions.forEach((region) => {
                combinedData.push(this.brandData[this.currentBrand].find(el => el['Regions'] === region));
              });
            }
            return combinedData.reduce((prev, current) => {
              return { 
                'Monthly Home Page Views': prev['Monthly Home Page Views'] + current['Monthly Home Page Views'],
                'Daily Home Page Views': prev['Daily Home Page Views'] + current['Daily Home Page Views'],
                'Daily Cost': prev['Daily Cost'] + current['Daily Cost'],
                '7 day cost': prev['7 day cost'] + current['7 day cost'], 
                }  
            });
          }
        }
    },

    watch: {
      currentBrand() {
        this.currentRegions.splice(0, this.currentRegions.length)
        this.currentRegions.push("NETWORK");
      },
    },

    methods: {
      selectChange(evt){
        console.log(evt);
      },
    },

    created() {
      // fetch('https://sheets.googleapis.com/v4/spreadsheets/1lAnd6q6d_FbsCwj0YckfjQonUAn4uTMyUSGT9uThkiw/values/Heart?key=AIzaSyC7UqzaeVkFn9wXzUok5JyFMFLe6rehu7g')
      fetch('https://sheets.googleapis.com/v4/spreadsheets/1lAnd6q6d_FbsCwj0YckfjQonUAn4uTMyUSGT9uThkiw/values:batchGet?ranges=Heart&ranges=Capital&ranges=Smooth&ranges=CapitalXTRA&ranges=Classic&ranges=RadioX&ranges=LBC&ranges=Gold&key=AIzaSyC7UqzaeVkFn9wXzUok5JyFMFLe6rehu7g')
      .then(response => response.json())
      .then(data => {
          data['valueRanges'].forEach( sheet => {
            let brand = sheet['range'].substring(0, sheet['range'].indexOf("!"));
            this.$set(sheetData, brand, sheet['values'].map( el => {
              let obj = {};
              sheet['values'][0].forEach((item,idx) => { obj[item] = isNaN(el[idx]) ? el[idx] : Number(el[idx]) })
              return obj;
            }));
            sheetData[brand].shift();
          });
          brandList.push(...Object.keys(sheetData));
          this.currentRegions.push("NETWORK");
          this.crmRegions.push("NETWORK");
      });
    },

  }
</script>

<style lang="scss" scoped>

  .calculator-wrap {
    max-width: 960px;
  }

  .tabs h2 {
    margin-bottom: 1rem;
  }

  .radio-tabs {
    // Doesn't work for some reason
    // .v-tabs-bar {
    //   height: 55px !important;
    // }
    
  }

  .v-tab--active {
      background: #3b0f89;
  }

  .radio-logo {
    max-width: 90px;
    max-height: 36px;
  }
</style>