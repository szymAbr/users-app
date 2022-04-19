<template>
  <div class="home my-4">
    <user-table
      :endpoint="url"
      :search="true"
      :sorting="true"
      :pagination="true"
      :providedFields="fields"
      @change="logChange"
    ></user-table>
  </div>
</template>

<script>
import UserTable from "@/components/UserTable.vue";

export default {
  name: "HomeView",
  components: {
    "user-table": UserTable,
  },
  data() {
    return {
      url: "https://jsonplaceholder.typicode.com/users",
      fields: ["name", "email", "company.name", "address.city", "website"],
    };
  },
  methods: {
    logChange(event) {
      console.log(event);
    },
  },
};
</script>

<style scoped>
.home {
  max-width: 100%;
}
</style>
