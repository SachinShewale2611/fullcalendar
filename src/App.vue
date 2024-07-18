<script setup>
import FullCalendar from './components/FullCalendar.vue'
import AppleSignIn from './components/AppleSignIn.vue'
</script>

<template >
  <div class="body">
    <FullCalendar/>
    <!-- <AppleSignIn/> -->
  </div>
    
</template>

<style scoped>
header {
  line-height: 1.5;
}
.body{
  display: block;
}
.logo {
  display: block;
  margin: 0 auto 2rem;
}
</style>
