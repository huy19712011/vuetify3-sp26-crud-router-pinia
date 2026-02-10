<template>
  <Tutorial></Tutorial>
</template>

<script setup>
import {useRoute} from 'vue-router'

const route = useRoute()
// You can access the ID via route.params.id
console.log('Current ID:', route.params.id)
</script>

<route lang="yaml">
name: "tutorial-details"
meta:
layout: "default"
</route>
