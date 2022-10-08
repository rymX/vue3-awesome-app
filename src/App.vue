


<script>
import Card from './components/Card.vue'

export default {
  name: 'app',
  components: { Card },
  data() {
    return {
      searchtext: '',
      test: 'hello world ',
      apis: [],
      searchByCategory: [],
      categories: [],
      searcharray :[]
    }
  },
  mounted() {
    fetch('https://api.publicapis.org/categories')
      .then((res) => res.json())
      .then(data => {
        this.categories = data.categories;
      })
      .catch(err => console.log(err))

    fetch('https://api.publicapis.org/entries')
      .then((res) => res.json())
      .then(data => {
        this.apis = data.entries
        this.searchByCategory = data.entries
        console.log(data.entries);
      })
      .catch(err => console.log(err))
  },

  methods: {
    onhandleSearch(event) {

      if (event.target.value !== "") {
        const newApiList = this.searchByCategory.filter((api) => {
          return api.API.toLowerCase().includes(event.target.value.toLowerCase());
        });
        if(newApiList.length == 0){
          console.log("no search result");
          // noti
        }
        this.searcharray = newApiList
      }
      else if (event.target.value == ""){
        this.searcharray =[];
      }
    }
    ,
    onhandlSelectCategory(event) {

      console.log(event.target.value);

      if (event.target.value == "allCategories") {
        this.searchByCategory = this.apis;
      } else {
        const newApiList = this.apis.filter((api) => {
          return api.Category.toLowerCase() === event.target.value.toLowerCase();
        });
        this.searchByCategory = newApiList;
      }
    }
  }
}

</script>

<template>
  <a-layout>
    <a-layout-header :style="{ position: 'fixed', zIndex: 1, width: '100%' , theme: 'dark' ,  mode: 'horizontal'  }">
      <a-space direction="horizontal">
        <input v-model="searchtext" type="text" placeholder="input text " @input="onhandleSearch"
          :style="{width: '200px' , height: '35px'}">

        <select @change="onhandlSelectCategory" :style="{width: '200px' , height: '35px'}">
          <option value="allCategories" selected>allcategories</option>
          <option v-for="category in categories" :key="category" :value="category">{{category}} </option>
        </select>


      </a-space>

    </a-layout-header>
    <a-layout-content :style="{ padding: '0 50px', marginTop: '64px' }">
      <a-breadcrumb :style="{ margin: '16px 0' }">
        <a-breadcrumb-item>Home</a-breadcrumb-item>
        <a-breadcrumb-item>{{searcharray.length}}</a-breadcrumb-item>
        <a-breadcrumb-item>{{searchByCategory.length}}</a-breadcrumb-item>
      </a-breadcrumb>
      <div v-if="searchByCategory.length" :style="{ background: '#fff', padding: '24px', minHeight: '380px' }" >
        <Card :data="test" :table="['name','rym']" />


      </div>
      <div v-else :style="{ background: '#fff', padding: '24px', minHeight: '380px' }" >
        <Card :data="test" :table="['no data','rym']" />


      </div>
    </a-layout-content>
    <a-layout-footer :style="{ textAlign: 'center' }">
      Created with Passion By FRADI Rym
    </a-layout-footer>
  </a-layout>
</template>






<style>
#components-layout-demo-fixed .logo {
  width: 120px;
  height: 31px;
  background: rgba(255, 255, 255, 0.2);
  margin: 16px 24px 16px 0;
  float: left;
}

.site-layout .site-layout-background {
  background: #fff;
}

[data-theme='dark'] .site-layout .site-layout-background {
  background: #141414;
}
</style>
