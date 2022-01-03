<template>
    <ImageDetectionComponent/>
</template>

<script lang="ts">
import { Options, Vue } from 'vue-class-component';
import ImageDetectionComponent from "./components/ImageDetectionComponent.vue";

@Options({
  components: {
    ImageDetectionComponent
  },
})
export default class App extends Vue {}
</script>

<style>
    @import "assets/css/style.css";
    @import "assets/css/font-awesome.min.css";
</style>
