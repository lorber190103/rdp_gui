<script lang="ts">
import { Value } from '@/scripts/value'

export default {
  props: ['values', 'value_types', 'sensors', 'locations'],
  setup(props) {
    console.log(props.values)
  },
  data() {
        return {
            editor_hidden: false
        };
    },
  emits: ['rename'],
  methods: {
    getTypeName(value: Value) {
      for (var i = 0; i < this.value_types.length; i++) {
        if (this.value_types[i].id == value.value_type_id) {
          return this.value_types[i].type_name
        }
      }
      return 'XXX'
    },
    getUnit(value: Value) {
      for (var i = 0; i < this.value_types.length; i++) {
        if (this.value_types[i].id == value.value_type_id) {
          return this.value_types[i].type_unit
        }
      }
      return 'XXX'
    },
    getSensorName(value: Value) {
      for (var i = 0; i < this.sensors.length; i++) {
        if (this.sensors[i].id == value.value_sensor_id) {
          return this.sensors[i].sensor_name
        }
      }
      return 'XXX'
    },
    getLocationName(value: Value) {
      for (var i = 0; i < this.sensors.length; i++) {
        if (this.sensors[i].id == value.value_sensor_id) {
          const sensor_location_id = this.sensors[i].sensor_location_id;
          for (var j = 0; j < this.locations.length; j++) {
            if (this.locations[j].id == sensor_location_id) {
              return this.locations[j].location_name;
            }
          }
        }
      }
      return 'XXX'
    },
    toggleEditor() {
        this.editor_hidden = !this.editor_hidden;
      }
    },
  }
</script>

<template>
  <div>
    <!-- Labels row -->
    <div class="row bg-primary mt-2 mb-1">
      <div class="col-1" data-bs-toggle="tooltip" data-bs-placement="top" data-bs-title="Tooltip on top">
        time
      </div>
      <div class="col-1">type</div>
      <div class="col-2">value</div>
      <div class="col-2">sensor</div>
      <div class="col-2">location</div>
      <span class="col text-end" v-on:click="toggleEditor()">
        <i class="bi bi-caret-down-fill" title="Show ValueDisplay" v-if="editor_hidden"></i>
        <i class="bi bi-caret-up-fill" title="Hide ValueDisplay" v-else></i>
      </span>
    </div>

    <!-- Values rows -->
    <div class="row bg-secondary rounded mt-1" v-for="value in values" :key="value" v-if="!editor_hidden">
      <div class="col-1">{{ value.time }}</div>
      <div class="col-1">{{ getTypeName(value) }}</div>
      <div class="col-2">{{ value.value.toFixed(2) }} {{ getUnit(value) }}</div>
      <div class="col-2">{{ getSensorName(value) }}</div>
      <div class="col-2">{{ getLocationName(value) }}</div>
    </div>
  </div>
</template>

