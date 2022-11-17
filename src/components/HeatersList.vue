<template>
    <div class="card">
      <div class="card-body">
        <h1 class="card-title fw-bold">Create a heater</h1>
        <form class="row align-items-center">
          <div class="d-flex align-items-center justify-content-around mb-3">
            <div class="col-sm-4 form-floating mx-2">
              <input v-model="heaterName" class="fw-bold form-control form-control-sm" id="floatingInput1"/>
              <label class="fw-bold mx-2" for="floatingInput1">Choose a heater Name</label>
            </div>
            <div class="col-sm-4 form-floating form-control-sm">
              <select v-model="roomID" class="form-select form-select-sm fw-bold" id="floatingSelect">
                <option v-for="room in rooms" v-bind:value="room.id">{{room.name}}</option>
              </select>
              <label class="fw-bold mx-2" for="floatingSelect">Choose a room</label>
            </div>
            <div class="col-sm-4 form-floating form-control-sm">
              <select v-model="Status" class="form-select form-select-sm fw-bold" id="floatingSelect2">
                <option value="ON">ON</option>
                <option value="OFF">OFF</option>
              </select>
              <label class="fw-bold mx-2" for="floatingSelect">Heater Status</label>
            </div>
          </div>
        </form>
        <div v-if="heaterName == '' || roomID == null">
            <button class="btn btn-warning disabled">Create heater</button>
          </div>
          <div v-else>
            <button class="btn btn-warning" @click="createNewHeater">Create heater</button>
          </div>
      </div>
    </div>
    <div class="heater-list pt-3">
      <heaters-list-item 
        v-for="heater in heaters"
        :heater="heater"
        :key="heater.id"  
        @heater-updated="updateHeater"
        @heaters-updated="updateHeaters"
      >
      </heaters-list-item>
    </div>
</template>
  
  
  <script>
  import axios from 'axios';
  import {API_HOST} from '../config';
  import HeatersListItem from './HeatersListItem';
  
  let axiosConfig = {
          headers: {
                      'Access-Control-Allow-Origin': '*',
                      'Access-Control-Allow-Headers': '*',
                      'Access-Control-Allow-Credentials': 'true'
          },
          auth: {
            username: 'admin',
            password: 'myAdmin'
          }, }
  
  export default {
    components: {
      HeatersListItem
    },
    name: 'HeatersList',
    data: function() {
      return {
        heaters: [],
        rooms: [],
        heaterName: "",
        Status: "OFF",
        roomID: null,
      }
    },
    created: async function() {
      let response = await axios.get(API_HOST + '/api/heaters', axiosConfig);
      let response2 = await axios.get(API_HOST + '/api/rooms', axiosConfig);
  
  
      let heaters = response.data;
      this.heaters = heaters;
      let rooms = response2.data;
      this.rooms = rooms;
    },
    methods: {
      updateHeater(newHeater) {
        let index = this.heaters.findIndex(heater => heater.id === newHeater.id);
        this.heaters.splice(index, 1, newHeater);
      },
      updateHeaters(newHeater) {
        let index = this.heaters.findIndex(heater => heater.id === newHeater.id);
        this.heaters.splice(index, 1);
      },
      async createNewHeater() {
        var data = {
          name: this.heaterName,
          roomId: this.roomID,
          heaterStatus: this.Status
        };
  
        await axios.post(API_HOST + '/api/heaters/', data, axiosConfig);
        let response = await axios.get(API_HOST + '/api/heaters/', axiosConfig);
        this.heaters = response.data;
      }
    }
  }
  </script>