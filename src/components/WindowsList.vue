<template>
  <div class="card">
    <div class="card-body">
      <h1 class="card-title fw-bold">Create a window</h1>
      <form class="row align-items-center">
        <div class="d-flex align-items-center justify-content-around mb-3">
          <div class="col-sm-4 form-floating mx-2">
            <input v-model="windowName" class="fw-bold form-control form-control-sm" id="floatingInput1"/>
            <label class="fw-bold mx-2" for="floatingInput1">Choose a window Name</label>
          </div>
          <div class="col-sm-4 form-floating form-control-sm">
            <select v-model="roomID" class="form-select form-select-sm fw-bold" id="floatingSelect">
              <option v-for="room in rooms" v-bind:value="room.id">{{room.name}}</option>
            </select>
            <label class="fw-bold mx-2" for="floatingSelect">Choose a room</label>
          </div>
          <div class="col-sm-4 form-floating form-control-sm">
            <select v-model="Status" class="form-select form-select-sm fw-bold" id="floatingSelect2">
              <option value="OPEN">OPEN</option>
              <option value="CLOSED">CLOSED</option>
            </select>
            <label class="fw-bold mx-2" for="floatingSelect">Window Status</label>
          </div>
        </div>
      </form>
      <div v-if="windowName == '' || roomID == null">
          <button class="btn btn-warning disabled">Create a new window</button>
        </div>
        <div v-else>
          <button class="btn btn-warning" @click="createNewWindow">Create a new window</button>
        </div>
    </div>
  </div>
  <div class="windows-list pt-3">
    <windows-list-item 
      v-for="window in windows"
      :window="window"
      :key="window.id"  
      @window-updated="updateWindow"
      @windows-updated="updateWindows"
    >
    </windows-list-item>
  </div>
</template>


<script>
import axios from 'axios';
import {API_HOST} from '../config';
import WindowsListItem from './WindowsListItem';

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
    WindowsListItem
  },
  name: 'WindowsList',
  data: function() {
    return {
      windows: [],
      rooms: [],
      windowName: "",
      Status: "CLOSED",
      roomID: null,
    }
  },
  created: async function() {
    let response = await axios.get(API_HOST + '/api/windows', axiosConfig);
    let response2 = await axios.get(API_HOST + '/api/rooms', axiosConfig);


    let windows = response.data;
    this.windows = windows;
    let rooms = response2.data;
    this.rooms = rooms;
  },
  methods: {
    updateWindow(newWindow) {
      /* Find the place of window object with the same Id in the array, and replace it */
      let index = this.windows.findIndex(window => window.id === newWindow.id);
      this.windows.splice(index, 1, newWindow);
    },
    updateWindows(newWindow) {
      let index = this.windows.findIndex(window => window.id === newWindow.id);
      this.windows.splice(index, 1);
    },
    async createNewWindow() {
      var data = {
        name: this.windowName,
        roomId: this.roomID,
        windowStatus: this.Status
      };

      await axios.post(API_HOST + '/api/windows/', data, axiosConfig);
      let response = await axios.get(API_HOST + '/api/windows/', axiosConfig);
      this.windows = response.data;
    }
  }
}
</script>