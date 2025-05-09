  
  <script setup>
    defineProps({
        inputData:{
            type: Object,
        },
    })
    const emit= defineEmits(['inputUpdate'])
    const inputUpdate = (e)=>{
      const chagnedData = {
        id: e.target.dataset.id,
        key: e.target.dataset.key,
        newValue: e.target.value,
      }
    emit('inputUpdate', chagnedData)
    }
</script>
  
  <template>
    <table>
        <tr v-for="(item, idx) in inputData" :key="idx">
        <td v-for="(_, key) in item" :key="key"  >
            <input type="text" :value="item[key]" @blur='inputUpdate' :data-id="idx" :data-key="key"/>
        </td>
        </tr>
    </table>
  </template>