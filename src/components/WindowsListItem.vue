<template>
  <div class="window border border-secondary rounded p-2 mb-2" :class="{expanded: isExpanded}">
    <div class="top-row d-flex" @click="toggleExpand">
      <div class="col-sm-2 d-flex justify-content-start">
        <div class="window-name fw-bold pe-3">{{window.name}}</div>
      </div>
      <div class="col-sm-2 d-flex justify-content-start">
        <div class="room-name fw-bold text-muted">{{window.roomName}}</div>
      </div>

      <div class="fw-bold col-sm-6 d-flex justify-content-end">
        <div class="open-status ms-4" :class="{open: isWindowOpen, closed: !isWindowOpen}">
          <template v-if="isWindowOpen">
            <span class="icon">&#x2B24;</span> Open
          </template>
          <template v-else>
            <span class="icon">&#x2716;</span> Closed
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
        <button type="button" class="btn btn-secondary me-2" @click="switchWindow">{{ isWindowOpen ? 'Close' : 'Open' }} window</button>
        <button type="button" class="btn btn-danger me-2" @click="confirmation">Delete window</button>
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
  name: 'WindowsListItem',
  props: ['window'],
  data: function() {
    return {
      isExpanded: false
    }
  }, 
  computed: {
    isWindowOpen: function() {
      return this.window.windowStatus === 'OPEN'; 
    }
  },
  methods: {
    toggleExpand() {
      this.isExpanded = !this.isExpanded;
    },
    async switchWindow() {
      let response = await axios.put(API_HOST + '/api/windows/' + this.window.id + '/switch', axiosConfig);
      let updatedWindow = response.data;
      this.$emit('window-updated', updatedWindow);
    },
    confirmation() {
        if (confirm("Are you sure ? You are going to delete the window : " + this.window.name + ".\nThis action is irreversible.") == true) {
          this.deleteWindow();
        }
      },
    async deleteWindow() {
      let index = this.window.id;
      await axios.delete(API_HOST + '/api/windows/' + this.window.id, axiosConfig);
    
      this.$emit('windows-updated', this.window);
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

.window {
  .top-row {
    cursor: pointer;
  }
}
</style>
