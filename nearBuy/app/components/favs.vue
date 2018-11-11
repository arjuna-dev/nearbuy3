<template>
    <Page>
        <ActionBar title="Favourites"/>
        <TabView androidTabsPosition="bottom" selectedIndex="selectedIndex" v-model="selectedIndex" @selectedIndexChange="onTabTap">
        <TabViewItem title="Tab 1" iconSource="res://search">
          <WrapLayout>
          </WrapLayout>
        </TabViewItem>
        <TabViewItem title="Tab 2" iconSource="res://map">
        <WrapLayout>
          <Label text="Content for Tab 2" />
        </WrapLayout>
        </TabViewItem>
        <TabViewItem title="Tab 3" iconSource="res://gps">
        <WrapLayout>
          <Label text="Content for Tab 3" />
        </WrapLayout>
        </TabViewItem>
      </TabView>
    </Page>
</template>

