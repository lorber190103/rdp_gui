<script lang="ts">

import { Sensor } from '@/scripts/sensor';
import axios from 'axios';

export default {
    props: ["sensor_prop"],
    data() {
        return {
            edit_sensor: new Sensor(),
            changed: false
        }
    },
    mounted() {
        console.log("Mounted with ", this.sensor_prop)
        this.edit_sensor.id=this.sensor_prop.id
        this.edit_sensor.sensor_name=this.sensor_prop.sensor_name
        this.edit_sensor.sensor_location_id=this.sensor_prop.sensor_location_id
    },
    methods: {
        update_changed() {
            this.changed = ( (this.sensor_prop.sensor_name != this.edit_sensor.sensor_name) || 
                             (this.sensor_prop.sensor_location_id != this.edit_sensor.sensor_location_id) )
            console.log("Changed is now",this.changed)
        },
        update_sensor() {
            axios.put("/api/sensors/"+this.edit_sensor.id+"/",this.edit_sensor)
                .then((result) => {
                    console.log(result)
                    this.$emit("update_sensor")
                })

        }
    },
    emits: ["update_sensor"]
}
</script>

<template>
<div class="row rounded mt-1 mb-0 p-0 ">
    <input class="col bg-secondary rounded me-1" v-model="edit_sensor.sensor_name" v-on:change="update_changed" />
    <input class="col bg-secondary rounded" v-model="edit_sensor.sensor_location_id" v-on:change="update_changed"/>
    <button class="col bg-primary text-end rounded mb-1" v-if="changed" v-on:click="update_sensor()">submit </button>
</div>

</template>