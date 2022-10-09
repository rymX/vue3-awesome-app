<script>
export default{
  props :{currentItems : [] , 'loading': Boolean}
  ,
  created() {
    // props are exposed on `this`
    console.log(this.currentItems)
  }
}

</script>

<template>

  <div v-if="loading">
    <a-card  :loading="loading" title="Card title"  size="small" :hoverable="true" style="width: 280px">
      <template #extra>
        <a href="#">More</a>
      </template>
    <p></p>
    <p>Card content</p>
    <p>Card content</p>
  </a-card>
  </div>
  <div v-else>
    <a-card v-for="api in currentItems" :key="api.API" title="Card title"  size="small" :hoverable="true" style="width: 280px" >
      <template #extra>
        <a href="#">More</a>
      </template>
    <p>{{currentItems.length}}</p>
    <p></p>
    <p>Card content</p>
  </a-card>
  </div>

</template>
