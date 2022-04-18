<template>
  <div>
    <b-navbar toggleable="lg" type="dark" variant="dark">
      <b-navbar-brand href="#">Users</b-navbar-brand>

      <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>

      <b-collapse id="nav-collapse" is-nav>
        <!-- Right aligned nav items -->
        <b-navbar-nav class="ml-auto">
          <b-nav-form @submit.prevent="searchUsers">
            <b-form-input
              size="sm"
              class="mr-sm-2"
              placeholder="Search"
              v-model="searchText"
            ></b-form-input>
            <b-button size="sm" class="my-2 my-sm-0" type="submit"
              >Search</b-button
            >
          </b-nav-form>
        </b-navbar-nav>
      </b-collapse>
    </b-navbar>

    <!-- <b-form-input
      v-model="filter"
      type="search"
      placeholder="Type to filter data"
    ></b-form-input> -->

    <b-table
      striped
      hover
      :fields="fields"
      :items="displayedUsers"
      :per-page="perPage"
      :current-page="currentPage"
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
    ></b-pagination>
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
      // perPage: 3,
      users: [],
      displayedUsers: [],
      searchText: "",
      // fields: [
      //   {
      //     key: "name",
      //     search: this.search,
      //     sortable: this.sorting,
      //   },
      //   {
      //     key: "email",
      //     search: this.search,
      //     sortable: this.sorting,
      //     formatter: "emailLink",
      //   },
      //   {
      //     key: "company.name",
      //     label: "Company",
      //     search: this.search,
      //     sortable: this.sorting,
      //   },
      //   {
      //     key: "address.city",
      //     label: "City",
      //     search: this.search,
      //     sortable: this.sorting,
      //   },
      //   {
      //     key: "website",
      //     search: this.search,
      //     sortable: this.sorting,
      //   },
      // ],
    };
  },
  methods: {
    fetchData() {
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
        });
    },
    paginate(currentPage, perPage) {
      const start = (currentPage - 1) * perPage;
      const newUsers = this.users.slice(start, start + 3);
      this.displayedUsers = newUsers;
    },
    searchUsers() {
      // this.providedFields.forEach(field => {
      //   user.field.toLowerCase().includes(this.searchText.toLowerCase())
      // })

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

<style></style>
