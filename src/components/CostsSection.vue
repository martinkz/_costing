<template>
  <v-container class="app-container">
    <v-row>
      <v-col cols="12" lg="8">
        <!-- <v-container> -->
          <v-sheet color="white">
              <v-tabs height="65px" class="radio-tabs" v-model="radioBrandTab" background-color="grey darken-2" fixed-tabs dark>
                <v-tab v-for="(brand, idx) in radioBrands" :class="radioTabEnabled(brand)" :key="brand">
                  <img class="radio-logo" :src="'img/'+radioLogos[idx]" alt="">
                </v-tab>
              </v-tabs>
          </v-sheet>
          <v-container></v-container>
          <v-card>
            <v-sheet color="cyan" dark>
              <!-- <template v-slot:extension> -->
                <v-tabs class="content-tablist" v-model="tab" background-color="deep-purple" fixed-tabs>
                  <v-tab v-for="item in contentTypes" :key="item">
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
                    <v-select :items="availablePageRegionsForCurrentBrand" filled label="Regions" multiple v-model="selectedPageRegions[currentBrand]"></v-select>
                    <v-select :items="pageTypes" filled label="Page Type" v-model="currentPageType"></v-select>
                    <v-text-field label="Campaign Days" min="1" max="365" type="number" filled v-model="pageDays"/>
                  </v-container>
                </v-card>
              </v-tab-item>

              <v-tab-item>
                <v-container>
                  <h2>CRM</h2>
                  <v-select :items="availableCRM_RegionsForCurrentBrand" filled label="Regions" multiple v-model="selectedCRM_Regions[currentBrand]"></v-select>
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
        <!-- </v-container> -->
      </v-col>

      <v-col cols="12" lg="4">
        <v-container>
          <h2 class="calculator-main-title">Cost calculator</h2>
          <h3 v-if="videoNum > 0">
            Video cost: {{ cf(videoViews*50) }}
          </h3> 

          <table class="cart-table" v-if="Object.keys(getDisplayPageDataForAllBrands).length !== 0">
            <tr>
              <th>Page Summary</th>
              <th>{{ currentBrand }}</th>
              <th>All Brands</th>
            </tr>
            <tr>
              <td>Monthly Home Page Views</td>
              <td>{{ getDisplayPageData ? getDisplayPageData['Monthly Home Page Views'] : 0 }}</td>
              <td>{{ getDisplayPageDataForAllBrands['Monthly Home Page Views'] }}</td>
            </tr> 
            <tr>
              <td>Daily Home Page Views</td>
              <td>{{ getDisplayPageData ? getDisplayPageData['Daily Home Page Views'] : 0 }}</td>
              <td>{{ getDisplayPageDataForAllBrands['Daily Home Page Views'] }}</td>
            </tr> 
            <tr>
              <td>Daily Cost</td>
              <td>£{{ getDisplayPageData ? getDisplayPageData['Daily Cost'].toFixed(2) : 0 }}</td>
              <td>£{{ getDisplayPageDataForAllBrands['Daily Cost'].toFixed(2) }}</td>
            </tr> 
            <tr>
              <td>{{pageDays}} day cost</td>
              <td>£{{ getDisplayPageData ? (getDisplayPageData['Daily Cost'] * pageDays).toFixed(2) : 0 }}</td>
              <td>£{{ (getDisplayPageDataForAllBrands['Daily Cost'] * pageDays).toFixed(2) }}</td>
            </tr> 
            <tr v-if="getDisplayPageData ? getDisplayPageData['UK Monthly full site views'] : false">
              <td>UK Monthly full site views</td>
              <td>{{ getDisplayPageData ? getDisplayPageData['UK Monthly full site views'] : 0 }}</td>
              <td>{{ getDisplayPageDataForAllBrands['UK Monthly full site views'] }}</td>
            </tr> 
            <tr v-if="getDisplayPageData ? getDisplayPageData['UK Monthly users'] : false">
              <td>UK Monthly users</td>
              <td>{{ getDisplayPageData ? getDisplayPageData['UK Monthly users'] : 0 }}</td>
              <td>{{ getDisplayPageDataForAllBrands['UK Monthly users'] }}</td>
            </tr> 
          </table>
          <br>
          <table class="cart-table" v-if="Object.keys(getDisplayCRMDataForAllBrands).length !== 0">
            <tr>
              <th>CRM Summary</th>
              <th>{{ currentBrand }}</th>
              <th>All Brands</th>
            </tr>
            <tr>
              <td>Volumes</td>
              <td>{{ getDisplayCRMData ? getDisplayCRMData['Volumes'] : 0 }}</td>
              <td>{{ getDisplayCRMDataForAllBrands['Volumes'] }}</td>
            </tr>
            <tr>
              <td>Avg OR Reach</td>
              <td>{{ getDisplayCRMData ? getDisplayCRMData['Avg OR Reach'] : 0 }}</td>
              <td>{{ getDisplayCRMDataForAllBrands['Avg OR Reach'] }}</td>
            </tr>
            <tr>
              <td>Solus</td>
              <td>{{ cf(getDisplayCRMData ? getDisplayCRMData['Solus'] : 0) }}</td>
              <td>{{ cf(getDisplayCRMDataForAllBrands['Solus']) }}</td>
            </tr>
            <tr>
              <td>Newsletter</td>
              <td>{{ cf(getDisplayCRMData ? getDisplayCRMData['Newsletter'] : 0) }}</td>
              <td>{{ cf(getDisplayCRMDataForAllBrands['Newsletter']) }}</td>
            </tr>
            <tr>
              <td>Banner</td>
              <td>{{ cf(getDisplayCRMData ? getDisplayCRMData['Banner'] : 0) }}</td>
              <td>{{ cf(getDisplayCRMDataForAllBrands['Banner']) }}</td>
            </tr>
          </table>
        </v-container>
      </v-col>
    </v-row>
  </v-container>

