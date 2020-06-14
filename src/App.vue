<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <span class="mr-2">micronaut-book</span>
    </v-app-bar>

    <v-main>
      <BookTable />
    </v-main>
  </v-app>
</template>

<script>
import BookTable from "./components/BookTable";

export default {
  name: "App",

  components: {
    BookTable
  },

  data: () => ({
    //
  })
};
</script>
