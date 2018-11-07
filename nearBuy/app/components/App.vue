
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
            <SearchBar hint="Search.." v-model="searchQuery" @submit="onButtonTap" width="100%"/>
            <ListView  for="product in searchedProds" @itemTap="onProductTap">
            <v-template>
            <GridLayout class="list-group-item" rows="*" columns="auto, *">
            <!-- Shows the list item label in the default color and style. -->
            <Image row="0" col="0" src="https://firebasestorage.googleapis.com/v0/b/nearbuy-3d083.appspot.com/o/products%2F29741939-1.png?alt=media&token=af304319-6823-4005-aa0f-7c14501763cc" width="100" height="100"/>
            <Label :text="product.name" row="0" col="1" id="product" />
            <Label text="AVAILABLE" row="1" col="1" id="availability" />
            <Label text="at Zani's (500m)" row="1" col="2" id="place" />
            <Label text="10€" row="0" col="2" id="price" />

            </GridLayout> 
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
  margin-top: 90%;
  transform: translateY(-100%);
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
