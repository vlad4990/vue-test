<template>
  <div class="contents">
    <div
      class="contents"
      v-for="(dataRow, key) in data"
      :key="key + dataRow.phone"
    >
      <tr v-if="!dataRow.children">
        <td class="px-6 py-1">
          <span
            :style="
              'padding-left:' +
              padding +
              'px; margin-right:-' +
              (padding + 20) +
              'px'
            "
            >{{ dataRow.name }}</span
          >
        </td>
        <td class="px-6 py-1">{{ dataRow.phone }}</td>
      </tr>
      <collapse-wrapper v-else>
        <template #toggleButton="{ id, status }">
          <tr>
            <td class="px-6 py-1 relative">
              <label
                v-if="dataRow.children"
                class="absolute cursor-pointer top-2"
                :style="'left:' + (6 + padding) + 'px'"
                :for="'collapse-' + id"
              >
                <svg
                  v-if="status"
                  xmlns="http://www.w3.org/2000/svg"
                  class="h-4 w-4"
                  fill="none"
                  viewBox="0 0 24 24"
                  stroke="currentColor"
                  stroke-width="2"
                >
                  <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    d="M18 12H6"
                  />
                </svg>
                <svg
                  v-if="!status"
                  xmlns="http://www.w3.org/2000/svg"
                  class="h-4 w-4"
                  fill="none"
                  viewBox="0 0 24 24"
                  stroke="currentColor"
                  stroke-width="2"
                >
                  <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    d="M12 6v6m0 0v6m0-6h6m-6 0H6"
                  />
                </svg>
              </label>
              <span
                :style="
                  'padding-left:' +
                  padding +
                  'px; margin-right:-' +
                  padding +
                  'px'
                "
                >{{ dataRow.name }}</span
              >
            </td>
            <td class="px-6 py-1">{{ dataRow.phone }}</td>
          </tr>
        </template>
        <template
          ><table-rows :data="dataRow.children" :padding="padding + 10"
        /></template>
      </collapse-wrapper>
    </div>
  </div>
</template>

<script>
import TableRows from "./TableRows.vue";
import CollapseWrapper from "./CollapseWrapper.vue";

export default {
  name: "TableRows",
  components: { TableRows, CollapseWrapper },
  props: { data: {}, padding: { type: Number, default: 0 } },
};
</script>

<style>
</style>