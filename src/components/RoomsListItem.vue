<template>
  <div class="card room border border-secondary rounded p-2 mb-2" :class="{expanded: isExpanded}">
    <div class="top-row d-flex" @click="toggleExpand">
      <div class="col-sm-3 d-flex justify-content-start">
        <div class="window-name fw-bold pe-3">{{room.buildingName}}</div>
      </div>
      <div class="col-sm-2 d-flex justify-content-start">
        <div class="window-name fw-bold pe-3">{{room.name}}</div>
      </div>
      <div class="col-sm-1 d-flex justify-content-start">
        <div class="room-name text-muted">Floor {{room.floor}}</div>
      </div>
      
      <div class="expand-button ms-auto">
        {{ isExpanded ? '&#9660;' : '&#9658;' }}
      </div>
    </div>
    <template v-if="isExpanded">
      <hr/>
      <div class="card border-dark text-dark bg-light mb-3 mx-auto my-3" style="width: 40%;">
        <div v-if="room.currentTemperature != null" class="mx-5 d-flex justify-content-start"><span class="fw-bold">Current temperature : </span><p class="invisible">a</p>{{room.currentTemperature}}°C</div>
        <div v-else  class="mx-5 d-flex justify-content-start"><span class="fw-bold">Current temperature : </span><p class="invisible">a</p>unknown</div>
        <div v-if="room.targetTemperature != null"  class="mx-5 d-flex justify-content-start"><span class="fw-bold">Target temperature : </span><p class="invisible">a</p>{{room.targetTemperature}}°C</div>
        <div v-else  class="mx-5 d-flex justify-content-start"><span class="fw-bold">Target temperature : </span><p class="invisible">a</p>unknown</div>
      </div>
      <div class="d-flex flex-">
        <div class="card border-dark text-dark bg-light mb-3 mx-auto my-3" style="width: 40%;"> <span class="fw-bold">Heaters</span>
          <div v-if="!room.heaters.length">No heaters in the room.</div>
          <div v-for="heater in room.heaters" class="mx-5 d-flex justify-content-start">
            <div class="col-sm-4 d-flex justify-content-start">
              <div class="heater-name fw-bold pe-3">{{heater.name}}</div>
            </div>
            <div class="col-sm-4 d-flex justify-content-start">
              <div class="room-name">{{heater.heaterStatus}}</div>
            </div>
          </div>
        </div>
        <div class="card border-dark text-dark bg-light mb-3 mx-auto my-3" style="width: 40%;"><span class="fw-bold">Windows</span>
          <div v-if="!room.windows.length">No windows in the room.</div>
          <div v-for="window in room.windows" class="mx-5 d-flex justify-content-start">
            <div class="col-sm-4 justify-content-start">
              <div class="heater-name fw-bold pe-3">{{window.name}}</div>
            </div>
            <div class="col-lg-4 justify-content-start">
              <div class="room-name">{{window.windowStatus}}</div>
            </div>
          </div>
        </div>
      </div>
      <hr/>
      <div class="details d-flex">
        <button type="button" class="btn btn-danger mx-2" @click="confirmation">Delete Room</button>
        <button type="button" class="btn btn-primary mx-2" @click="switchWindows">Switch Windows</button>
        <button type="button" class="btn btn-secondary mx-2" @click="switchHeaters">Switch Heaters</button>
      </div>
    </template>
  </div>
</template>


<script>
import axios from 'axios';
import {API_HOST} from '../config';

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
  name: 'RoomsListItem',
  props: ['room'],
  data: function() {
    return {
      isExpanded: false
    }
  }, 
  methods: {
    toggleExpand() {
      this.isExpanded = !this.isExpanded;
    },
    confirmation() {
      if (confirm("Are you sure ? You are going to delete the room : " + this.room.name + ".\nThis action is irreversible.") == true) {
        this.deleteRoom();
      }
    },
    async deleteRoom() {
      let index = 
      await axios.delete(API_HOST + '/api/rooms/' + this.room.id, axiosConfig);
    
      this.$emit('rooms-updated', this.room);
    },
    async switchWindows() {
      let response = await axios.put(API_HOST + '/api/rooms/' + this.room.id + '/switchWindows', axiosConfig);
      this.room.windows = response.data;
      this.$emit('room-updated-windows', this.room);
    },
    async switchHeaters() {
      let response = await axios.put(API_HOST + '/api/rooms/' + this.room.id + '/switchHeaters', axiosConfig);
      this.room.heaters = response.data;
      this.$emit('room-updated-heaters', this.room);
    }

  }
}
</script>

<style lang="scss" scoped>

.open-status {
  .icon {
    position: relative;
  }

  &.open {
    color: #198754;
    .icon {
      font-size: 12px;
      top: -3px;
    }
  }

  &.closed {
    color: #dc3545;
  }
}

.room {
  .top-row {
    cursor: pointer;
  }
}
</style>
