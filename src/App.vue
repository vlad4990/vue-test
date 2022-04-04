<template>
  <div id="app">
    <button
      class="h-10 px-6 font-semibold rounded-md bg-black text-white"
      @click="isFormOpen = !isFormOpen"
    >
      Добавить сотрудника
    </button>
    <user-table
      :columns="tableColumns"
      :data="sortedUsers"
      :sortingMod="sortingMod"
      @sortingModInput="
        (val) => {
          sortingMod = val;
          setSortingMod();
        }
      "
    />
    <button
      class="h-10 px-6 font-semibold rounded-md bg-red-500 text-white"
      @click="resetTable"
    >
      Обнулить Таблицу
    </button>
    <button
      class="h-10 px-6 font-semibold rounded-md bg-gray-500 text-white ml-10"
      @click="resetSorting"
    >
      Сбросить фильтр
    </button>
    <modal-window v-if="isFormOpen" @close="isFormOpen = false">
      <user-form
        @close="isFormOpen = false"
        @addUser="addUserHandler"
        :chiefs="allUsers"
      />
    </modal-window>
  </div>
</template>

<script>
import ModalWindow from "./components/ModalWindow.vue";
import UserForm from "./components/UserForm.vue";
import UserTable from "./components/UserTable.vue";

export default {
  name: "App",
  components: {
    UserTable,
    ModalWindow,
    UserForm,
  },
  data() {
    return {
      tableColumns: [
        { field: "name", label: "Имя" },
        { field: "phone", label: "Телефон" },
      ],
      users: JSON.parse(window.localStorage.getItem("app-users")) || [],
      isFormOpen: false,
      sortingMod: JSON.parse(window.localStorage.getItem("app-sorting")) || {
        name: null,
        type: "Descending",
      },
    };
  },
  methods: {
    addUserHandler(createdUser) {
      this.isFormOpen = false;
      if (!createdUser.chief) {
        this.users.push({ name: createdUser.name, phone: createdUser.phone });
        return;
      }
      let chief = null;
      const findElement = (arr) => {
        arr.find((user) => {
          if (
            user.name == createdUser.chief.name &&
            user.phone == createdUser.chief.phone
          )
            chief = user;
          if (user.children) findElement(user.children);
        });
      };
      findElement(this.users);
      if (chief) {
        if (!chief.children) this.$set(chief, "children", []);
        chief.children.push({
          name: createdUser.name,
          phone: createdUser.phone,
        });
      }
      window.localStorage.setItem("app-users", JSON.stringify(this.users));
    },
    resetTable() {
      window.localStorage.setItem("app-users", []);
      this.users = [];
    },
    resetSorting() {
      window.localStorage.setItem("app-sorting", []);
      this.sortingMod = {
        name: null,
        type: "Descending",
      };
    },
  },
  computed: {
    allUsers() {
      let users = [];
      const flatUsers = (arr) => {
        arr.forEach((user) => {
          users.push({ name: user.name, phone: user.phone });
          if (user.children) {
            flatUsers(user.children);
          }
        });
      };
      flatUsers(this.users);
      return users;
    },
    sortedUsers() {
      if (this.sortingMod.type == null) return this.users;
      const compare = (a, b) => {
        if (this.sortingMod.type == "Ascending") {
          if (
            a[this.sortingMod.name].toLowerCase() <
            b[this.sortingMod.name].toLowerCase()
          ) {
            return -1;
          }
          if (
            a[this.sortingMod.name].toLowerCase() >
            b[this.sortingMod.name].toLowerCase()
          ) {
            return 1;
          }
          return 0;
        }
        if (this.sortingMod.type == "Descending") {
          if (
            a[this.sortingMod.name].toLowerCase() <
            b[this.sortingMod.name].toLowerCase()
          ) {
            return 1;
          }
          if (
            a[this.sortingMod.name].toLowerCase() >
            b[this.sortingMod.name].toLowerCase()
          ) {
            return -1;
          }
          return 0;
        }
      };
      const sortArray = (arr) => {
        if (!this.sortingMod.name) return arr;
        const localArray = arr.sort(compare);
        localArray.forEach((user) => {
          if (user.children) {
            user.children = sortArray(user.children);
          }
        });
        return localArray;
      };
      return sortArray(this.users.slice());
    },
  },
  watch: {
    users: {
      handler() {
        window.localStorage.setItem("app-users", JSON.stringify(this.users));
      },
      deep: true,
    },
    sortingMod: {
      handler() {
        window.localStorage.setItem(
          "app-sorting",
          JSON.stringify(this.sortingMod)
        );
      },
      deep: true,
    },
  },

  mounted() {
    window.localStorage.setItem("app-users", JSON.stringify(this.users));
    window.localStorage.setItem("app-sorting", JSON.stringify(this.sortingMod));
  },
};
</script>

<style lang="scss">
@import "~@ztrehagem/vue-accordion/dist/vue-accordion.css";
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
