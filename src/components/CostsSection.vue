<template>
  <v-container class="calculator-wrap">
    <v-sheet color="white">
      <!-- <v-container> -->
        <!-- <v-select :items="brands" outlined label="Radio brand" @change="selectChange($event)" v-model="currentBrand"></v-select> -->
        <v-tabs height="65px" class="radio-tabs" v-model="radioBrandTab" background-color="grey darken-2" fixed-tabs dark>
          <v-tab v-for="(brand, idx) in radioBrands" @change="selectChange($event)" :class="radioTabEnabled(brand)" :key="brand">
            <img class="radio-logo" :src="'img/'+radioLogos[idx]" alt="">
          </v-tab>
        </v-tabs>
      <!-- </v-container> -->
    </v-sheet>
    <v-container></v-container>
    <v-card>
      <v-sheet color="cyan" dark>
        <!-- <template v-slot:extension> -->
          <v-tabs class="content-tablist" v-model="tab" background-color="deep-purple" fixed-tabs>
            <v-tab v-for="item in items2" :key="item">
              {{ item }}
            </v-tab>
          </v-tabs>
        <!-- </template> -->
      </v-sheet>

      <v-tabs-items class="content-tabs" v-model="tab">
        <v-tab-item>
          <v-card flat>
            <!-- <v-card-text v-text="text"></v-card-text> -->
            <v-container>
              <h2>Competition page</h2>
              <v-select :items="regions" filled label="Regions" multiple v-model="currentRegions[currentBrand]"></v-select>
              <v-select :items="pageTypes" filled label="Page Type" v-model="currentPageType"></v-select>
              <v-text-field label="Campaign Days" min="1" max="365" type="number" filled v-model="pageDays"/>
            </v-container>
          </v-card>
        </v-tab-item>

        <v-tab-item>
          <v-container>
            <h2>CRM</h2>
            <v-select :items="currentCRM_Regions" filled label="Regions" multiple v-model="selectedCRM_Regions[currentBrand]"></v-select>
          </v-container>
        </v-tab-item>
        
        <v-tab-item>
          <v-container>
            <h2>Video</h2>
            <!-- <v-text-field label="Videos" min="1" max="100" type="number" filled v-model="videoNum"/> -->
            <v-select :items="videoNumList" filled label="Number of videos" v-model="videoNum"></v-select>
            <v-slider class="video-slider" v-model="videoViews" :readonly="videoNum==0" label="Video views / cost" thumb-color="#ff5252" thumb-label="always" :min="videoNum*200" :max="videoNum > 0 ? 4000 : 0" step="10" thumb-size="75">
              <template v-slot:thumb-label="item">
                {{ item.value < 1000 ? item.value+'k' : item.value/1000+'M' }} <br> {{ cf(videoViews*50) }}
              </template>
            </v-slider>
          </v-container>
        </v-tab-item>
      </v-tabs-items>
    </v-card>

    <v-container class="summary">
      <h2>Summary for {{ currentBrand }}</h2>
      <hr>
      <div v-if="getDataForCurrentRegions">
        <h3>Monthly Home Page Views: {{ getDataForCurrentRegions['Monthly Home Page Views'] }}</h3> 
        <h3>Daily Home Page Views: {{ getDataForCurrentRegions['Daily Home Page Views'] }}</h3> 
        <h3>Daily Cost: £{{ getDataForCurrentRegions['Daily Cost'].toFixed(2) }}</h3> 
        <h3>{{pageDays}} day cost: £{{ (getDataForCurrentRegions['Daily Cost'] * pageDays).toFixed(2) }}</h3> 
        <h3 v-if="getDataForCurrentRegions['UK Monthly full site views']">UK Monthly full site views: {{ getDataForCurrentRegions['UK Monthly full site views'] }}</h3> 
        <h3 v-if="getDataForCurrentRegions['UK Monthly users']">UK Monthly users: {{ getDataForCurrentRegions['UK Monthly users'] }}</h3> 
        <hr>
      </div>
      <div v-if="getDisplayDataForSelectedCRMRegions">
        <h3>Volumes: {{ getDisplayDataForSelectedCRMRegions['Volumes'] }}</h3> 
        <h3>Avg OR Reach: {{ getDisplayDataForSelectedCRMRegions['Avg OR Reach'] }}</h3> 
        <h3>Solus: {{ cf(getDisplayDataForSelectedCRMRegions['Solus']) }}</h3>
        <h3>Newsletter: {{ cf(getDisplayDataForSelectedCRMRegions['Newsletter']) }}</h3>
        <h3>Banner: {{ cf(getDisplayDataForSelectedCRMRegions['Banner']) }}</h3>
        <hr>
      </div>
      <h2>Total</h2>
      <h3 v-if="videoNum > 0">Video cost: {{ cf(videoViews*50) }}</h3> 
    </v-container>
  </v-container>
