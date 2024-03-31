<script lang="ts">
import {defineComponent} from 'vue'
import Studentlist from "~/Pages/Studentlist.vue";

export default defineComponent({
  name: "index",
  components: {Studentlist}
})
</script>

<template>
  <Studentlist></Studentlist>
</template>

<style scoped>

</style>