<script setup>
import {  ref } from "vue";
const emit= defineEmits(['select'])
const select = (e)=>{
     emit('select', e.target.value)
}

</script>

<template>
    <select @change="select">
      <option>category</option>
      <option>currency</option>
      <option>account</option>
    </select>
    
</template>