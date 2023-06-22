<template>
  <div class="hello">
    <Header />
    <div class="dropdown-container">
          <div class="row">
            <div class="dropdown-col">
              <select v-model="selectedCountry.countryName" id="countryList" class="dropdown" @change="changeCountry(selectedCountry.countryName)">
                <option v-for="(item,index) in countryList" :key="index" :value="item.countryName">
                    {{item.countryName}}
                </option>
            </select>  
            </div>
            <div class="dropdown-col">
              <select v-model="selectedCategory" id="catList" class="dropdown">
                <option v-for="(item,index) in categoryList" :key="index" :value="item.categoryName">
                    {{item.categoryName}}
                </option>
            </select> 
            </div>
            <div class="dropdown-col">
              <input type="text" placeholder="Keywords (optional)" v-model="filterText" />
            </div>
            <div class="dropdown-buttons">
              <input type="submit" value="search" class="search-button" @click="submitQuery()"/>
            </div>
          </div>
    </div>
    <div class="api">
    <h1 v-if="items.length > 0"> {{items.length}} News Results </h1>
    <br />
    <div class="cards-container" v-for="item of items" v-bind:key="item.id">

      <div class="card-detail">
        <div class="card-detail-image">
          <img :alt="item.title" :src="item.urlToImage" width="100%" height="200">
        </div>
        <div class="card-detail-title">
          <h2>
            {{item.title}}
          </h2>
          <div class="blog-post-text">
            <p>{{ item.source.name }}</p>
          </div>
          <div class="card-detail-desc">
            <p>{{ item.description }}</p>
          </div>
          <div class="card-button">
            <a :href="item.url">Go to page</a>
          </div>
            </div>
            <div class="card-detail-date">
              <p>Published : {{ localizeDate(item.publishedAt) }}</p>
            </div>

            </div>


    </div>
  </div>

  </div>
</template>

<script>
import axios from 'axios'
import Header from './Header.vue'

export default {
  name: 'Dashboard',
  components: {
    Header
  },
  data() {
      return {
          items: [],
          selectedCountry: {id: '3', countryName: 'United States', countryCode: 'us'},
          categoryList : [{id: '1', categoryName: 'business'},
          {id: '1', categoryName: 'entertainment'},
          {id: '1', categoryName: 'general'},
          {id: '1', categoryName: 'health'},
          {id: '1', categoryName: 'science'},
          {id: '1', categoryName: 'sports'},
          {id: '1', categoryName: 'technology'}],
          selectedCategory: 'sports',
          selectedCountryCode: 'us',
          apiKey: '89685d1ac8ba4974954f651b43d6977c',
          countryList : [{id: '1', countryName: 'Argentina', countryCode: 'ar'},
          {id: '2', countryName: 'Greece', countryCode: 'gr'},
          {id: '3', countryName: 'United States', countryCode: 'us'},
          {id: '3', countryName: 'Malaysia', countryCode: 'my'}]
      }
  },
  methods: {
    getInfo(framedurl) {
      this.items = [];
      axios({
        method: "GET",

        url: framedurl,
          // "https://cors-anywhere.herokuapp.com/https://newsapi.org/v2/top-headlines?country=us&category=entertainment&apiKey=89685d1ac8ba4974954f651b43d6977c",
          //"https://cors-anywhere.herokuapp.com/https://newsapi.org/v2/sources?apiKey=89685d1ac8ba4974954f651b43d6977c",

        dataType: "json",

        headers: {
          "X-Requested-With": "XMLHttpRequest",
          "Access-Control-Allow-Origin": "*"
        }
      }).then(res => {
        /* eslint-disable no-console */
        console.log(res.data);
        this.items = res.data.articles;
        //this.countryList = res.data.sources;
      });
    },
    localizeDate(date) {
      if (!date || !date.includes('-')) return date
      const [yyyy, mm, dd] = date.split('-')
      return `${mm}/${dd.slice(0,2)}/${yyyy}`;
    },
    changeCountry (item) {
      var newAObj = this.countryList.filter(function (el) {
        return el.countryName == item 
      });
      this.selectedCountryCode = newAObj[0].countryCode;
    },
    submitQuery () {
      const frameurl = `https://cors-anywhere.herokuapp.com/https://newsapi.org/v2/top-headlines?country=${this.selectedCountryCode}&category=${this.selectedCategory}&apiKey=${this.apiKey}`;
      this.getInfo(frameurl);
    }
  },
  mounted () {
    const frameurl = this.submitQuery();
    this.getInfo(frameurl);
  }
}
</script>
<style lang="css">
.cards-container {
  display: block;
  margin: 0 auto;
  max-width: 1200px;
}
.card-detail {
  float:left;
    box-sizing: border-box;
      max-width: 350px;
      margin-right: 50px;
      margin-bottom: 50px;
      border: 1px solid #f1f2f3;
  }
  .card-detail-image {
    width: 100%;
    height: 200px;
    background: #888;
  }
  .card-detail-title {
    display: flex;
    flex: 1;
    flex-direction: column;
    padding: 1.125rem;
  }
  .card-detail-title h2 {
    font-size: 1.5rem;
    margin-top: 0;
    margin-bottom: 0.625rem;
    overflow: hidden;
    text-overflow: ellipsis;
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
  }
  .card-button a {
    align-items: center;
    background-color: #666;
    color: #fff;
    border: 0.063rem solid #fff;
    border-radius: 1.125rem;
    cursor: pointer;
    display: flex;
    padding: 0.5rem 1rem;
    text-align: center;
    text-decoration: none;
    width: 5rem;
  }
  .card-detail-date {
    border-top: 1px solid #f1f2f3;
    padding: 0.625rem;
    height: 50px;
    background: #f8fdff;
  }
  .card-detail-desc {
    font-weight: 400;
    font-size: 1rem;
    min-height: 100px;
  }
  .card-detail-desc p {
    overflow: hidden;
    text-overflow: ellipsis;
    display: -webkit-box;
    -webkit-line-clamp: 3;
    -webkit-box-orient: vertical;
  }
  .dropdown-container {
    display: flex;
    align-items: center;
    justify-content: center;
    background: #eee;
  }
  .dropdown-container .row {
    display: flex;
    margin: 15px 0;
  }
  .dropdown-container .row .dropdown-col {
    margin-right: 20px;
  }
  .dropdown-container .row .search-button {
    background: #1871ae;
    height: 30px;
    width: 100px;
    color: #fff;
    cursor: pointer;
    margin-top: 9px;
    font-size: 1rem;
  }
  .dropdown-container .row .dropdown-col input {
    height: 44px;
    border: 0;
    padding: 0 10px;
  }
</style>