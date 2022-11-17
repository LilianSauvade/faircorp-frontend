<template>
  <div id="app">
    <header>
      <div class="fw-bold">{{title}}</div>
    </header>

    <section class="main-content w-50 mx-auto">
      <main-navigation :route="route" @updated-route="updateRoute"></main-navigation>
      <div v-if="route == '/rooms/'">
        <rooms-list></rooms-list>
      </div>
      <div v-else-if="route == '/heaters/'">
        <heaters-list></heaters-list>
      </div>
      <div v-else>
        <windows-list></windows-list>
      </div>
    </section>
  </div>
</template>

<script>
import MainNavigation from './components/MainNavigation.vue';
import WindowsList from './components/WindowsList.vue';
import HeatersList from './components/HeatersList.vue';
import RoomsList from './components/RoomsList.vue';

export default {
  name: 'App',
  components: {
    MainNavigation,
    WindowsList,
    HeatersList,
    RoomsList
  },
  
  data: function() {
    return {
      title: 'Faircorp Application',
      route: '',
    }
  },
  methods: {
    updateRoute(newRoute) {
      this.route = newRoute;
    }
  }
}

</script>

<style lang="scss" scoped>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

</style>
