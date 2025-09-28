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
          titulo="Administradores"
          height="600"
          class="mt-13" 
          :items="items" 
          :headers="headers" 
          @editou="editItem" 
          @deletou="deleteItem"
          @abrir-dialog="abrirCriarFromChild" 
          @dialog-edit="editarFromChild"
        />
      </v-dialog>
      <v-dialog 
        v-model="ativo"
        max-width="400"
      >
        <v-card 
          height="650" 
          width="400" 
          theme="dark"
        >
          <v-card-title>
            Criar
          </v-card-title>
          <v-card-text>
            <v-row>
              <v-col>
                <v-text-field 
                  v-model="pessoa.nome"
                  placeholder="Nome"
                  item-title="label" 
                  item-value="nome"
                />
              </v-col>
            </v-row>
            <v-row>
              <v-col>
                <v-text-field 
                  v-model="pessoa.email"
                  placeholder="Email"
                  item-title="label" 
                  item-value="email"
                />
              </v-col>
            </v-row>
            <v-row>
              <v-col>
                <v-text-field 
                  v-model="pessoa.telefone"
                  placeholder="Telefone"
                  item-title="label" 
                  item-value="telefone"
                />
              </v-col>
            </v-row>
            <v-row>
              <v-col>
                <v-text-field 
                  v-model="pessoa.empresa"
                  placeholder="Empresa"
                  item-title="label" 
                  item-value="empresa"
                />
              </v-col>
            </v-row>
            <v-row>
              <v-col>
                <v-text-field 
                  v-model="pessoa.cidade"
                  placeholder="Cidade"
                  item-title="label" 
                  item-value="cidade"
                />
              </v-col>
            </v-row>
          </v-card-text>
          <v-card-actions>
            <v-btn variant="outlined" @click="create()">
              Criar
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>

      <v-dialog 
        v-model="ativo2"
        max-width="500"
      >
        <v-card 
          height="650" 
          width="400" 
          theme="dark"
        >
          <v-card-title>
            Editar
          </v-card-title>
          <v-card-text>
            <v-row>
              <v-col>
                <v-text-field 
                  v-model="pessoa.nome"
                  placeholder="Nome"
                  item-title="label" 
                  item-value="nome"
                />
              </v-col>
            </v-row>
            <v-row>
              <v-col>
                <v-text-field 
                  v-model="pessoa.email"
                  placeholder="Email"
                  item-title="label" 
                  item-value="email"
                />
              </v-col>
            </v-row>
            <v-row>
              <v-col>
                <v-text-field 
                  v-model="pessoa.telefone"
                  placeholder="Telefone"
                  item-title="label" 
                  item-value="telefone"
                />
              </v-col>
            </v-row>
            <v-row>
              <v-col>
                <v-text-field 
                  v-model="pessoa.empresa"
                  placeholder="Empresa"
                  item-title="label" 
                  item-value="empresa"
                />
              </v-col>
            </v-row>
            <v-row>
              <v-col>
                <v-text-field 
                  v-model="pessoa.cidade"
                  placeholder="Cidade"
                  item-title="label" 
                  item-value="cidade"
                />
              </v-col>
            </v-row>
          </v-card-text>

          <v-card-actions>
            <v-btn variant="outlined" @click="edit()">
              Editar
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
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
          textoUsuario: null,
          search: "",
          pessoa: {
            nome: null,
            email: null,
            telefone: null,
            empresa: null,
            cidade: null,
          },
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
        ativo(valor) {
          if (valor == false) {
            this.resetPessoa();
          }
        },
        ativo2(valor) {
          if (valor == false) {
            this.resetPessoa();
          }
        },
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
        resetPessoa() {
          this.pessoa = {
            nome: null,
            email: null,
            telefone: null,
            empresa: null,
            cidade: null,
          };
          this.ativo = false;
          this.ativo2 = false;
        },

        abrirCriar() {
          this.ativo = true;
        },

        abrirCriarFromChild() {
          this.ativo = true;
        },

        editarFromChild(item) {
          if (item) {
            this.administrador = { ...item };
          }
          this.ativo2 = true;
        },

        async getItems() {
          this.loading = true;
          try {
            const response = await this.$api.get("/pessoa");
            this.items = response.response;
          } catch (error) {
            console.error("Erro ao carregar itens:", error);
          } finally {
            this.loading = false;
            console.log("dados carregados");
          }
        },

        editItem(item) {
          this.pessoa = {
            ...item,
          };
          this.ativo2 = true;
        },

        async create() {
          await this.$api.post("/pessoa/create", this.pessoa);
          console.log("Criando item");
          await this.getItems();
          this.resetPessoa();
        },

        async edit() {
          await this.$api.patch(`/pessoa/update/${this.pessoa.id}`, this.pessoa);
          console.log("Editando item");
          await this.getItems();
          this.resetPessoa();
        },

        async deleteItem(item) {
          if (confirm(`Deseja deletar o registro com cpf ${item.id}`)) {
            this.loading = true;
            try {
              await this.$api.delete(`/pessoa/delete/${item.id}`);
            } catch (error) {
              console.error("Erro ao excluir item:", error);
            }
            console.log("Deletando item");
            await this.getItems();
            this.loading = false;
          }
        }
      },
    };
  </script>