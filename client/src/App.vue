<template>
  <div class="page">
    <div class="search">
      <span class="searchBox">
        <span class="glyphicon glyphicon-search"></span>
        <input
          class="search-text"
          placeholder="Search"
          id="title"
          required
          name="title"
          v-model="content.title"
          @keypress="search"
        />
      </span>
      
    </div>
    <div class="notes">
      <div class="notes__item">
        <input
          class="notes__item--create"
          placeholder="Take a note"
          id="todo"
          required
          name="todo"
          v-model="content.todo"
          v-on:keyup.enter="addTodo"
        />
        <span @click="addTodo" class="cusor">+</span>
      </div>
      <div 
        class="notes__item"
        v-for="(Todo, index) in Todos"
        :key="index"
      >
        <div v-if="content.title?Todo['description'].indexOf(content.title) >= 0?true:false:true">
          <label v-bind:class="[Todo.done ? checked : added]">
            {{Todo.description}}
            <input type="checkbox" @click="updateTodo( Todo._id, Todo.description)">
            <span class="checkmark" />
          </label>
          <span class="notes__item--time"> {{ updateMinutes(Todo.createdAt) }} </span>
          <span class="notes__item--remove cusor" @click="removeTodo( Todo._id)"> &times; </span>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from '@vue/composition-api';
import axios from 'axios';
import Jsona from 'jsona';

export default defineComponent({
  
  name: 'App',
  setup() {
    return {
      message: 'Todo list should be here',
    };
  },
  created() {
    setInterval(this.getNow, 1000);
  },
  data(){
    return {
      checked: "container notes__item--checked",
      added: "container notes__item--added",
      content: {
        id:null,
        todo:"",
        title:"",
        updated:"",
      },
      timestamp:"",
      options: {
        headers: {
          'Accept': 'application/json',
          'Content-Type': 'application/json',
        }
      },
      Todos: [],
      url: 'http://localhost:3000/api/v1/todo',
    }
  },
  
  mounted() {
    this.retrieve();
    let style = document.createElement('link');
    style.type = "text/css";
    style.rel = "stylesheet";
    style.href = 'https://maxcdn.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css';
    document.head.appendChild(style);
  },
  methods:{
    addTodo(){
      var data = {
        description: this.content.todo,
       };
      this.content.todo = "";
      axios.post(this.url, data, this.options)
      .then(response => {
        
        this.Todos = response.data;
      });
    },
    getNow: function() {
        const today = new Date();
        const n = today.valueOf();
        const date = today.getFullYear()+'-'+(today.getMonth()+1)+'-'+today.getDate();
        const time = today.getHours() + ":" + today.getMinutes() + ":" + today.getSeconds();
        const dateTime = date +' '+ time;
        this.timestamp = dateTime;
    },

    updateMinutes(date: string){
        console.log(date);
        const createdAt = new Date(date);
        const current = new Date();
        const convertFrom = createdAt.valueOf();
        const convertTo = current.valueOf();

        const temp = convertTo - convertFrom;
        let rlt = temp/60000;
        rlt = Math.round(rlt);

        let hours = 0;
        let minutes = 0;
        let result = "";
        if(rlt > 60){
          hours = Math.floor(rlt / 60);
          minutes = rlt%60;
        }else{
          minutes = rlt;
        }

        if(hours > 0) result = hours + " hours " + minutes + " minutes ago.";
        else result = minutes + " minutes ago.";
        return result;
    },

    removeTodo(id: string){
      var data = {
        id: id,
       };

      axios.delete(`${this.url}/${id}`)
      .then(response => {
        this.Todos = response.data;
      });
    },

    updateTodo(id: string, text: string){
      this.content.todo == "" ? this.content.todo = text : this.content.todo;  
      var data = {
        description: this.content.todo,
        done: true
       };
       this.content.todo = "";
      axios.put(`${this.url}/${id}`, data, this.options)
      .then(response => {
        this.Todos = response.data;
      });
    },
    search (){
      this.Todos.map((todo, index)=>{
      })
    },
    retrieve(){
      axios.get(this.url, this.options)
        .then(response => {
          this.Todos = response.data;
      });
    },
  }
});
</script>


<style lang="scss">
.todo-app {
  text-align: center;
}
$white: #fff;
$var_border: #C6C6C6;
$c_grey: #A8A8A8;
$c_lightGrey: #AEAEAE;
$background: #E5E5E5;
$c_green: #5DC2AF;
body {
  margin: 0;
}
.cusor{
  cursor: pointer;
}

.cusor:hover{
  color:tomato;
}

.cusor:active{
  color:chocolate;
}
.search{
  text-align: center;
  margin-bottom: 10px;
}
.searchBox{
  background: #cccccc78;
  border-radius: 13px;
  padding: 7px;
  padding-left: 12px;
}

span.searchBox span{
  color: #00000040;
}
.search-text{
    width: 45rem;
    height: 30px;
    padding-left: 10px;
    border-radius: 10px;
    border: none;
    outline: none;
    background: transparent;
}

.search-text:focus{
  border-color: #1222ff1c;
}

.page {
  background-color: $background;
  width: 100vw;
  height: 100vh;
  padding-top: 50px;
  justify-content: center;
  align-items: center
}
.notes {
  position: relative;
  margin:auto;
  width: 50rem;
  height: auto;
  background-color: $white;
  border: 1px solid rgba(0, 0, 0, 0.17);
  box-sizing: border-box;
  box-shadow: 2px 1px 7px rgba(0, 0, 0, 0.25);
  border-radius: 4px;
}
.notes {
  &__item {
    position: relative;
    display: flex;
    flex-direction: row;
    justify-content: flex-start;
    align-items: baseline;
    border-bottom: 0.0625rem solid $var_border;
    padding: 0 1rem;
    font-size: 1rem;
    font-family: Roboto;
    font-style: normal;
    font-weight: 200;
    &:hover {
      .notes__item--remove {
        display: block;
      }
    }
    &--remove {
      position: absolute;
      font-size: 1rem;
      right: 1rem;
      top: 1rem;
      display: none;
      transform: rotate(90deg);
    }
    &--checked {
      text-decoration: line-through;
      color: $c_lightGrey;
    }
    &--create {
      width: 100%;
      border: none;
      margin: 1rem 0;
      color: $c_grey;
      line-height: 1.1875rem;
      font-weight: 400;
    }
    &--create:focus {
      outline: none;
    }
    &--time {
      margin-left: 1rem;
      font-weight: 200;
      font-size: 0.625rem;
      line-height: 0.7325rem;
    }
     .container {
      // display: block;
      position: relative;
      padding-left: 2.1875rem;
      margin: 0.75rem 0;
      cursor: pointer;
       input {
         position: absolute;
         opacity: 0;
         cursor: pointer;
         height: 0;
         width: 0;
       }
        .checkmark {
          position: absolute;
          top: -0.1875rem;
          left: 0;
          height: 1.5625rem;
          width: 1.5625rem;
          background-color: $white;
          border:0.0625rem solid $c_green;
          border-radius: 3.125rem;
        }
        .checkmark:after {
          content: "";
          position: absolute;
          display: none;
        }
      input:checked ~ .checkmark:after {
        display: block;
       }
       .checkmark:after {
          left: 0.5625rem;
          top: 0.3125rem;
          width: 0.3125rem;
          height: 0.625rem;
          border: solid $c_green;
          border-width: 0 0.1875rem 0.1875rem 0;
          -webkit-transform: rotate(45deg);
          -ms-transform: rotate(45deg);
          transform: rotate(45deg);
        }
    }
  }
}
</style>
