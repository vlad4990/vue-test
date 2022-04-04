<template>
  <div>
    <div class="max-w-4xl mx-auto py-10 sm:px-6 lg:px-12 prose">
      <div
        class="shadow overflow-hidden border-b border-gray-200 sm:rounded-lg"
      >
        <table class="min-w-full divide-y divide-gray-200">
          <thead v-if="columns.length" class="bg-gray-50">
            <tr>
              <th
                v-for="(column, key) in columns"
                :key="key"
                class="
                  relative
                  px-6
                  py-3
                  text-left text-xs
                  font-medium
                  text-gray-500
                  uppercase
                  tracking-wider
                "
                @click="changeSortingMod(column.field)"
              >
                {{ column.label }}
                <div class="absolute right-2 top-1/2 -translate-y-1/2">
                  <div
                    :class="{
                      active:
                        (sortingMod.name == column.field &&
                          sortingMod.type == 'Ascending') ||
                        sortingMod.name == null,
                    }"
                    class="arrow-up"
                  ></div>
                  <div class="h-1"></div>
                  <div
                    :class="{
                      active:
                        (sortingMod.name == column.field &&
                          sortingMod.type == 'Descending') ||
                        sortingMod.name == null,
                    }"
                    class="arrow-down"
                  ></div>
                </div>
              </th>
            </tr>
          </thead>
          <tbody class="bg-white divide-y divide-gray-200 text-left">
            <table-rows v-if="data.length" :data="data" />
            <tr v-else>
              <td class="px-6 py-1 h-8">-</td>
              <td class="px-6 py-1 h-8">-</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import TableRows from "./TableRows.vue";

export default {
  components: { TableRows },
  props: {
    columns: {
      type: Array,
      default: () => [],
    },
    data: {
      type: Array,
      default: () => [],
    },
    sortingMod: {},
  },
  data() {
    return {
      id: this._uid,
      isExpanded: false,
    };
  },
  computed: {
    sortingModLocal: {
      get() {
        return this.sortingMod || null;
      },

      set(newValue) {
        this.$emit("sortingModInput", newValue);
      },
    },
  },
  methods: {
    changeSortingMod(colName) {
      if (colName == this.sortingModLocal.name) {
        this.sortingModLocal.type =
          this.sortingModLocal.type == "Descending"
            ? "Ascending"
            : "Descending";
      } else {
        this.sortingModLocal.name = colName;
        this.sortingModLocal.type = "Descending";
      }
    },
  },
};
</script>

<style lang='scss'>
.arrow-up {
  width: 0;
  height: 0;
  border-left: 5px solid transparent;
  border-right: 5px solid transparent;

  border-bottom: 5px solid transparent;

  &.active {
    border-bottom: 5px solid black;
  }
}

.arrow-down {
  width: 0;
  height: 0;
  border-left: 5px solid transparent;
  border-right: 5px solid transparent;

  border-top: 5px solid transparent;

  &.active {
    border-top: 5px solid black;
  }
}
</style>
