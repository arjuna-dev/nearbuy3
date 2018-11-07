
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

/* Page{
  height: 100%;
} */
ActionBar {
  background-color: #6202ee;
  color: #ffffff;
}


#availability{
  margin-top:60;
  margin-right:58%;
  margin-bottom: 20;
  border: none;
  background-color: #27AE60;
  width: 20%;
  text-align: center;
  border-radius: 40%;
  color: #ffffff;
      
}
#icon, #nearbuy {
  margin-right: 80%;
  width:100%;
}
TabView {
}
#product {
  font-size:25;
  margin-top: 10;
  padding-right: 80%;
}
.list-group-item{
  border: 5 solid black ;

}
#place {
  margin-top: 60;
  margin-left:20%;
}
#price {
  margin-left:50%;
  background-color: #E0E0E0;  
  width: 12%;
  height: 20%;
  text-align: center;
  font-size: 25;

}

</style>
