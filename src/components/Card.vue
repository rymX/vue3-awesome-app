<script>

import { toRaw } from 'vue';
export default {
  props: {
    currentItems: null,
    parentMsg: Number
  }
  ,
  data() {
    currentapis: []
  },
 

  created() {
    const rawObjectOrArray = toRaw(this.currentItems)
    // props are exposed on `this`
    this.currentapis = rawObjectOrArray
    console.log(rawObjectOrArray)
  }
}

</script>

<template>



  <!-- <a-card-grid  :loading="loading" title="Card title"  size="small" :hoverable="true" style="width: 25%; text-align: center">
      <template #extra>
        <a href="#">More</a>
      </template>
    <p></p>
    <p>Card content</p>
    <p>Card content</p>
  </a-card-grid> -->
  <div v-for="api in currentapis" :key="api.API">

    <a-card :title="api['API']" size="small" :hoverable="true" :style="{width: '250px' , margin: '15px'}">
      <template #extra>
        <a :href="api['Link']">More</a>
      </template>
      <p>{{parentMsg}}</p>
      <p>{{api["API"]}}</p>
      <p>Category : {{api["Category"]}}</p>
      <p>Description :{{api["Description"]}} </p>

    </a-card>
  </div>

</template>

<style scoped>
p {
  text-overflow: ellipsis;
  overflow: hidden;
  white-space: nowrap;
}
</style>
