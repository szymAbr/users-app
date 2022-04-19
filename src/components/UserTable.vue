<template>
  <div class="user-table py-4">
    <div class="page" v-if="showSpinner">
      <b-spinner class="spinner" variant="primary" key="primary"></b-spinner>
    </div>

    <b-container
      ><b-navbar type="dark" variant="dark">
        <b-navbar-brand href="#">Users</b-navbar-brand>

        <b-navbar-nav class="ml-auto">
          <b-nav-form @submit.prevent="searchUsers">
            <b-form-input
              size="sm"
              class="mr-sm-2"
              placeholder="Search by name"
              v-model="searchText"
            ></b-form-input>

            <b-button size="sm" class="my-2 my-sm-0" type="submit"
              >Search</b-button
            >
          </b-nav-form>
        </b-navbar-nav>
      </b-navbar>

      <b-table
        striped
        hover
        caption-top
        stacked="md"
        :fields="fields"
        :items="displayedUsers"
        :per-page="perPage"
        :current-page="currentPage"
        @change="$emit('change', displayedUsers)"
      >
        <template #cell(email)="data">
          <a :href="`mailto:${data.value}`">{{ data.value }}</a>
        </template>
      </b-table>

      <b-pagination
        v-model="currentPage"
        :total-rows="rows"
        :per-page="perPage"
        first-text="First"
        prev-text="Prev"
        next-text="Next"
        last-text="Last"
        @change="$emit('change', displayedUsers)"
      ></b-pagination
    ></b-container>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "UserTable",
  props: ["endpoint", "search", "sorting", "pagination", "providedFields"],
  data() {
    return {
      currentPage: 1,
      users: [],
      displayedUsers: [],
      searchText: "",
      showSpinner: false,
    };
  },
  methods: {
    fetchData() {
      this.showSpinner = true;

      axios
        .get(this.endpoint)
        .then((response) => {
          this.users = response.data;
          this.displayedUsers = response.data;
        })
        .catch((error) => {
          console.log(error);
        })
        .finally(() => {
          console.log("data fetch complete");
          this.showSpinner = false;
        });
    },
    paginate(currentPage, perPage) {
      const start = (currentPage - 1) * perPage;
      const newUsers = this.users.slice(start, start + 3);
      this.displayedUsers = newUsers;
    },
    searchUsers() {
      const filteredUsers = this.users.filter((user) =>
        user.name.toLowerCase().includes(this.searchText.toLowerCase())
      );

      this.displayedUsers = filteredUsers;
    },
    emailLink(value) {
      return value;
    },
  },
  mounted() {
    this.fetchData();
  },
  computed: {
    perPage() {
      if (this.pagination) {
        return 3;
      }

      return 10;
    },
    fields() {
      let fieldArray = [];

      this.providedFields.forEach((field) => {
        let fieldObject = {
          key: field,
          search: this.search,
          sortable: this.sorting,
        };

        if (field.indexOf(".") !== -1) {
          const newString = field.substring(0, field.indexOf("."));

          fieldObject = {
            ...fieldObject,
            label: newString.charAt(0).toUpperCase() + newString.slice(1),
          };
        }

        if (field === "email") {
          fieldObject = {
            ...fieldObject,
            formatter: "emailLink",
          };
        }

        fieldArray.push(fieldObject);
      });

      return fieldArray;
    },
    rows() {
      return this.displayedUsers.length;
    },
  },
};
</script>

<style scoped>
.user-table {
  max-width: 100%;
}

.page {
  position: absolute;
  background: rgba(0, 0, 0, 0.3);
  z-index: 25;
  width: 100%;
  height: 100%;
}

.spinner {
  position: relative;
  top: 50%;
}
</style>
