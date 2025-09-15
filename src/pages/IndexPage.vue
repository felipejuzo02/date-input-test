<template>
  <q-page class="flex flex-center">
    <div>
      <app-date-time-input v-model="model" label="Somente data" use-date-only></app-date-time-input>
    </div>
  </q-page>
</template>

<script setup>
import AppDateTimeInput from 'src/components/AppDateTimeInput.vue'

import { ref } from 'vue'

const model = ref('')
</script>
