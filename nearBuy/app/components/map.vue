<template>
    <Page class="page">
        <ActionBar class="action-bar" title="nearBuy"></ActionBar>
        <GridLayout>
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
        </GridLayout>
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
    textFieldValue: ""
  },
  data() {
    return {};
  },
  methods: {
    onMapReady(args) {
      console.log(this.barcode);
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
  background-color: #6202EE;
  color: #ffffff;
}

.message {
  vertical-align: center;
  text-align: center;
  font-size: 20;
  color: #333333;
}
</style>
 

