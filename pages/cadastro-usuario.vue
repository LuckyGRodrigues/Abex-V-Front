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
                  v-model="usuario.telefone"
                  placeholder="Telefone"
                  item-title="label" 
                  item-value="cpf"
                />
              </v-col>
            </v-row>
            <v-row>
              <v-col>
                <v-autocomplete 
                  v-model="usuario.idPessoa"
                  :items="pessoas" 
                  placeholder="Pessoa vinculada"
                  item-title="nome" 
                  item-value="id"
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
                  v-model="usuario.telefone"
                  placeholder="Nome"
                  item-title="label" 
                  item-value="cpf"
                />
              </v-col>
            </v-row>
            <v-row>
              <v-col>
                <v-text-field
                  v-model="usuario.idPessoa"
                  placeholder="Nome"
                  item-title="label" 
                  item-value="cpf"
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
          pessoas: [],
          ativo: false,
          ativo2: false,
          loading: true,
          textoUsuario: null,
          search: "",
          usuario: {
            nome: null,
            email: null,
            senha: null,
            telefone: null,
            idPessoa: null,
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
        await this.getPessoas();
        await this.getItems();
      },

      methods: {
        resetUsuario() {
          this.usuario = {
            nome: null,
            email: null,
            senha: null,
            telefone: null,
            idPessoa: null,
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
          this.usuario = {
            ...item,
          };
          this.ativo2 = true;
        },

        async create() {
          await this.$api.post("/usuario/create", this.usuario);
          console.log("Criando item");
          await this.getItems();
          this.resetUsuario();
        },

        async edit() {
          await this.$api.patch(`/usuario/update/${this.usuario.id}`, this.usuario);
          console.log("Editando item");
          await this.getItems();
          this.resetUsuario();
        },

        async deleteItem(item) {
          if (confirm(`Deseja deletar o registro com cpf ${item.id}`)) {
            this.loading = true;
            try {
              await this.$api.delete(`/usuario/delete/${item.id}`);
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