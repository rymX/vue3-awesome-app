<script>
import { notification } from 'ant-design-vue';
import VueTailwindPagination from '@ocrv/vue-tailwind-pagination'
import '@ocrv/vue-tailwind-pagination/styles'

export default {
  name: 'app',
  components: { VueTailwindPagination },
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
      const newoffset = ((page - 1) * 20) % this.searchByCategory.length
      this.itemOffset = newoffset;
      const endoffset = newoffset + 20;
      this.currentItems = this.searchByCategory.slice(newoffset, endoffset);
      this.pageCount = Math.ceil(this.searchByCategory.length / 20)
      this.currentPage = page
      window.scrollTo(0, 0);
    }
    ,
    onhandleSearch(event) {

      if (event.target.value !== "") {
        const result = this.searchByCategory.filter((api) => {
          return api.API.toLowerCase().includes(event.target.value.toLowerCase());
        });
        this.currentItems = result.slice(0, 20);
        this.searcharray = result
        if (result.length === 0){
          notification.open({
            message: "No Search Results ",
          description: `Sorry, but nothing matched your search terms " ${event.target.value} " in selected category `
        });
        this.currentPage = 0
        }
      }
      else if (event.target.value == "") {
        this.searcharray = []
      }
    }
    ,
    onhandlSelectCategory(event) {
      if (event.target.value == "allCategories") {
        this.searchByCategory = this.apis;
        this.currentItems = this.apis.slice(0, 20)
        this.pageCount = Math.ceil(this.apis.length / 20)
        this.currentPage =1;
      } else {
        const newApiList = this.apis.filter((api) => {
          return api.Category.toLowerCase() === event.target.value.toLowerCase();
        });
        this.currentItems = newApiList.slice(0, 20);
        this.searchByCategory = newApiList
        this.pageCount = Math.ceil(newApiList.length / 20)
        window.scrollTo(0, 0);
      }
    }
  }
}
</script>

<template>
  <a-layout>
    <a-layout-header :style="{ position: 'fixed', zIndex: 1, width: '100%' , theme: 'dark' ,  mode: 'horizontal'  }">
      <a-space class="form" direction="horizontal">
        <input v-model="searchtext" type="text" placeholder="input search text " @input="onhandleSearch">

        <select @change="onhandlSelectCategory">
          <option value="allCategories" selected>allcategories</option>
          <option v-for="category in categories" :key="category" :value="category">{{category}} </option>
        </select>
      </a-space>
    </a-layout-header>
    <a-layout-content :style="{ padding: '0 50px', marginTop: '64px' }">
      <a-breadcrumb :style="{ margin: '16px 0' }">
        <a-breadcrumb-item>{{ searcharray.length ? searcharray.length : searchByCategory.length}} apis
        </a-breadcrumb-item>
        <a-breadcrumb-item>{{pageCount}} page </a-breadcrumb-item>
      </a-breadcrumb>
      <div :style="{ padding: '24px', minHeight: '380px' }">
        <a-row v-if="currentItems" :xs="{ order: 1 }" :sm="{ order: 2 }" :md="{ order: 4 }" :lg="{ order: 6 }">
          <div v-for="api in currentItems" :key="api.Link">
            <a-card :title="api['API']" size="small" :hoverable="true" :style="{width: '250px' , margin: '15px'}">
              <template #extra>
                <a :href="api['Link']">More</a>
              </template>
             <p> <b>{{api["API"]}}</b></p> 
              <p>Category : {{api["Category"]}}</p>
              <p>Description :{{api["Description"]}} </p>
            </a-card>
          </div>
        </a-row>
        <a-row v-else type="flex" :xs="{ order: 1 }" :sm="{ order: 2 }" :md="{ order: 4 }" :lg="{ order: 6 }">
          <div v-for="i in 20" :key="i">
            <a-card :loading=true title="Card title" size="small" :style="{width: '250px' , margin: '15px'}">
              <b>card title</b>
              <p>card content</p>
              <p>card content</p>
            </a-card>
          </div>
        </a-row>
        <div>
          <VueTailwindPagination :current="currentPage"
            :total="searcharray.length ? searcharray.length : searchByCategory.length" :per-page="perPage"
            @page-changed="onPageClick" />
        </div>
      </div>
    </a-layout-content>
    <a-layout-footer :style="{ textAlign: 'center' }">
      Created with Passion By FRADI Rym
    </a-layout-footer>
  </a-layout>
</template>
