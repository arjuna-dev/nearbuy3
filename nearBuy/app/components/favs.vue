<template>
    <Page>
        <ActionBar class="actionbar">
          <StackLayout orientation="horizontal">
            <Image src="res://baseline_favorite_white_18" width="40" height="40" id="logo" />
            <Label text="Favourites" fontSize="24" id="nearbuy" />
          </StackLayout>
    </ActionBar>
        <TabView androidTabsPosition="bottom" selectedIndex="selectedIndex" v-model="selectedIndex" @selectedIndexChange="onTabTap">
        <TabViewItem title="Tab 1" iconSource="res://search">
         
            
         <StackLayout>
         <GridLayout class="list-group-item" rows="*" columns="auto, *">
            <!-- Shows the list item label in the default color and style. -->
            <Image row="0" col="0" src="https://firebasestorage.googleapis.com/v0/b/nearbuy-3d083.appspot.com/o/products%2Fclubmate.jpg?alt=media&token=380a7643-8aad-4b5c-8961-c329c7d8d7c2" width="80" height="80"/>
            <Label text=" Club Mate" row="0" col="1" id="product" />
            <Label text="CLOSE" row="1" col="1" id="availability" />
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
        <TabViewItem title="Tab 2" iconSource="res://map">
        <WrapLayout>
          <Label text="Content for Tab 2" />
        </WrapLayout>
        </TabViewItem>
        <TabViewItem title="Tab 3" iconSource="res://favorite">
        <WrapLayout>
          
        </WrapLayout>
        </TabViewItem>
      </TabView>
    </Page>
</template>

<script>
import app from "./app";

export default {
  methods: {
    
    onTabTap() {
      console.log(this.$data.selectedIndex.value);
      if (this.$data.selectedIndex.value == 5){
        this.$navigateTo(app);
      }


    },
    onProductTap(event) {
      console.log(event.item.name);
      this.$navigateTo(Map, { props: { barcode: event.item.barcode, name:event.item.name, image:event.item.image} });
    }
  },
  data() {
    return {
      selectedIndex: 2
      
    };
  }

  
};
</script>


<style scoped>
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
  border: 10 solid black;
  
  height: 18%;
 
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
ActionBar {
  background-color: #6202ee;
  color: #ffffff;
}
#logo {
    
}
</style>
