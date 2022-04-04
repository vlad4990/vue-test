<template>
    <div class="collapse-wrapper contents">
      <input
        class="hidden"
        type="checkbox"
        v-model="toggle"
        :id="'collapse-' + id"
      />

      <slot name="toggleButton" :id="id" :status="toggle"></slot>
      <VueAccordion :expanded="toggle">
        <slot></slot>
      </VueAccordion>
    </div>
</template>

<script>
import VueAccordion from "@ztrehagem/vue-accordion";

export default {
  components: {
    VueAccordion,
  },
  data() {
    return {
      id: this._uid,
      toggle: false,
    };
  },
};
</script>

<style lang='scss'>
</style>