<!--Frequently used command line stuff:-->
<!--tns run android --bundle-->
<!--tns build android --bundle-->
<!--tns platform remove android && tns platform add android-->
<template>
  <Page>
    <ActionBar class="actionbar">
          <StackLayout orientation="horizontal">
            <Image src="res://logo" width="40" height="40" id="logo" />
            <Label text="NearBuy" fontSize="24" id="nearbuy" />
          </StackLayout>
    </ActionBar>
      <TabView androidTabsPosition="bottom" selectedIndex="selectedIndex">
        <TabViewItem title="Tab 1" iconSource="res://search">
          <WrapLayout>
            <SearchBar hint="What are you looking for today?" v-model="searchQuery" @submit="onButtonTap" />
            <ListView  for="product in searchedProds" @itemTap="onProductTap">
            <v-template>
           <GridLayout class="list-group-item" rows="*" columns="auto, *">
            <!-- Shows the list item label in the default color and style. -->
            <Image row="0" col="0" :src="product.image" width="80" height="80"/>
            <Label :text="product.name" row="0" col="1" id="product" />
            <Label text="AVAILABLE" row="1" col="1" id="availability" />
            <Label text="at Zani's (500m)" row="1" col="2" id="place" />
            <Label text="10€" row="0" col="2" id="price" />
            </GridLayout> 
            </v-template>
            </ListView>
          </WrapLayout>
        </TabViewItem>
        <TabViewItem title="Tab 2" iconSource="res://map">
          <WrapLayout>            
            <StackLayout>
              <!-- Shows the list item label in the default color and style. -->
              <GridLayout class="list-group-item" rows="*" columns="auto, *">
              <!-- Shows the list item label in the default color and style. -->
              <Image row="0" col="0" :src="image" width="100" height="100"/>
              <Label :text="name" row="0" col="1" id="product" />
              <Label text="AVAILABLE" row="1" col="1" id="availability" />
              <Label text="at Zani's (500m)" row="1" col="2" id="place" />
              <Label text="10€" row="0" col="2" id="price" />
              </GridLayout> 
              <ContentView height="100%" width="100%" id="map">
                <Mapbox
                  accessToken="pk.eyJ1IjoiZW1pbHNhbGxlbSIsImEiOiJjam5rZ3BibnMwZjZmM3dwazhsM29pcWg2In0.Kn7kBXDI7niAIHvwS2iDMw"
                  mapStyle="traffic_day"
                  latitude="52.493997"
                  longitude="13.446464"
                  hideCompass="true"
                  zoomLevel="12"
                  showUserLocation="true"
                  disableZoom="false"
                  disableRotation="false"
                  disableScroll="false"
                  disableTilt="false"
                  @mapReady="onMapReady($event)">
                </Mapbox>
              </ContentView>
            </StackLayout>
          </WrapLayout>
        </TabViewItem>
        <TabViewItem title="Tab 3" iconSource="res://favorite">
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
      console.log("you just did onSubmit");
      this.$data.searchedProds = [];
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
      this.$navigateTo(Map, { props: { barcode: event.item.barcode, name:event.item.name, image:event.item.image} });
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

#search-icon{
  width:30;
  height:30;
  background-color: red;
}

#availability {
  margin-top: 50;
  margin-right: 58%;
  margin-bottom: 20;
  border: none;
  background-color: #27ae60;
  width: 20%;
  text-align: center;
  border-radius: 40%;
  color: #ffffff;
}
#icon,
#nearbuy {
  margin-right: 80%;
  width: 100%;
}
TabView {
}
#product {
  font-size: 22;
  margin-top: 10;
  padding-right: 80%;
}
.list-group-item {
  border: 5 solid black;
  z-index: 10;
}
#place {
  margin-top: 50;
  margin-left: 24%;
}
#price {
  margin-left: 58%;
  background-color: #e0e0e0;
  width: 12%;
  height: 20%;
  text-align: center;
  font-size: 22;
}
SearchBar{
  width: 100%
}
</style>
