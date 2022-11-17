<template>
    <div class="card">
        <div class="card-body">
            <h1 class="card-title fw-bold">Create a room</h1>
            <form class="row align-items-center">
              <div class="d-flex align-items-center justify-content-around">
                <div class="col-sm-4 form-floating form-control-sm">
                  <select class="form-select form-select-sm mb-3 fw-bold" id="floatingSelect" aria-label="Floating label select example" v-model="buildingData">
                    <option v-for="building in buildings" v-bind:value="[building.id, building.name]" class="fw-bold">{{building.name}}</option>
                  </select>
                  <label class="fw-bold mx-2" for="floatingSelect">Choose a building</label>
                </div>
                <div class="col-sm-4 mb-3 form-floating mx-2">
                    <input v-model="roomName" class="fw-bold form-control form-control-sm" id="floatingInput1"/>
                    <label class="fw-bold mx-2" for="floatingInput1">Choose a room Name</label>
                </div>
                <div class="col-sm-4 mb-3 form-floating mx-1">
                    <input v-model="roomFloor" id="floatingInput2" class="fw-bold form-control form-control-sm" />
                    <label class="fw-bold mx-2" for="floatingInput2">Choose a floor</label>
                </div>
              </div>
              <div class="row d-flex flex-row justify-content-around">
                <div class="col-sm-5 mb-3 form-floating">
                    <input v-model="currentTemperature" id="floatingInput3" class="form-control form-control-sm" />
                    <label class="fw-bold mx-2 justify-content-around" for="floatingInput3">Choose a current temperature</label>
                </div>
                <div class="col-sm-5 mb-3 form-floating">
                    <input v-model="targetTemperature" id="floatingInput4" class="form-control form-control-sm" />
                    <label class="fw-bold mx-2" for="floatingInput4">Choose a target temperature</label>
                </div>
              </div>
            </form>
            <div v-if="buildingData == 'Select a building' || roomName == '' || roomFloor == null">
                <button class="btn btn-warning disabled">Create a new room</button>
            </div>
            <div v-else>
                <button class="btn btn-warning" @click="createNewRoom">Create a new room</button>
            </div>
        </div>
    </div>
    <div class="rooms-list pt-3">
        <rooms-list-item 
            v-for="room in rooms"
            :room="room"
            :key="room.id"  
            @rooms-updated="updateRooms"
            @room-updated-windows="updateRoom"
            @room-updated-heaters="updateRoom"
        >
        </rooms-list-item>
    </div>
</template>
  
  
  <script>
  import axios from 'axios';
  import {API_HOST} from '../config';
  import RoomsListItem from './RoomsListItem';
  
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
      RoomsListItem
    },
    name: 'RoomsList',
    data: function() {
      return {
        rooms: [],
        buildings: [],
        roomName: "",
        roomFloor: null,
        currentTemperature: null,
        targetTemperature: null,
        buildingData: "Select a building",
      }
    },
    created: async function() {
      let response = await axios.get(API_HOST + '/api/rooms', axiosConfig);
      let response2 = await axios.get(API_HOST + '/api/buildings', axiosConfig);
      let rooms = response.data;
      this.rooms = rooms;
      let buildings = response2.data;
      this.buildings = buildings;
    },
    methods: {
      updateRooms(newRoom) {
        let index = this.rooms.findIndex(room => room.id === newRoom.id);
        this.rooms.splice(index, 1);
      },
      updateRoom(newRoom) {
      let index = this.windows.findIndex(room => room.id === newRoom.id);
      this.rooms.splice(index, 1, newRoom);
    },
      async createNewRoom() {
        var data = {
          buildingId: this.buildingData[0],
          buildingName: this.buildingData[1],
          name: this.roomName,
          floor: this.roomFloor,
          currentTemperature: this.currentTemperature,
          targetTemperature: this.targetTemperature,
        };
  
        await axios.post(API_HOST + '/api/rooms/', data, axiosConfig);
        let response = await axios.get(API_HOST + '/api/rooms', axiosConfig);
        this.rooms = response.data;
      },
    }
  }
  </script>