


<script>
import Card from './components/Card.vue'
import VueTailwindPagination from '@ocrv/vue-tailwind-pagination'
import '@ocrv/vue-tailwind-pagination/styles'
export default {
  name: 'app',
  components: { Card  , VueTailwindPagination},
  data() {
    return {


      currentPage: 1,
      perPage: 20,
      total: 0,
      searchtext: '',
      test: 'hello world ',
      apis: [],
      searchByCategory: [],
      categories: [],
      searcharray: [],

      currentItems : null,
      pageCount : 0,
      itemOffset : 0 


      // curret : page number 
      // total : nb of records
      // perpage : 20 
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

        const itemOffset = 0;
      const endOffset = itemOffset + 20;
      this.currentItems = data.entries.slice(itemOffset, endOffset)
      this.pageCount =Math.ceil(data.entries.length / 20)
      console.log(this.currentItems);
      })
      .catch(err => console.log(err))


  },

  methods: {

    onPageClick (page) {
      console.log(page)
    }
    ,
    onhandleSearch(event) {

      if (event.target.value !== "") {
        const newApiList = this.searchByCategory.filter((api) => {
          return api.API.toLowerCase().includes(event.target.value.toLowerCase());
        });
        if (newApiList.length == 0) {
          console.log("no search result");
          // noti
        }
        this.searcharray = newApiList
      }
      else if (event.target.value == "") {
        this.searcharray = [];
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

      <div v-if="searchByCategory.length" :style="{ background: '#fff', padding: '24px', minHeight: '380px' }">
        <Card :currentItems="this.currentItems" />
      </div>
      <div v-else :style="{ background: '#fff', padding: '24px', minHeight: '380px' }">
        <Card :loading="true" />
      </div>

      <div>
  <VueTailwindPagination
          :current="current" 
          :total="this.searchByCategory.length"
          :per-page="perPage"
          @page-changed="onPageClick"
/>
<!-- curret :  page number  -->
<!-- totla : nb of records  -->
<!-- per-page : records per page 20 -->
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
