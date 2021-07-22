<template>
<div>
 <v-data-table
      :headers="headers"
      :items="rowsToDisplay"
      :hide-default-footer="true"
      class="primary"
    >
      <template #item.thirtyDaysDiff="{ item }">
            <span :class="item.thirtyDaysDiffClass">{{ item.thirtyDaysDiff }}%</span>
      </template>
      <template #item.sevenDaysDifference="{ item }">
            <span :class="item.sevenDaysDiffClass">{{ item.sevenDaysDiff }}%</span>
      </template>
    </v-data-table>
</div>

</template>

<script>
import axios from 'axios';

  export default {
    data () {
      return {
        bitcoinInfo: [],
        headers: [
          {
            text: 'Currency',
            align: 'start',
            value: 'currency',
          },
          
          { text: '30 Days Ago', value: '30d' },
          { text: '30 Day Diff %', value: 'thirtyDaysDiff'},
          { text: '7 Days Ago', value: '7d' },
          { text: '7 Day Diff %', value: 'sevenDaysDifference' },
          { text: '24 Hours Ago', value: '24h' },
        ],
      }
    },

    methods: {
      
      getBitcoinData() {
        axios
      .get('data.json')
      .then((response => {

      var convertedCollection =  Object.keys(response.data).map(key => {
            return {currency: key, thirtyDaysDiff: 0, sevenDaysDifference: 0,  ...response.data[key]}
          })
          this.bitcoinInfo = convertedCollection
          
      }))
      .catch(err => console.log(err))
        },

       
      calculateDifference(a, b) {
      let calculatedPercent = 100 * Math.abs((a - b) / ((a + b) / 2));
      return Math.max(Math.round(calculatedPercent * 10) / 10, 2.8).toFixed(2);
    },

       getDiffClass(a, b) {
      return a > b ? 'positive' : a < b ? 'negative' : ''
     },


       calculateSevenDayDifference(item) {

         let calculatedPercent = 100 * Math.abs((item['24h'] - item['7d']) / ((item['24h'] + item['7d']) / 2));
      return Math.max(Math.round(calculatedPercent * 10) / 10, 2.8).toFixed(2);
       }
      },
         computed: {
    rowsToDisplay() {
      return Object.keys(this.bitcoinInfo)
        .map(key => {
          return {
            currency: key,
            ...this.bitcoinInfo[key]
          }
        }).map((item) => ({
          ...item,
          thirtyDaysDiff: this.calculateDifference(item['7d'], item['30d']),
          thirtyDaysDiffClass: this.getDiffClass(item['7d'], item['30d']),
          sevenDaysDiff: this.calculateDifference(item['24h'], item['7d']),
          sevenDaysDiffClass: this.getDiffClass(item['24h'], item['7d']),
        }))
    }
  },
      mounted() {
        this.getBitcoinData()
      }
  }

</script>


<style>
.negative {
  color: red;
}

.positive {
  color: green;
}
</style>