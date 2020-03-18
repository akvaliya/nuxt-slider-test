<template>
  <v-layout column justify-center align-center>
    <v-flex xs12 sm8 md6>
      <div v-for="(prty,index) in properties" :key="prty.property_id">
        <CProperty :pdata="prty" :pindex="index"></CProperty>
      </div>
    </v-flex>
  </v-layout>
</template>

<script>
import axios from '~/plugins/axios'
import CProperty from '~/components/CProperty.vue'

export default {
  components: {
    CProperty
  },
  data: () => ({
    properties: []
  }),
  created() {
    this.getData();
  },
  methods: {
    getData() {
      axios.get("api.json").then((res) => {
        if (res.data && res.data.properties) {
          this.properties = res.data.properties;
        }
      })      
    }
  }
}
</script>
