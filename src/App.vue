


<script>
import Card from './components/Card.vue'
import { toRaw } from 'vue';

import VueTailwindPagination from '@ocrv/vue-tailwind-pagination'

import '@ocrv/vue-tailwind-pagination/styles'
export default {
  name: 'app',
  components: { Card, VueTailwindPagination },
  data() {
    return {
      currentPage: 1,
      perPage: 20,
      total: 0,
      searchtext: '',
      apis: [],
      searchByCategory: [],
      categories: [],
      searcharray: [],
      currentItems: null,
      pageCount: 0,
      itemOffset: 0,


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
        this.pageCount = Math.ceil(data.entries.length / 20)
      })
      .catch(err => console.log(err))


  },

  methods: {

    onPageClick(page) {
      const newoffset = ((page-1) * 20) % this.searchByCategory.length
      console.log(newoffset);
      this.itemOffset = newoffset;
      const endoffset = newoffset + 20;
      // const categories = this.searchByCategory
      // const rawObjectOrArray = categories.slice(newoffset, endoffset);
      console.log(this.searchByCategory.slice(newoffset, endoffset));
      this.currentItems = this.searchByCategory.slice(newoffset, endoffset);
      this.pageCount = Math.ceil(this.searchByCategory.length / 20)
      this.currentPage = page
      window.scrollTo(0,0);
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
      if (event.target.value == "allCategories") {
        this.searchByCategory = this.apis;
        this.currentItems = this.apis.slice(0, 20)
        this.pageCount = Math.ceil(this.apis.length / 20)

      } else {
        const newApiList = this.apis.filter((api) => {
          return api.Category.toLowerCase() === event.target.value.toLowerCase();
        });
      // this.currentItems = newApiList.slice(newoffset, endOffset);
       // this.searchByCategory = newApiList;
       // const rawObjectOrArray = toRaw(newApiList)
        this.currentItems = newApiList.slice(0, 20);
        this.searchByCategory = newApiList
        this.pageCount = Math.ceil(newApiList.length / 20)
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
      <div :style="{ padding: '24px', minHeight: '380px' }">

        <a-row v-if="currentItems" :xs="{ order: 1 }" :sm="{ order: 2 }" :md="{ order: 4 }" :lg="{ order: 6 }">
          <div v-for="api in currentItems" :key="api.API">

            <a-card :title="api['API']" size="small" :hoverable="true" :style="{width: '250px' , margin: '15px'}">
              <template #extra>
                <a :href="api['Link']">More</a>
              </template>
              <p>{{api["API"]}}</p>
              <p>Category : {{api["Category"]}}</p>
              <p>Description :{{api["Description"]}} </p>

            </a-card>
          </div>

        </a-row>

        <a-row v-else type="flex">
          <!-- <Card :currentItems="currentItems" :parentMsg= "parentMsg" /> -->
        </a-row>
        <div>
          <VueTailwindPagination :current="this.currentPage" :total="this.searchByCategory.length" :per-page="perPage"
            @page-changed="onPageClick" />
          <!-- curret :  page number  -->
          <!-- totla : nb of records  -->
          <!-- per-page : records per page 20 -->
        </div>
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
p {
  text-overflow: ellipsis;
  overflow: hidden;
  white-space: nowrap;
}
</style>
