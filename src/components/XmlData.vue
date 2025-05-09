<script setup>
  import { computed, reactive, ref } from "vue";
  import { toXml } from "../utils";
    const data = ref([])
    defineProps({
        itemsString:{
            type: String,
            required: true,
        },
    })
    const emit= defineEmits(['xmlExport'])
    const xmlExport = (e)=>{
    emit('xmlExport', 'xmlExport')
}
</script>
  
<template>
<button type="button" @click='xmlExport'>
  <h2> export XML file </h2>
</button>
  <pre> {{ itemsString }} </pre>

</template>

<style scoped>
pre {
  text-align: left;
}
</style>