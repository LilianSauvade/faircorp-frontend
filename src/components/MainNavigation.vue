<template>
  <ul class="nav nav-tabs">
    <div v-if="route == '/rooms/'" class="d-flex flex-row">
      <li class="nav-item">
        <a class="nav-link" href="#" tabindex="-1" aria-disabled="true" @click="navigateMenuWindows">Windows</a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="#" tabindex="-1" aria-disabled="true" @click="navigateMenuHeaters">Heaters</a>
      </li>
      <li class="nav-item">
        <a class="nav-link active" aria-current="page" href="#" @click="navigateMenuRooms">Rooms</a>
      </li>
    </div>
    <div v-else-if="route == '/heaters/'" class="d-flex flex-row">
      <li class="nav-item">
        <a class="nav-link" aria-current="page" href="#" @click="navigateMenuWindows">Windows</a>
      </li>
      <li class="nav-item">
        <a class="nav-link active" aria-current="page" href="#" @click="navigateMenuHeaters">Heaters</a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="#" tabindex="-1" aria-disabled="true" @click="navigateMenuRooms">Rooms</a>
      </li>
    </div>
    <div v-else class="d-flex flex-row">
      <li class="nav-item">
        <a class="nav-link active" aria-current="page" href="#" @click="navigateMenuWindows">Windows</a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="#" tabindex="-1" aria-disabled="true" @click="navigateMenuHeaters">Heaters</a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="#" tabindex="-1" aria-disabled="true" @click="navigateMenuRooms">Rooms</a>
      </li>
    </div>
  </ul>
</template>


<script>
export default {
  name: 'MainNavigation',
  props: ['route'],
  data: function() {
        return {
        }
      },
methods: {
      navigateMenuWindows() {
        if (this.route != "/windows/") {
          let updatedRoute = "/windows/";
          this.$emit("updated-route", updatedRoute);
        }
      },
      navigateMenuHeaters() {
        if (this.route != "/heaters/") {
          let updatedRoute = "/heaters/";
          this.$emit("updated-route", updatedRoute);
        }
      },
      navigateMenuRooms() {
        if (this.route != "/rooms/") {
          let updatedRoute = "/rooms/";
          this.$emit("updated-route", updatedRoute);
        } 
      }
    }
  }
</script>
