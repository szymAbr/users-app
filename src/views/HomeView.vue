<template>
  <div class="home">
    <b-container>
      <b-row align-v="center"
        ><user-table
          :endpoint="url"
          :search="true"
          :sorting="true"
          :pagination="true"
          :providedFields="fields"
        ></user-table
      ></b-row>
    </b-container>
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
};
</script>
