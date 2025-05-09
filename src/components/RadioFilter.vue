<script setup>
const emit= defineEmits(['filter'])
const filter = (e)=>{
     emit('filter', e.target.value)
}
</script>

<template>
  <div>
    <label>xml<input type="radio" value="xml" name="show"  @change="filter"/></label>
    <label>table<input type="radio" value="table"  name="show" checked @change="filter"/> </label>
  </div>
</template>