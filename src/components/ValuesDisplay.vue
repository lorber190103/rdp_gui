<script lang="ts">
import { Value } from '@/scripts/value'

export default {
  props: ['values', 'value_types', 'sensors', 'locations'],
  setup(props) {
    console.log(props.values)
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
  }
}
</script>

<template>
  <div class="row bg-primary mt-2 mb-1">
    <div
      class="col-1"
      data-bs-toggle="tooltip"
      data-bs-placement="top"
      data-bs-title="Tooltip on top">
      time
    </div>
    <div class="col-1">type</div>
    <div class="col">value</div>
    <div class="col-1">sensor</div>
    <div class="col-1">location</div>
  </div>
  <div class="row bg-secondary rounded mt-1" v-for="value in values" :key="value">
    <div class="col-1">
      {{ value.time }}
    </div>
    <div class="col-1">
      {{ getTypeName(value) }}
    </div>
    <div class="col">{{ value.value.toFixed(2) }} {{ getUnit(value) }}</div>
    <div class="col-1">{{ getSensorName(value) }}</div>
    <div class="col-1"> {{ getLocationName(value) }} </div>
  </div>
</template>
