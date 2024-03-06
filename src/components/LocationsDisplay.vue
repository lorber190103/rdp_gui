<script lang="ts">
import SingleLocationDisplay from './SingleLocationDisplay.vue';
export default {
    props: ['locations'],
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
    components: { SingleLocationDisplay },
    emits: ['update_location']
}
</script>

<template>
  <div class="row bg-primary mt-1">
    <span class="col-2">Location Editor</span
    ><span class="col text-end" v-on:click="toggleEditor()">
      <i class="bi bi-caret-down-fill" title="Show LocationEditor" v-if="editor_hidden"></i>
      <i class="bi bi-caret-up-fill" title="Hide LocationEditor" v-else></i>
    </span>
  </div>
  <span v-if="!editor_hidden">
    <SingleLocationDisplay :location_prop="location" v-for="location in locations" :key="location" @update_location="$emit('update_location')"/>
  </span>
</template>
