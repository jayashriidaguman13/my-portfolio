<template>
  <div class="card col-md-3 mb-4">
    <a :href="project.link" target="blank_"><img :src="project.image" class="card-img-top img-fluid" alt="Project image"></a>
    <div class="card-body">
      <h5 class="card-title">{{ project.title }}</h5>
      <p class="card-text">{{ project.description }}</p>
    </div>
  </div>
</template>

<script setup>
  import { defineProps } from 'vue';

  defineProps({
    project: Object
  });
</script>