</template>

<script>
  // import costData from '../data/costData.js';
  let sheetData = {};
  let CRM_Data = {};
  // let brandList = [];
  let pageTypes = ["Enhanced Competition Page", "Budget Competition Page"];
  let radioBrands = ['Heart', 'Capital', 'Smooth', 'CapitalXTRA', 'Classic', 'RadioX', 'LBC', 'Gold'];
  let radioLogos = ['Heart_White_Nostrap.svg', 'Capital_White.png', 'smooth-white.png', 'CapitalXtra_White.png', 'Classic_white_min.svg', 'RadioX_Landscape_White.svg', 'LBC_White_Strap.svg', 'Gold.svg'];

  export default {

    data: () => ({
        currentRegions: Object.fromEntries(radioBrands.map(k => [k, []])),
        selectedCRM_Regions: Object.fromEntries(radioBrands.map(k => [k, []])),
        pageTypes: pageTypes,
        currentPageType: undefined,
        // brands: brandList,
        brandData: sheetData,
        CRMData: CRM_Data,
        tab: null,
        radioBrandTab: 0,
        radioBrands: radioBrands,
        radioLogos: radioLogos,
        pageDays: 7,
        videoNum: 0,
        videoViews: 0,
        videoNumList: [{'text': 'No video', 'value': 0},{'text': '1', 'value': 1},{'text': '2', 'value': 2},{'text': '3', 'value': 3},{'text': '4', 'value': 4},{'text': '5', 'value': 5}],
        items2: [ 'Page', 'CRM', "Video" ],
    }),

    computed: {
        currentBrand() {
          return this.radioBrands[this.radioBrandTab];
        },
        regions() {
          return this.brandData[this.currentBrand] ? this.brandData[this.currentBrand].map((item)=>item['Regions']) : [];
        },
        currentCRM_Regions() {
          return this.CRMData[this.currentBrand] ? this.CRMData[this.currentBrand].map((item)=>item['Regions']) : [];
        },
        getDataForCurrentRegions() {
          if(this.currentRegions[this.currentBrand].length > 0) {
            let combinedData = [];
            if(this.currentBrand && this.currentRegions[this.currentBrand].length > 0) {
              // console.log(this.brandData,this.currentBrand, this.brandData['Heart']);
              this.currentRegions[this.currentBrand].forEach((region) => {
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
        },
        getDisplayDataForSelectedCRMRegions() {
          if(this.selectedCRM_Regions[this.currentBrand].length > 0) {
            let combinedData = this.getCombinedData(this.currentBrand, this.selectedCRM_Regions, this.CRMData);
            // console.log(combinedData);
            return combinedData.reduce((prev, current) => {
              return { 
                'Volumes': prev['Volumes'] + current['Volumes'],
                'Avg OR Reach': prev['Avg OR Reach'] + current['Avg OR Reach'],
                'Solus': prev['Solus'] + current['Solus'],
                'Newsletter': prev['Newsletter'] + current['Newsletter'],
                'Banner': prev['Banner'] + current['Banner'], 
                }  
            });
          }
        },
    },

    watch: {

    },

    methods: {
      getCombinedData(forBrand, selectedDataObj, dataObj) {
        let combinedData = [];
        if(forBrand === undefined) {
          Object.entries(selectedDataObj).forEach(([brand, regions]) => {
            regions.forEach((region) => {
              combinedData.push(dataObj[brand].find(el => el['Regions'] === region));
            });
          });
        } else {
          if(selectedDataObj[forBrand].length > 0) {
            selectedDataObj[forBrand].forEach((region) => {
              combinedData.push(dataObj[forBrand].find(el => el['Regions'] === region));
            });
          }
        }
        return combinedData;
      },
      radioTabEnabled(brand) {
        return this.currentRegions[brand].length !== 0 || this.selectedCRM_Regions[brand].length !== 0 ? 'radioTabEnabled' : '';
      },
      selectChange(evt){
        console.log(evt);
        console.log(this.getCombinedData(undefined, this.selectedCRM_Regions, this.CRMData));
      },
      cf(num) { // currency formatting function
        let formatter = new Intl.NumberFormat('en-US', {
          style: 'currency',
          currency: 'GBP',
          maximumFractionDigits: 0 // causes 2500.99 to be printed as £2,501
          //minimumFractionDigits: 0, // this suffices for whole numbers, but will print 2500.10 as £2,500.1
        });

        return formatter.format(num); /* £2,500 */
      }
    },

    created() {
      const PageSheetNames = 'ranges=Page_'+radioBrands.join('&ranges=Page_');
      fetch(`https://sheets.googleapis.com/v4/spreadsheets/1lAnd6q6d_FbsCwj0YckfjQonUAn4uTMyUSGT9uThkiw/values:batchGet?${PageSheetNames}&key=AIzaSyC7UqzaeVkFn9wXzUok5JyFMFLe6rehu7g`)
      .then(response => response.json())
      .then(data => {
          data['valueRanges'].forEach( sheet => {
            let brand = sheet['range'].substring(5, sheet['range'].indexOf("!"));
            this.$set(sheetData, brand, sheet['values'].map( el => {
              let obj = {};
              sheet['values'][0].forEach((item,idx) => { obj[item] = isNaN(el[idx]) ? el[idx] : Number(el[idx]) })
              return obj;
            }));
            sheetData[brand].shift();
          });
          // brandList.push(...Object.keys(sheetData));
          // Object.keys(this.currentRegions).forEach(el => (this.currentRegions[el].push("NETWORK")));
      });

      const CRMSheetNames = 'ranges=CRM_'+radioBrands.join('&ranges=CRM_');
      fetch(`https://sheets.googleapis.com/v4/spreadsheets/1lAnd6q6d_FbsCwj0YckfjQonUAn4uTMyUSGT9uThkiw/values:batchGet?${CRMSheetNames}&key=AIzaSyC7UqzaeVkFn9wXzUok5JyFMFLe6rehu7g`)
      .then(response => response.json())
      .then(data => {
          data['valueRanges'].forEach( sheet => {
            let brand = sheet['range'].substring(4, sheet['range'].indexOf("!"));
            this.$set(CRM_Data, brand, sheet['values'].map( el => {
              let obj = {};
              sheet['values'][0].forEach((item,idx) => { obj[item] = isNaN(el[idx]) ? el[idx] : Number(el[idx]) })
              return obj;
            }));
            CRM_Data[brand].shift();
          });
          // Object.keys(this.selectedCRM_Regions).forEach(el => (this.selectedCRM_Regions[el].push("NETWORK")));
        console.log(this.getCombinedData(undefined, this.selectedCRM_Regions, this.CRMData));
      });
      // this.videoViews = this.videoNum*200;
    },

  }
</script>

<style lang="scss" scoped>

  .calculator-wrap {
    max-width: 960px;
  }

  .content-tabs h2 {
    margin-bottom: 1rem;
  }

  .content-tablist .v-tab--active {
    background: #3b0f89;
  }

  .radio-tabs {
    .v-tab--active {
        background: #424242;
    }
    .radioTabEnabled {
      background-color: #3b0f89;
    }
  }

  .radio-logo {
    max-width: 90px;
    max-height: 36px;
  }

  .video-slider {
    margin-top: 2rem;
  }
</style>

<!-- Non-scoped styles, to overwrite Vuetfy defaults -->
<style lang="scss">
  .v-slider__thumb-label {
    border-radius: 9px !important;
    text-align: center;
    height: 45px !important;
    transform: translateY(-15%) translateY(-12px) translateX(-50%) !important;


    &:before {
      content: "";
      display: block;
      position: absolute;
      top: 100%;
      left: 50%;
      transform: translateX(-50%);
      width: 0; 
      height: 0; 
      border-left: 8px solid transparent;
      border-right: 8px solid transparent;
      border-top: 8px solid rgb(255, 82, 82);
    }

    > * {
      transform: none !important;
    }
  }

  // .radio-tabs {
  //   .v-tabs-bar {
  //     height: 75px !important;
  //   }
  // }
</style>