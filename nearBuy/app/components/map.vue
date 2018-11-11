<template>
  <Page class="page">
    <ActionBar class="actionbar">
      <StackLayout orientation="horizontal" >
        <Image src="res://baseline_keyboard_arrow_left_white_18" width="40" height="40" id="logo" @tap="$navigateBack"/>
            <Label :text="name" fontSize="24" id="nearbuy" />
      </StackLayout>
    </ActionBar>

    <TabView androidTabsPosition="bottom" selectedIndex="selectedIndex">
        <TabViewItem title="Tab 1" iconSource="res://search">
          <WrapLayout>            
            <StackLayout>
              <!-- Shows the list item label in the default color and style. -->
              <GridLayout class="list-group-item" rows="*" columns="auto, *">
              <!-- Shows the list item label in the default color and style. -->
              <Image row="0" col="0" :src="image" width="100" height="100"/>
              <Label :text="name" row="0" col="1" id="product" />
              <Label text="CLOSE" row="1" col="1" id="availability" />
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
        <TabViewItem title="Tab 2" iconSource="res://map">
        <WrapLayout>
          <Label text="Content for Tab 2" />
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
const firebase = require("nativescript-plugin-firebase");
const mapbox = require("nativescript-mapbox");

console.log("im map");
import * as utils from "utils/utils";
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
  data() {
    return {};
  },
  methods: {
    
    onMapReady(args) {
      
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
      /*args.map.setCenter({
        lat: 52.360216, // mandatory
        lng: 4.889168, // mandatory
        animated: false // default true
      });*/
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
    }
  }
};
</script> 

<style scoped>
ActionBar {
  background-color: #6202ee;
  color: #ffffff;
}
#map{
 
}
#availability {
  margin-top: 40;
  margin-right: 58%;
  margin-bottom: 3;
  border: none;
  background-color: #27ae60;
  width: 20%;
  text-align: center;
  font-size: 10;
  border-radius: 40%;
  color: #ffffff;
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
 

