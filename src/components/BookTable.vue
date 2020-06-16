
<template>
  <div>
    <v-data-table
      :headers="headers"
      :items="books"
      :options.sync="options"
      :loading="isLoading"
      class="elevation-1"
      :server-items-length="total"
    >
      <template v-slot:top>
        <v-toolbar flat color="white">
          <v-toolbar-title>My Book List</v-toolbar-title>
          <v-divider class="mx-4" inset vertical></v-divider>
          <v-spacer></v-spacer>
          <v-dialog v-model="dialog" max-width="500px">
            <template v-slot:activator="{ on, attrs }">
              <v-btn color="primary" dark class="mb-2" v-bind="attrs" v-on="on">New Book</v-btn>
            </template>
            <v-card>
              <v-card-title>
                <span class="headline">{{ formTitle }}</span>
              </v-card-title>

              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field v-model="editedBook.title" label="Title"></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field v-model="editedBook.author" label="Author"></v-text-field>
                    </v-col>
                  </v-row>
                </v-container>
              </v-card-text>

              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="close">Cancel</v-btn>
                <v-btn color="blue darken-1" text @click="save">Save</v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-toolbar>
      </template>
      <template v-slot:item.actions="{ item }">
        <v-icon small class="mr-2" @click="editBook(item)">mdi-pencil</v-icon>
        <v-icon small @click="deleteBook(item)">mdi-delete</v-icon>
      </template>
    </v-data-table>
  </div>
</template>


<script>
import Axios from "axios";

export default {
  name: "BookTable",

  data: () => ({
    dialog: false,
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
      { text: "ID", align: "center", sortable: true, value: "id" },
      { text: "Title", align: "center", sortable: true, value: "title" },
      { text: "Author", align: "center", sortable: true, value: "author" },
      { text: "Actions", align: "center", sortable: false, value: "actions" }
    ],
    editedBook: {
      title: "",
      author: ""
    },
    defaultBook: {
      title: "",
      author: ""
    }
  }),

  computed: {
    formTitle() {
      return this.editedId === -1 ? "New Book" : "Edit Book";
    }
  },

  watch: {
    dialog(val) {
      val || this.close();
    },
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
    },

    async editBook(book) {
      this.editedId = book.id;
      this.editedBook = Object.assign({}, book);
      console.log(book);
      this.dialog = true;
    },

    async deleteBook(book) {
      console.log(book);
      if (
        confirm(
          `削除しますか？ID=${book.id},Title=${book.title},Author=${book.author}`
        )
      ) {
        await Axios.delete(`/book/${book.id}`);
        await this.loadList();
      }
    },

    async close() {
      this.dialog = false;
      this.$nextTick(() => {
        this.editedBook = Object.assign({}, this.defaultItem);
        this.editedId = -1;
      });
    },

    async save() {
      if (this.editedId > -1) {
        await Axios.put(`/book/${this.editedBook.id}`, this.editedBook);
      } else {
        await Axios.post("/book", this.editedBook);
      }
      await this.loadList();
      this.close();
    }
  },
  created: function() {
    this.loadList();
  }
};
</script>
