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

      <TabViewItem title="searchTab" iconSource="res://search">
        <WrapLayout>
          <SearchBar hint="What are you looking for today?" v-model="searchQuery" @submit="onButtonTap" />
          <ListView  for="product in searchedProds" @itemTap="onProductTap">
          <v-template>
          <GridLayout class="list-group-item" rows="*" columns="auto, *">
          <!-- Shows the list item label in the default color and style. -->
          <Image row="0" col="0" :src="product.image" width="80" height="80"/>
          <Label :text="product.name" row="0" col="1" id="product" />
          <Label text="Nearbuy" row="1" col="1" id="availability" />
          <Label text="at Zani's (500m)" row="1" col="2" id="place" />
          <Label text="10€" row="0" col="2" id="price" />
          </GridLayout> 
          </v-template>
          </ListView>
        </WrapLayout>
      </TabViewItem>

      <TabViewItem title="mapTab" iconSource="res://map">           
        <StackLayout>
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
      </TabViewItem>
    
      <TabViewItem title="favTab" iconSource="res://favorite">
        <StackLayout>
          <GridLayout class="list-group-item" rows="*" columns="auto, *">
            <!-- Shows the list item label in the default color and style. -->
            <Image row="0" col="0" src="https://firebasestorage.googleapis.com/v0/b/nearbuy-3d083.appspot.com/o/products%2Fclubmate.jpg?alt=media&token=380a7643-8aad-4b5c-8961-c329c7d8d7c2" width="80" height="80"/>
            <Label text=" Club Mate" row="0" col="1" id="product" />
            <Label text="Nearbuy" row="1" col="1" id="availability" />
            <Label text="at Zani's (500m)" row="1" col="2" id="place" />
            <Label text="10€" row="0" col="2" id="price" />
            </GridLayout>
            <GridLayout class="list-group-item" rows="*" columns="auto, *">
            <!-- Shows the list item label in the default color and style. -->
            <Image row="0" col="0" src="https://firebasestorage.googleapis.com/v0/b/nearbuy-3d083.appspot.com/o/products%2Fclubmate.jpg?alt=media&token=380a7643-8aad-4b5c-8961-c329c7d8d7c2" width="80" height="80"/>
            <Label text=" Club Mate" row="0" col="1" id="product" />
            <Label text="CLOSE" row="1" col="1" id="availability" />
            <Label text="at Zani's (500m)" row="1" col="2" id="place" />
            <Label text="10€" row="0" col="2" id="price" />
          </GridLayout>
        </StackLayout>    
      </TabViewItem>
      
    </TabView>
  </Page>
</template>


<script>
//Importing the map component
const firebase = require("nativescript-plugin-firebase");
const mapbox = require("nativescript-mapbox");
import * as utils from "utils/utils";
import Map from "./map";
import { sep } from "path";
import { login } from "nativescript-plugin-firebase";
let searchProductResults = [];
//Wiring firebase
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
  props: {
    barcode: {
      type: String
    },
    name:{
      type: String
    },
    image:{
      type:String
    },
    textFieldValue: ""
  },
  methods: {
    onMapReady(args) {
      console.log('onMapReady runing from app.vue');
      console.log(this.name);
      args.map.getUserLocation().then(function(userLocation) {
        console.log(
          "Current user location: " +
            userLocation.location.lat +
            ", " +
            userLocation.location.lng
        );
        console.log("Current user speed: " + userLocation.speed);
      });
      var storesCollection = firebase.firestore.collection("stores");
      storesCollection
        .where("products", "array-contains", this.barcode)
        .get()
        .then(function(querySnapshot) {
          querySnapshot.forEach(function(doc) {
            console.log(doc.data());
            args.map.addMarkers([
              {
                lat: doc.data().xcord,
                lng: doc.data().ycord,
                title: doc.data().name,
                subtitle: "Home of The Polyglot Developer!",
                icon: 'res://map_marker',
                onCalloutTap: () => {
                  utils.openUrl("https://www.thepolyglotdeveloper.com");
                }
              }
            ]);

          });
        });
    },
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
  /* background-color: #27ae60; */
  width: 20%;
  text-align: center;
  border-radius: 40%;
  /* color: #ffffff; */
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


/* css for mapTab */
#availability {
  margin-top: 40;
  margin-right: 58%;
  margin-bottom: 3;
  border: none;
  /* background-color: #27ae60; */
  width: 20%;
  text-align: center;
  /* font-size: 10; */
  border-radius: 40%;
  /* color: #ffffff; */
}


#product {
  font-size: 20;
  margin-top: 10;
  padding-right: 80%;
}
#place {
  margin-top: 40;
  margin-left: 20%;
}
#price {
  margin-left: 50%;
  background-color: #e0e0e0;
  width: 12%;
  height: 60%;
  text-align: center;
  font-size: 20;
  
}
.list-group-item {
  z-index: 10;
  margin-bottom: 0%;
  margin-top: 0%;
  padding-bottom: 0%;
  height: 10%;
}

</style>
