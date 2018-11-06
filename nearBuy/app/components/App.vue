
<template>
    <Page>
        <ActionBar class="actionbar">
          <StackLayout orientation="horizontal" >
            <Image src="res://icon" width="40" height="40" id="logo"/>
            <Label text="NearBuy" fontSize="24" id="nearbuy" />
          </StackLayout>
        </ActionBar>
        <WrapLayout>
            <!-- <SearchBar hint="Search hint" :text="searchPhrase" @textChange="onTextChanged" @submit="onSubmit" /> -->
            <SearchBar hint="What are you looking for today?" v-model="searchQuery" @submit="onButtonTap" />
            <ListView  for="product in searchedProds" @itemTap="onProductTap">
            <v-template>
            <!-- Shows the list item label in the default color and style. -->
            <Label :text="product.name" />
            </v-template>
            </ListView>

            <TabView :selectedIndex="selectedIndex" v-model="selectedIndex" @selectedIndexChange="onTabTap">
                <TabViewItem title="" iconSource="res://icon">
                    <Label text="" />
                </TabViewItem>
                <TabViewItem title="Tab 2" iconSource="res://search_icon">
                    <Label text="" />
                </TabViewItem>
                <TabViewItem title="Tab 3" iconSource="res://drawable-mdpi/icon.png">
                    <Label text="" />
                </TabViewItem>
                
            </TabView>       

        </WrapLayout>
    </Page>
</template>


<script>
//Importing the map component
import Map from "./map";
import { sep } from "path";
import { login } from "nativescript-plugin-firebase";
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
    onTabTap() {
      console.log(this.$data.selectedIndex.value);
    },
    onButtonTap() {
      //console.log(this.$data.textFieldValue);
      console.log("you just did onSubmit");
      //let rawInput = this.$data.textFieldValue;
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
    onProductTap(event) {
      console.log(event.item.name);
      this.$navigateTo(Map, { props: { barcode: event.item.barcode } });
    }
  },
  data() {
    return {
      selectedIndex: 0,
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
ActionBar {
  background-color: #6202ee;
  color: #ffffff;
}

.message {
  vertical-align: center;
  text-align: center;
  font-size: 20;
  color: #333333;
}

#icon, #nearbuy {
  margin-right: 80%;
  width:100%;
}
TabView {
  margin-top: 90%;
  transform: translateY(-100%);
}
</style>
