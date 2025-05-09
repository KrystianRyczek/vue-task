<script setup lang="ts">
import { getCurrentInstance, computed, reactive, ref } from "vue";
import { dataGroup, toXml, useExampleData, exportXmlFromJson } from "./utils";
import  RadioFilter  from "./components/RadioFilter.vue"
import  SelectFilter  from "./components/SelectFilter.vue"
import  XmlData  from "./components/XmlData.vue"
import  TableData  from "./components/TableData.vue"
import  Inputs  from "./components/Inputs.vue"


const radioFilter = ref('table');
const selectFilter = ref('category');
const inputData = ref(null);
const xmlExport = ref('');
type Data = {
  category: string;
  amount: string;
  currency: string;
  [key: string]: string;
};

const data = useExampleData<Data>();

if(inputData.value ===null){
inputData.value = data
};

const handleRadioBtn = (filter)=>{
  radioFilter.value=filter
};

const handleSelectFilter = (select)=>{
  selectFilter.value=select
};

const handleInputUpdate = (inputUpdate)=>{
  const index = inputUpdate.id
  const key = inputUpdate.key
  const newValue = inputUpdate.newValue
  inputData.value.value[index][key]=newValue
};

const handleXmlExport = (xmlExport)=>{
  exportXmlFromJson(inputData.value.value)
};

// TODO: TASK → avoid recomputing while user is still typing
const xml = computed(() => toXml(inputData.value.value  ?? []));

// TODO: TASK → let the user also group by currency and account
const groupedData = computed(() =>{
  return inputData.value.value  //
    ? dataGroup(inputData.value.value , selectFilter.value)
    : []
},);
const headers = computed(() =>
  Object.keys(inputData.value.value ?.[0] ?? {}).filter((i) => i !== selectFilter.value),
);

// TODO: TASK → handle different currencies. Use `plnToCurrency` function to get the rates

async function totalGet( initvalue:object ): Promise<number> {
  let currency
  const valuesInPln = await Promise.all(
    initvalue.items.map(async (item) => {
         initvalue.selectFilter==='currency'? currency=initvalue.key
                                            : currency=item.currency
      const rate = await plnToCurrency(currency.toLowerCase());
      return Number(item.amount) / rate;
    })
  );
  const total = valuesInPln.reduce((acc, val) => acc + val, 0);
  return total
}

// @ts-ignore
async function plnToCurrency(curr: string) {
  if (curr === "pln") return 1;

  const res = await fetch(
    `http://localhost:5173/currency/pln-to-${curr.toLowerCase()}`,
  );
  const text = await res.text();
  return Number(text.trim());
}
</script>



<template>
  <RadioFilter @filter="handleRadioBtn"/>
  <SelectFilter v-if="radioFilter !== 'xml'" @select="handleSelectFilter"/>
  <XmlData v-if="radioFilter === 'xml'" :itemsString="xml" @xmlExport="handleXmlExport"/>
  <TableData v-else :groupedData="groupedData" :headers="headers" :totalGet="totalGet" :selectFilter="selectFilter"/>
   <Inputs :inputData="inputData.value" @inputUpdate="handleInputUpdate"/>
</template>




