<template>
  <div>
    <div class="flex justify-between">
      <label>Добавление пользователя</label>
      <button @click="$emit('close')" class="cursor-pointer">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          class="h-6 w-6"
          fill="none"
          viewBox="0 0 24 24"
          stroke="currentColor"
          stroke-width="2"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            d="M6 18L18 6M6 6l12 12"
          />
        </svg>
      </button>
    </div>
    <div class="flex my-4 items-center">
      <label class="w-2/5 text-left">Имя</label>
      <input
        class="w-3/5 border border-gray-300 rounded px-2 py-1"
        :class="{
          'border-red-500': errors.includes('name'),
        }"
        @input="
          errors.splice(
            errors.findIndex((i) => i == 'name'),
            1
          )
        "
        v-model="createdUser.name"
        type="text"
      />
    </div>
    <div class="flex my-4 items-center">
      <label class="w-2/5 text-left">Телефон</label>

      <phone-mask-input
        v-model="createdUser.phone"
        wrapperClass="w-3/5"
        defaultCountry="ru"
        :inputClass="
          'w-full border border-gray-300 rounded px-2 py-1 ' +
          (errors.includes('phone') && 'border-red-500')
        "
        @input="
          errors.splice(
            errors.findIndex((i) => i == 'phone'),
            1
          )
        "
      />
    </div>
    <div class="flex my-4 items-center">
      <label class="w-2/5 text-left">Начальник</label>
      <v-select
        :options="chiefs"
        label="name"
        v-model="createdUser.chief"
        id="user__form_select"
        class="w-3/5"
      />
    </div>
    <button
      @click="addUser"
      class="
        w-full
        bg-blue-600
        text-white text-sm
        leading-6
        font-medium
        py-1
        px-3
        rounded
      "
    >
       Сохранить
    </button>
    <transition name="fade">
      <div
        v-if="errors.length"
        class="
          absolute
          -bottom-12
          w-full
          bg-white
          -ml-5
          rounded-lg
          text-red-500
          py-1
        "
      >
        Заполните все обязательные поля
      </div>
    </transition>
  </div>
</template>

<script>
import vSelect from "vue-select";
import "vue-select/dist/vue-select.css";

import PhoneMaskInput from "vue-phone-mask-input";

export default {
  components: { vSelect, PhoneMaskInput },
  props: { chiefs: {} },
  data() {
    return {
      createdUser: {
        name: "",
        phone: "",
        chief: null,
      },
      errors: [],
    };
  },
  methods: {
    addUser() {
      if (!this.createdUser.name) this.errors.push("name");
      if (this.createdUser.phone.length < 3) this.errors.push("phone");
      if (this.errors.length) return;
      this.$emit("addUser", this.createdUser);
    },
  },
};
</script>

<style lang="scss">
#user__form_select {
  &.v-select.vs--open {
    .vs__dropdown-toggle {
      border-color: #015fcc;
      border-width: 2px;
      padding: 0 0 4px;
    }
  }
  .vs__dropdown-toggle {
    padding: 1px 1px 5px;
    height: 34px;
    border-color: #d1d5db;
  }
}

.collapse-wrapper {
  .vue-accordion {
    display: contents;
    .vue-accordion__inner {
      display: contents;
    }
  }
}
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active до версии 2.1.8 */ {
  opacity: 0;
}
</style>