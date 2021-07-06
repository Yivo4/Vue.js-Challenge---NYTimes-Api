<template>
  <div class="container">
    <div class="card">
      <div class="heading">
        <h4 class="title">NYTimes Books</h4>
        <h5 class="subTitle">Pick a Category</h5>
      </div>
      <div class="content">
        <ul class="categories" id="categories">
          <li v-for="category in categoryList.slice(0,10)" :key="category.list_name"><button class="btn" v-on:click="chouse(category.list_name_encoded, category.list_name)">{{category.list_name}}</button></li>
        </ul>
      </div>
      <div class="search" v-if="showOn">    
        <input type="text" class="form-control" v-model="search" v-bind:placeholder="category"/>
      </div>
      <div class="content">
        <ul v-if="search">
          <li class="searchResult" v-for="book in searchQuery" :key="book.title">
              <form action="https://google.com/search">
                <input type="hidden" name="q" v-bind:value="book.title">
                <input type="hidden" name="q" v-bind:value="book.author">
                <button type="submit" class="btnResult">
                  <div class="cardBook">
                    <h3 class="bookTitle">{{book.title}}</h3>
                    <hr>
                    <p class="description">{{book.description}}</p>             
                    <p class="author">{{book.author}}</p>
                  </div>
                </button>
              </form>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios"
const url = "https://api.nytimes.com/svc/books/v3/lists"
const token = "wMrIxYjKdpTQq76wy7ngPAG1OD0VJy8j"

export default {
  name: "Books",
  data(){
    return{
      categoryList: [],
      search: "",
      bookList:[],
      showOn: false,
      category:"",
    }
  },

  mounted(){
    this.getAll();    
    },

  methods:{ 
    getAll(){ 
      axios.get(`${url}/names.json?api-key=${token}`)
        .then(response =>{
          this.categoryList = response.data.results
        })
        .catch(e => console.log(e)) 
    },
    chouse(list_name_encoded, list_name){
      this.bookList = []
      this.showOn = true
      axios.get(`${url}/current/${list_name_encoded}.json?api-key=${token}`)
        .then(response =>{
          let booksResult = response.data.results.books
          this.category= `Search by title... in ${list_name} category.`
          this.bookList.push(booksResult)         
        })
        .catch(e => console.log(e)) 
    },
    
  },
  computed: {
    searchQuery:function(){
      if(!this.bookList[0]){
        return false
      }else{
          return this.bookList[0].filter((book)=>{
            return book.title.toLowerCase().match(this.search.toLowerCase())
      })     
      }
    },
    activate(){
      var header = document.getElementById("categories");
      var btns = header.getElementsByClassName("btn");
      for (var i = 0; i < btns.length; i++) {
        btns[i].addEventListener("click", function() {
        var current = document.getElementsByClassName("active");
        current[0].className = current[0].className.replace(" active", "");
        this.className += " active";
        });
      }
    }
  }
}

</script>

<style scoped lang="scss">
.container {
  z-index: 1;
  margin: 36px auto;
  max-width: 826px;
  background-color: white;
}

.card {
  box-shadow: 0 2px 3px rgba(10, 10, 10, 0.1), 0 0 0 1px rgba(10, 10, 10, 0.1);
  padding: 14px;
  margin-top: 10px;
  margin-bottom: 10px;
  text-align: center;
}

.list {
  > li {
    &:not(:last-child) {
      margin-bottom: 18px;
    }
    > a {
      color: #0a5b8c;
      display: block;
      margin-bottom: 6px;
    }

    > span {
      color: rgba(#3b4242, 0.7);
      font-size: 12px;
    }
  }
}

.btn {
  color: #fff;
  cursor: pointer;
  background-color: #117a8b;
  border: 1px solid transparent;
  padding: 6px 12px;
  border-radius: 6px;
  transition: all 0.1s ease-in;
  &:hover {
    background-color: #138496;
    border-color: #117a8b;
  }
}

.heading {
  margin-bottom: 12px;

  .title {
    font-size: 18px;
    font-weight: 600;
  }
  .subTitle {
    margin-top: 10px;
    margin-left: 5px;
    font-size: 16px;
    font-weight: 500;
  }
}

.search {
  margin-bottom: 24px;
  margin-top: 24px;
  .form-control {
    background-color: transparent;
    border: none;
    border-bottom: 1px solid #ced4da;
    border-radius: 0;
    outline: 0;
    box-shadow: none;
    padding: 6px 0;
    width: 100%;
    font-size: 15px;
  }
}

.bookTitle {
  font-size: 18px;
  font-weight: 600;
}

.categories {
  display:flex;
  flex-wrap: wrap;
}

.categories>li{
  padding: 5px;
}

.author, .description {
  margin-top: 15px;
  font-size: 17px;
  font-weight: 400;
}

.cardBook {
  padding: 14px;
  margin-top: 10px;
  margin-bottom: 10px;
  text-align: center;
  font-family: "Gill Sans", sans-serif;
}

.btnResult {
  border: solid 1px orange;
  background-color: white;
  cursor: pointer;
  margin-bottom: 15px;
  width: 100%;
}

.author{
  font-family: 'cursive';
  font-size: 19px;
  margin-left: 60%;
}
</style>
