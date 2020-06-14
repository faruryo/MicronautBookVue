
<template>
  <div>
    <v-data-table
      :headers="headers"
      :items="books"
      :options.sync="options"
      :loading="isLoading"
      class="elevation-1"
      :server-items-length="total"
    ></v-data-table>
  </div>
</template>


<script>
import Axios from "axios";

export default {
  name: "BookTable",

  data: () => ({
    options: {
      sortBy: ["id"],
      sortDesc: [false],
      page: 1,
      itemsPerPage: 10
    },
    isLoading: true,
    books: [],
    total: 0,
    headers: [
      { text: "id", align: "center", sortable: true, value: "id" },
      { text: "title", align: "center", sortable: true, value: "title" },
      { text: "author", align: "center", sortable: true, value: "author" }
    ]
  }),
  watch: {
    options: {
      handler() {
        this.loadList();
      },
      deep: true
    }
  },
  methods: {
    async loadList() {
      this.isLoading = true;
      try {
        const { sortBy, sortDesc, page, itemsPerPage } = this.options;
        const res = await Axios.get("/book", {
          params: {
            page: page - 1,
            size: itemsPerPage,
            sort:
              sortBy[0] === undefined
                ? null
                : sortBy[0] + (sortDesc[0] ? ",desc" : "")
          }
        });
        this.books = res.data.results;
        this.total = res.data.count;
      } catch (error) {
        alert("情報を取得できませんでした。時間をおいてやり直してください。");
      }
      this.isLoading = false;
    }
  },
  created: function() {
    this.loadList();
  }
};
</script>
