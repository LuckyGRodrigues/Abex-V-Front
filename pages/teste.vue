<template>
  <div>
    <v-container>
      <v-row class="my-4">
        <v-col cols="12">
          <v-btn variant="outlined" @click="showTabela = !showTabela"> 
            {{ showTabela ? 'Ocultar tabela' : 'Mostrar tabela' }}
          </v-btn>
        </v-col>
      </v-row>

      <v-dialog v-model="showTabela" max-width="1200">
        <TabelaComponent 
          titulo="Cliente"
          height="600"
          class="mt-13" 
          :items="items" 
          :headers="headers" 
          @editou="editItem" 
          @deletou="deleteItem"
          @abrir-dialog="abrirCriar" 
        />
      </v-dialog>

      <FormularioOrcamentoComponent
        :ativo="ativo"
        :modo-edicao="false"
        @fechar="fecharFormulario"
        @salvar="create"
      />

      <FormularioOrcamentoComponent
        :ativo="ativo2"
        :modo-edicao="true"
        :item-edicao="pessoaEdicao"
        @fechar="fecharFormularioEdicao"
        @salvar="edit"
      />
    </v-container>
  </div>
</template>

  <script>
    export default {
      data: () => {
        return {
          tab: 1,
          valor: 0,
          showTabela: true,
          ativo: false,
          ativo2: false,
          loading: true,
          pessoa: [],
          textoUsuario: null,
          search: "",
          pessoaEdicao: null,
          headers: [
            {   
              title: "ID",
              key: "id",
            },
            {   
              title: "Nome",
              key: "nome",
            },
            {
              title: "Empresa",
              key: "empresa",
            },
            {
              title: "",
              key: "action",  
              sortable: false,
            },
          ],
          items: [],
        };
      },

      watch: {
        resetTabela() {
          if (this.loading == false) {
            this.getItems();
          }
        }
      },

      async created() {
        await this.getItems();
      },

      methods: {
        abrirCriar() {
          this.ativo = true;
        },

        fecharFormulario() {
          this.ativo = false;
        },

        fecharFormularioEdicao() {
          this.ativo2 = false;
          this.pessoaEdicao = null;
        },

        async getItems() {
          this.loading = true;
          try {
            const response = await this.$api.get("/orcamento");
            this.items = response.response;
          } catch (error) {
            console.error("Erro ao carregar itens:", error);
          } finally {
            this.loading = false;
            console.log("dados carregados");
          }
        },
        
        async getPessoas() {
          this.loading = true;
          try {
            const response = await this.$api.get("/pessoa");
            this.pessoas = response.response;
          } catch (error) {
            console.error("Erro ao carregar itens:", error);
          } finally {
            this.loading = false;
            console.log("dados carregados");
          }
        },

        editItem(item) {
          this.pessoaEdicao = { ...item };
          this.ativo2 = true;
        },

        async create(dadosFormulario) {
          try {
            await this.$api.post("/orcamento/create", dadosFormulario);
            console.log("Criando item");
            await this.getItems();
            this.fecharFormulario();
          } catch (error) {
            console.error("Erro ao criar item:", error);
          }
        },

        async edit(dadosFormulario) {
          try {
            await this.$api.patch(`/orcamento/update/${dadosFormulario.id}`, dadosFormulario);
            console.log("Editando item");
            await this.getItems();
            this.fecharFormularioEdicao();
          } catch (error) {
            console.error("Erro ao editar item:", error);
          }
        },

        async deleteItem(item) {
          if (confirm(`Deseja deletar o registro com ID ${item.id}?`)) {
            this.loading = true;
            try {
              await this.$api.delete(`/orcamento/delete/${item.id}`);
              console.log("Deletando item");
              await this.getItems();
            } catch (error) {
              console.error("Erro ao excluir item:", error);
            } finally {
              this.loading = false;
            }
          }
        }
      },
    };
  </script>