<template>
  <v-data-table
    :headers="headers"
    :items="items"
    :search="search"
    theme="light"
    style="border: solid grey 4px;
    border-radius: 4px"
    @delete-item="deleteItem"
    @edit-item="editItem"
  >
    <template #item.action="{ item }"> 
      <v-icon class="me-2" size="small" style="color: goldenrod;" @click="editItem(item)">
        mdi-pencil
      </v-icon>
      <v-icon size="small" color="error]" style="color: violet;" @click="deleteItem(item)">
        mdi-delete
      </v-icon>
    </template>
    <template #top>
      <v-toolbar flat>
        <v-toolbar-title> {{ titulo }} </v-toolbar-title>
        <v-btn
          style="color: white; background-color: brown; font-size: large;"
          @click="abrirDialog()"
        >
          Criar
        </v-btn>
      </v-toolbar>
      <v-text-field
        v-model="search"
        label="Search"
        prepend-inner-icon="mdi-magnify"
        variant="outlined"
        hide-details
        single-line
      />
    </template>
  </v-data-table>
</template>

<script>
export default {
  name: "TabelaComponent",
  props: {
    titulo: {
      type: String,
      default: null,
    },
    headers: {
      type: Array,
      default: null,
    },
    items: {
      type: Array,
      default: null,
    },
  },

  emits: ["editou", "deletou", "abrir-dialog", "dialog-edit"],

  data() {
    return {
      search: "",
    };
  },

  methods: {
    editItem(item) {
      this.$emit("editou", item);
    },
    deleteItem(item) {
      this.$emit("deletou", item);
    },
    abrirDialog() {
      this.$emit("abrir-dialog");
    },
    dialogEdit() {
      this.$emit("dialog-edit");
    },
  },
};
</script>