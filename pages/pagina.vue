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
          height="400"
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
        max-width="500"
      >
        <v-card 
          height="350" 
          width="500" 
          theme="dark"
        >
          <v-card-title>
            Criar
          </v-card-title>
          <v-card-text>
            <v-row>
              <v-col>
                <v-text-field 
                  v-model="usuario.nome"
                  placeholder="CPF"
                  item-title="label" 
                  item-value="cpf"
                />
              </v-col>
            </v-row>
            <v-row>
              <v-col>
                <v-text-field 
                  v-model="usuario.email"
                  placeholder="Email"
                  item-title="label" 
                  item-value="email"
                />
              </v-col>
            </v-row>
            <v-row>
              <v-col>
                <v-text-field 
                  v-model="usuario.senha"
                  placeholder="Senha"
                  item-title="label" 
                  item-value="email"
                />
              </v-col>
            </v-row>
            <v-row>
              <v-col>
                <v-text-field 
                  v-model="usuario.nome"
                  placeholder="Nome"
                  item-title="label" 
                  item-value="cpf"
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
          height="350" 
          width="500" 
          theme="dark"
        >
          <v-card-title>
            Editar
          </v-card-title>
          <v-card-text>
            <v-row>
              <v-col>
                <v-text-field 
                  v-model="administrador.cpf"
                  placeholder="CPF"
                  item-title="label" 
                  item-value="cpf"
                />
              </v-col>
            </v-row>
            <v-row>
              <v-col>
                <v-text-field 
                  v-model="administrador.nome"
                  placeholder="Nome"
                  item-title="label" 
                  item-value="nome"
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
          administrador: {
            cpf: null,
            nome: null,
          },
          headers: [
            {   
              title: "Nome",
              key: "nome",
            },
            {   
              title: "Nome",
              key: "nome",
            },
            {
              title: "Telefone",
              key: "telefone",
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
            this.resetUsuario();
          }
        },
        ativo2(valor) {
          if (valor == false) {
            this.resetUsuario();
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
        resetUsuario() {
          this.administrador = {
            cpf: null,
            nome: null,
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
            const response = await this.$api.get("/usuario");
            this.items = response.response;
          } catch (error) {
            console.error("Erro ao carregar itens:", error);
          } finally {
            this.loading = false;
            console.log("dados carregados");
          }
        },

        editItem(item) {
          this.administrador = {
            ...item,
          };
          this.ativo2 = true;
        },

        async create() {
          await this.$api.post("/administrador/create", this.administrador);
          console.log("Criando item");
          await this.getItems();
          this.resetUsuario();
        },

        async edit() {
          await this.$api.patch(`/administrador/update/${this.administrador.cpf}`, this.administrador);
          console.log("Editando item");
          await this.getItems();
          this.resetUsuario();
        },

        async deleteItem(item) {
          if (confirm(`Deseja deletar o registro com cpf ${item.cpf}`)) {
            this.loading = true;
            try {
              await this.$api.delete(`/administrador/delete/${item.cpf}`);
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