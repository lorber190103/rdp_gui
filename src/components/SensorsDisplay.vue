<script lang="ts">
import SingleSensorDisplay from './SingleSensorDisplay.vue';
export default {
    props: ['sensors'],
    data() {
        return {
            editor_hidden: true
        };
    },
    methods: {
        toggleEditor() {
            this.editor_hidden = !this.editor_hidden;
        }
    },
    components: { SingleSensorDisplay },
    emits: ['update_sensor']
}
</script>

<template>
  <div class="row bg-primary mt-1">
    <span class="col-2">Sensor Editor</span
    ><span class="col text-end" v-on:click="toggleEditor()">
      <i class="bi bi-caret-down-fill" title="Show SensorEditor" v-if="editor_hidden"></i>
      <i class="bi bi-caret-up-fill" title="Hide SensorEditor" v-else></i>
    </span>
  </div>
  <span v-if="!editor_hidden">
    <SingleSensorDisplay :sensor_prop="sensor" v-for="sensor in sensors" :key="sensor" @update_sensor="$emit('update_sensor')"/>
  </span>
</template>
