<template>
<UContainer>
<UCard class="mt-10">
  <template #header>
    <div class="flex justify-between">
      <h1>Vilniaus kolegija </h1>
      <ColorScheme><USelect v-model="$colorMode.preference" :options="['system', 'light', 'dark']" /></ColorScheme>
    </div>
  </template>
  <slot/>

</UCard>
</UContainer>
</template>
 <script setup lang="ts">
 </script>