</template>

<script>
  const radioBrands = ['Heart', 'Capital', 'Smooth', 'CapitalXTRA', 'Classic', 'RadioX', 'LBC', 'Gold'];
  const radioLogos = ['Heart_White_Nostrap.svg', 'Capital_White.png', 'smooth-white.png', 'CapitalXtra_White.png', 'Classic_white_min.svg', 'RadioX_Landscape_White.svg', 'LBC_White_Strap.svg', 'Gold.svg'];

  export default {

    data: () => ({
        selectedPageRegions: Object.fromEntries(radioBrands.map(k => [k, []])),
        selectedCRM_Regions: Object.fromEntries(radioBrands.map(k => [k, []])),
        pageTypes: ["Enhanced Competition Page", "Budget Competition Page"],
        currentPageType: undefined,
        pageData: {},
        CRMData: {},
        tab: null,
        radioBrandTab: 0,
        radioBrands: radioBrands,
        radioLogos: radioLogos,
        pageDays: 7,
        videoNum: 0,
        videoViews: 0,
        videoNumList: [{'text': 'No video', 'value': 0},{'text': '1', 'value': 1},{'text': '2', 'value': 2},{'text': '3', 'value': 3},{'text': '4', 'value': 4},{'text': '5', 'value': 5}],
        contentTypes: [ 'Page', 'CRM', "Video" ],
    }),

    computed: {
        currentBrand() {
          return this.radioBrands[this.radioBrandTab];
        },
        availablePageRegionsForCurrentBrand() {
          return this.pageData[this.currentBrand] ? this.pageData[this.currentBrand].map((item)=>item['Regions']) : [];
        },
        availableCRM_RegionsForCurrentBrand() {
          return this.CRMData[this.currentBrand] ? this.CRMData[this.currentBrand].map((item)=>item['Regions']) : [];
        },
        getDisplayPageData() {
          if(this.selectedPageRegions[this.currentBrand].length > 0) {
            let combinedData = this.getCombinedData(this.selectedPageRegions, this.pageData, this.currentBrand);
            let page_fields = ['Monthly Home Page Views', 'Daily Home Page Views', 'Daily Cost', '7 day cost', 'UK Monthly full site views', 'UK Monthly users'];
            return this.calcTotalForDataProps(combinedData, page_fields);
          }
        },
        getDisplayPageDataForAllBrands() {
            let page_fields = ['Monthly Home Page Views', 'Daily Home Page Views', 'Daily Cost', '7 day cost', 'UK Monthly full site views', 'UK Monthly users'];
            return this.calcTotalForDataProps(this.getCombinedData(this.selectedPageRegions, this.pageData), page_fields);
        },
        getDisplayCRMData() {
          if(this.selectedCRM_Regions[this.currentBrand].length > 0) {
            let combinedData = this.getCombinedData(this.selectedCRM_Regions, this.CRMData, this.currentBrand);
            let crm_fields = ['Volumes', 'Avg OR Reach', 'Solus', 'Banner', 'Newsletter']
            return this.calcTotalForDataProps(combinedData, crm_fields);
          }
        },
        getDisplayCRMDataForAllBrands() {
            let crm_fields = ['Volumes', 'Avg OR Reach', 'Solus', 'Banner', 'Newsletter'];
            return this.calcTotalForDataProps(this.getCombinedData(this.selectedCRM_Regions, this.CRMData), crm_fields);
        },
    },

    watch: {

    },

    methods: {
      getCombinedData(selectedData, allData, brand) {
        let combinedData = [];
        if(brand === undefined) {
          Object.entries(selectedData).forEach(([theBrand, regions]) => {
            regions.forEach((region) => {
              combinedData.push(allData[theBrand].find(el => el['Regions'] === region));
            });
          });
        } else {
          if(selectedData[brand].length > 0) {
            selectedData[brand].forEach((region) => {
              combinedData.push(allData[brand].find(el => el['Regions'] === region));
            });
          }
        }
        return combinedData;
      },
      calcTotalForDataProps(data, props) {
        let summedData = {}
        for (const dataObj of data) {
          for (const prop of props) {
            summedData[prop] = (summedData[prop] || 0) + dataObj[prop];
          }
        };
        return summedData;
      },
      radioTabEnabled(brand) {
        return this.selectedPageRegions[brand].length !== 0 || this.selectedCRM_Regions[brand].length !== 0 ? 'radioTabEnabled' : '';
      },
      selectChange(evt){
        // console.log(evt);
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
      const CRMSheetNames = 'ranges=CRM_'+radioBrands.join('&ranges=CRM_');
      fetch(`https://sheets.googleapis.com/v4/spreadsheets/1lAnd6q6d_FbsCwj0YckfjQonUAn4uTMyUSGT9uThkiw/values:batchGet?${PageSheetNames}&${CRMSheetNames}&key=AIzaSyC7UqzaeVkFn9wXzUok5JyFMFLe6rehu7g`)
      .then(response => response.json())
      .then(data => {
          data['valueRanges'].forEach( sheet => {
            let brand = sheet['range'].substring(sheet['range'].indexOf("_")+1, sheet['range'].indexOf("!"));
            let type = sheet['range'].substring(0, sheet['range'].indexOf("_"));
            let dataSet;
            if(type === 'Page'){ 
              dataSet = this.pageData;
            } else if(type === 'CRM') {
              dataSet = this.CRMData;
            } else {
              console.log("Unknown sheet type");
            }
            this.$set(dataSet, brand, sheet['values'].map( el => {
              let obj = {};
              sheet['values'][0].forEach((item,idx) => { obj[item] = (isNaN(el[idx]) ? el[idx] : Number(el[idx])) })
              return obj;
            }));
            dataSet[brand].shift();
          });
          Object.keys(this.selectedPageRegions).forEach(el => (this.selectedPageRegions[el].push("NETWORK")));
          Object.keys(this.selectedCRM_Regions).forEach(el => (this.selectedCRM_Regions[el].push("NETWORK")));
      });
    },

  }
</script>

<style lang="scss" scoped>

  .app-container {
    max-width: 1440px;
  }

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
      background-color: #673ab7;
    }
  }

  .radio-logo {
    max-width: 90px;
    max-height: 36px;
  }

  .video-slider {
    margin-top: 2rem;
  }

  .calculator-main-title {
    margin: 0 0 20px;
    text-transform: uppercase;
  }

  .cart-table {
    border-collapse: collapse;
    
    td, th {
      text-align: left;
    }
    tr {
      // background: #e9e9e9;
      border-bottom: 1px solid #c8c8c8;
    }
    td:nth-child(1), th:nth-child(1) {
      width: 210px;
      max-width: 100%;
    }
    td:nth-child(2), th:nth-child(2) {
      width: 100px;
    }
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

  .radio-tabs {
    .v-tab--active {
      background: #3b0f89 !important;
    }
    // .v-tabs-bar {
    //   height: 75px !important;
    // }
  }
</style>