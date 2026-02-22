<template>
  <v-container>
    <TutorialList></TutorialList>
  </v-container>
</template>
<script setup>
// adding <route> tag + path inside => replace index.vue file in this folder!!!
// now pages/Tutorials.vue can be deleted
</script>

<route lang="yaml">
path: /tutorials
name: tutorial-list
meta:
  layout: default2
</route>
