<template>
    <Page>
      <ActionBar title="TO DO LIST">
        <ActionItem text="ADD" @tap="onAddTap"></ActionItem>
        <ActionItem :text="filterButtonText" @tap="toggleFilter"></ActionItem>
      </ActionBar>
      <StackLayout>
        <GroceryList :items="items" :filterDone="filterDone" @updateItems="saveToLocalStorage"/>
      </StackLayout>
    </Page>
</template>

<script > 
  import GroceryList from "./GroceryList";
  import AddItem from './AddItem';
  import * as localstorage from 'nativescript-localstorage';


  export default {
    components: {GroceryList},
    data() {
      return {
        items: [],
        filterDone: false
      }
    },
    methods: {
      onAddTap() {
        const newId= new Date().getTime();
        this.$showModal(AddItem, {
          props: {
            id: newId
          }
        }).then( newItem => {
          if(newItem) {
            this.items.unshift(newItem);
            this.saveToLocalStorage();
          }
        })
      },
      toggleFilter() {
        this.filterDone = !this.filterDone;
      },
      saveToLocalStorage() {
          localStorage.setItem("data", JSON.stringify(this.items));
      }
    },
    created: function() {
        if (localStorage.getItem("data") !== null) {
            this.items = JSON.parse(localStorage.getItem("data"));
        }
    },
    computed: {
      filterButtonText: function() {
        if(this.filterDone){ return "SHOW ALL TASKS" }
        else{ return "ONLY NOT DONE" }
      }
    }
  }
</script>

<style scoped>
  ActionBar {
    background-color: rgb(228, 201, 201);
    color:black;
  }
</style>
