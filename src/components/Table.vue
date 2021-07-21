<template>
<div>
 <v-data-table
      :headers="headers"
      :items="bitcoinInfo"
      :hide-default-footer="true"
    >
      <template #item.thirtyDaysDiff="{ item }">
        <td>{{ calculateThirtyDayDifference(item) }}%</td>
      </template>
      <template #item.sevenDaysDifference="{ item }">
       <td>{{ calculateThirtyDayDifference(item) }}%</td>
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

       
        calculateThirtyDayDifference(item) {

         let calculatedPercent = 100 * Math.abs((item['7d'] - item['30d']) / ((item['7d'] + item['30d']) / 2));
      return Math.max(Math.round(calculatedPercent * 10) / 10, 2.8).toFixed(2);
       },

       calculateSevenDayDifference(item) {

         let calculatedPercent = 100 * Math.abs((item['24h'] - item['7d']) / ((item['24h'] + item['7d']) / 2));
      return Math.max(Math.round(calculatedPercent * 10) / 10, 2.8).toFixed(2);
       }
      },
        
      mounted() {
        this.getBitcoinData()
      }
  }

</script>