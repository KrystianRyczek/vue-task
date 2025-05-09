<script setup lang="ts">
  import { computed, reactive, ref, watchEffect } from "vue";
  import { toXml } from "../utils";

const props = defineProps<{
  groupedData: Record<string, { amount: string | number; currency: string }[]>;
  headers: string[];
  selectFilter: string;
  totalGet: (
    initvalue:{ items: { amount: string | number; currency: string }[];
                selectFilter: string;
                key: string
                }
  ) => Promise<number>;
}>();

 const totals = reactive(new Map<string, number>());

watchEffect(() => {
  for (const [key, rows,] of Object.entries(props.groupedData)) {
    const initvalue={ items: rows,
                      selectFilter: props.selectFilter,
                      key: key,
                    }
    props.totalGet( initvalue ).then((result) => {
    totals.set(key, result);
    });
  }
});
const hidden = reactive(new Set<string>());

function groupToggle(groupKey: string) {
  hidden.has(groupKey) //
    ? hidden.delete(groupKey)
    : hidden.add(groupKey);
}

</script>

<template>
    <h2>Grouped table</h2>
    <br/>

    <table>
      <thead>
        <tr class="header">
          <td v-for="header in headers" :key="header">
            {{ header }}
          </td>
        </tr>
      </thead>
      <tbody>
        <template
          v-for="([key, value], idx) in Object.entries(groupedData)"
          :key="idx"
        >
          <tr @click="groupToggle(key)" class="group">
            <td>
              <div style="display: flex; justify-content: space-between">
                <span>{{ key }}</span>
              </div>
            </td>
          </tr>

          <template v-if="!hidden.has(key)">
            <tr v-for="(row, idx) in value" :key="idx">
              <td v-for="(cellValue, cellKey) in row" :key="cellKey">
                {{ cellValue }}
              </td>
            </tr>

            <tr v-if="value.length > 1">
              <td style="text-align: right">
                <span v-if="value.length > 1" :totals>
                  total: {{ totals.get(key)?.toFixed(2) ?? "..." }} PLN
                </span>
              </td>
            </tr>
          </template>
        </template>
      </tbody>
    </table>
  </template>