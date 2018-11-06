
<template>
  <Page>
    <ActionBar title="nearBuy"></ActionBar>
      <TabView androidTabsPosition="bottom" selectedIndex="selectedIndex">
        <TabViewItem title="Tab 1" iconSource="res://icon">                
          <WrapLayout>
            <SearchBar hint="What are you looking for today?" v-model="searchQuery" @submit="onButtonTap" />
            <ListView  for="product in searchedProds" @itemTap="onProductTap">
            <v-template>
            <Label :text="product.name" />
            </v-template>
            </ListView>
          </WrapLayout>
        </TabViewItem>
        <TabViewItem title="Tab 2" iconSource="res://icon">
        <WrapLayout>
          <Label text="Content for Tab 2" />
        </WrapLayout>
        </TabViewItem>
        <TabViewItem title="Tab 3" iconSource="res://drawable-mdpi/icon.png">
        <WrapLayout>
          <Label text="Content for Tab 3" />
        </WrapLayout>
        </TabViewItem>
      </TabView>
  </Page>
</template>


<script>
//Importing the map component
import Map from "./map";
import { sep } from "path";
import { login } from 'nativescript-plugin-firebase';
let searchProductResults = [];
//Wiring firebase
const firebase = require("nativescript-plugin-firebase");

firebase
  .init({
    // Optionally pass in properties for database, authentication and cloud messaging,
    // see their respective docs.
  })
  .then(
    function(instance) {
      console.log("firebase.init done");
    },
    function(error) {
      console.log("firebase.init error: " + error);
    }
  );
console.log("i am app");
export default {
  methods: {
    onButtonTap() {
      //console.log(this.$data.textFieldValue);
      console.log('you just did onSubmit');
      this.$data.searchedProds = []
      let rawInput = this.$data.searchQuery;
      let vm = this;
      let promise;
      //Looping over collection and display/get all data in it
      var productsCollection = firebase.firestore.collection("products");

      //Splitting raw input into words
      let splitInput = rawInput.split(" ");

      //promise = new Promise(resolve => {

      //Looping over every searched word
      for (let word of splitInput) {
        console.log(word);

        //Looping over each found element
        productsCollection
          .where("tags", "array-contains", word)
          .get()
          .then(function(querySnapshot) {
            querySnapshot.forEach(function(doc) {
              // doc.data() is never undefined for query doc snapshots
              if (searchProductResults.length == 0) {
                vm.$data.searchedProds.push(doc.data());
                searchProductResults.push(doc.data());
              } else {
                let found = false;
                searchProductResults.forEach(function(product) {
                  if (product.barcode == doc.data().barcode) {
                    found = true;
                  }
                });
                if (!found) {
                  vm.$data.searchedProds.push(doc.data());
                  searchProductResults.push(doc.data());
                }
              }
            });
          })
          .catch(function(error) {
            console.log("Error getting documents: ", error);
          });
      }
      //console.log(this);
    },
    onProductTap(event){
        console.log(event.item.name);
        this.$navigateTo(Map, {props:{barcode: event.item.barcode}});
    }
  },
  data() {
    return {
      searchQuery: "",
      textFieldValue: "",
      searchedProds: []
    };
  },
  created() {
    console.log("created!");
  }
};
</script>

<style scoped>

/* Page{
  height: 100%;
} */
ActionBar {
  background-color: #6202EE;
  color: #ffffff;
}

TabView {
}
</style>
