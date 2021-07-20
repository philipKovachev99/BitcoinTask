<template>
<div>
  <v-data-table
    :headers="headers"
    :items="bitcoinInfo"
    :hide-default-footer="true"
  ></v-data-table>
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
          { text: '30 Day Diff %', value: 'thirtyDaysDiff' },
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
            return {currency: key, ...response.data[key]}
          })
          this.bitcoinInfo = convertedCollection
      })) 
        },
      },
           
      mounted() {
        this.getBitcoinData()
      },
  }

</script>