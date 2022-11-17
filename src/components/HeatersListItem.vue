<template>
    <div class="heater border border-secondary rounded p-2 mb-2" :class="{expanded: isExpanded}">
      <div class="top-row d-flex" @click="toggleExpand">
        <div class="col-sm-2 d-flex justify-content-start">
          <div class="heater-name fw-bold pe-3">{{heater.name}}</div>
        </div>
        <div class="col-sm-2 d-flex justify-content-start">
          <div class="room-name fw-bold text-muted">{{heater.roomName}}</div>
        </div>
  
        <div class="fw-bold col-sm-6 d-flex justify-content-end">
          <div class="open-status ms-4" :class="{open: isHeaterOn, closed: !isHeaterOn}">
            <template v-if="isHeaterOn">
              <span class="icon">&#x2B24;</span> ON
            </template>
            <template v-else>
              <span class="icon">&#x2716;</span> OFF
            </template>
          </div>
        </div>
        
  
        <div class="expand-button ms-auto">
          {{ isExpanded ? '&#9660;' : '&#9658;' }}
        </div>
      </div>
      <template v-if="isExpanded">
        <hr/>
        <div class="details d-flex">
          <button type="button" class="btn btn-secondary me-2" @click="switchHeater">Switch {{ isHeaterOn ? 'OFF' : 'ON' }} heater</button>
          <button type="button" class="btn btn-danger me-2" @click="confirmation">Delete heater</button>
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
    name: 'HeatersListItem',
    props: ['heater'],
    data: function() {
      return {
        isExpanded: false
      }
    }, 
    computed: {
      isHeaterOn: function() {
        return this.heater.heaterStatus === 'ON';
      }
    },
    methods: {
      toggleExpand() {
        this.isExpanded = !this.isExpanded;
      },
      async switchHeater() {
        let response = await axios.put(API_HOST + '/api/heaters/' + this.heater.id + '/switch', axiosConfig);
        let updatedHeater = response.data;
        this.$emit('heater-updated', updatedHeater);
      },
      confirmation() {
          if (confirm("Are you sure ? You are going to delete the heater : " + this.heater.name + ".\nThis action is irreversible.") == true) {
            this.deleteHeater();
          }
        },
      async deleteHeater() {
        let index = this.heater.id;
        await axios.delete(API_HOST + '/api/heaters/' + this.heater.id, axiosConfig);
      
        this.$emit('heaters-updated', this.heater);
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
  
  .heater {
    .top-row {
      cursor: pointer;
    }
  }
  </style>
  