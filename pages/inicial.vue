<template>
  <v-app>
    <v-navigation-drawer
      v-model="drawer"
      :permanent="!mobile"
      :temporary="mobile"
      app
      color="grey-darken-4"
      dark
      width="240"
    >
      <v-list-item class="px-2">
        <v-list-item-title class="text-h6 font-weight-bold">
          Sistema
        </v-list-item-title>
      </v-list-item>

      <v-divider />

      <v-list density="compact" nav>
        <v-list-item
          v-for="item in menuItems"
          :key="item.title"
          :prepend-icon="item.icon"
          :title="item.title"
          link
          @click="handleNavigation(item.route)"
        />
      </v-list>
    </v-navigation-drawer>

    <v-app-bar
      v-if="mobile"
      app
      color="grey-darken-4"
      dark
      elevation="1"
    >
      <v-app-bar-nav-icon @click="drawer = !drawer" />
      <v-toolbar-title>Sistema de Gestão</v-toolbar-title>
    </v-app-bar>

    <v-main>
      <v-container fluid class="pa-6">
        <div class="text-center mb-8">
          <h1 class="display-1 font-weight-bold text-grey-darken-3 mb-4">
            Bem vindo, {{ userName }}!
          </h1>
        </div>

        <h2 class="text-h4 font-weight-bold text-grey-darken-3 mb-6">
          Atalhos
        </h2>

        <v-row>
          <v-col
            v-for="shortcut in shortcuts"
            :key="shortcut.title"
            cols="12"
            sm="6"
            md="4"
            lg="3"
          >
            <v-card
              class="shortcut-card"
              elevation="4"
              hover
              @click="handleNavigation(shortcut.route)"
            >
              <v-card-text class="text-center pa-6">
                <v-icon
                  :icon="shortcut.icon"
                  size="64"
                  color="grey-darken-2"
                  class="mb-4"
                />
                <h3 class="text-h6 font-weight-bold mb-2">
                  {{ shortcut.title }}
                </h3>
                <p class="text-body-2 text-grey-darken-1">
                  {{ shortcut.description }}
                </p>
              </v-card-text>
            </v-card>
          </v-col>
        </v-row>

        <!-- Seção de Cadastro de Pessoas -->
        <v-divider class="my-8" />

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

        <FormularioComponent
          :ativo="ativo"
          :modo-edicao="false"
          @fechar="fecharFormulario"
          @salvar="create"
        />

        <FormularioComponent
          :ativo="ativo2"
          :modo-edicao="true"
          :item-edicao="pessoaEdicao"
          @fechar="fecharFormularioEdicao"
          @salvar="edit"
        />

        <!-- Seção de Usuários -->
        <v-divider class="my-8" />

        <v-dialog v-model="showTabelaUsuarios" max-width="1200">
          <TabelaComponent 
            titulo="Usuário"
            height="600"
            class="mt-13" 
            :items="itemsUsuarios" 
            :headers="headersUsuarios" 
            @editou="editItemUsuario" 
            @deletou="deleteItemUsuario"
            @abrir-dialog="abrirCriarUsuario" 
          />
        </v-dialog>

        <!-- Formulário para criar usuário -->
        <FormularioUsuarioComponent
          :ativo="ativoUsuario"
          :modo-edicao="false"
          :pessoas="pessoas"
          @fechar="fecharFormularioUsuario"
          @salvar="createUsuario"
        />

        <!-- Formulário para editar usuário -->
        <FormularioUsuarioComponent
          :ativo="ativoUsuario2"
          :modo-edicao="true"
          :item-edicao="usuarioEdicao"
          :pessoas="pessoas"
          @fechar="fecharFormularioEdicaoUsuario"
          @salvar="editUsuario"
        />

      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import TabelaComponent from '~/components/TabelaComponent.vue';
import FormularioComponent from '~/components/FormularioComponent.vue';
import FormularioUsuarioComponent from '~/components/FormularioUsuarioComponent.vue';

export default {
  components: {
    TabelaComponent,
    FormularioComponent,
    FormularioUsuarioComponent,
  },
  
  data() {
    return {
      drawer: false, // Inicializa como false
      userName: 'Fulano',
      mobile: false,
      // Dados do cadastro de pessoas (migrados do cadastroPessoa.vue)
      tab: 1,
      valor: 0,
      showTabela: false,
      ativo: false,
      ativo2: false,
      loading: false,
      textoUsuario: null,
      search: "",
      pessoaEdicao: null,
      // Dados do cadastro de usuários
      showTabelaUsuarios: false,
      ativoUsuario: false,
      ativoUsuario2: false,
      usuarioEdicao: null,
      pessoas: [],
      itemsUsuarios: [],
      headersUsuarios: [
        {   
          title: "ID",
          key: "id",
        },
        {   
          title: "Nome",
          key: "nome",
        },
        {   
          title: "E-mail",
          key: "email",
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
      // Dados originais da tela inicial
      navigationItems: [
        {
          title: 'Clientes',
          description: 'Consulte, adicione e gerencie sua base de clientes.',
          icon: 'mdi-account-group',
          route: '/clientes'
        },
        {
          title: 'Obras',
          description: 'Acompanhe o andamento de todas as suas obras e projetos.',
          icon: 'mdi-hammer-wrench',
          route: '/obras'
        },
        {
          title: 'Financeiro',
          description: 'Controle o fluxo de caixa, contas a pagar e a receber.',
          icon: 'mdi-cash-multiple',
          route: '/financeiro'
        },
        {
          title: 'Fornecedores',
          description: 'Gerencie seus contatos e contratos com fornecedores.',
          icon: 'mdi-truck-delivery',
          route: '/fornecedores'
        },
        {
          title: 'Viagens',
          description: 'Organize e registre as viagens corporativas da equipe.',
          icon: 'mdi-airplane',
          route: '/viagens'
        },
        {
          title: 'Usuários',
          description: 'Administre os perfis e permissões dos usuários do sistema.',
          icon: 'mdi-account-cog',
          route: '/usuarios'
        },
        {
          title: 'Configurações',
          description: 'Ajuste as preferências e parâmetros gerais do sistema.',
          icon: 'mdi-cog',
          route: '/configuracoes'
        }
      ]
    };
  },

  computed: {
    menuItems() {
      return this.navigationItems;
    },
    shortcuts() {
      return this.navigationItems;
    }
  },

  watch: {
    resetTabela() {
      if (this.loading == false) {
        this.getItems();
      }
    }
  },

  async created() {
    this.checkMobile();
    // Só carrega os dados se necessário, para evitar erro na inicialização
    // await this.getItems();
  },

  mounted() {
    // Configura o drawer após o componente estar montado
    this.drawer = !this.mobile;
    window.addEventListener('resize', this.checkMobile);
  },

  beforeUnmount() {
    window.removeEventListener('resize', this.checkMobile);
  },

  methods: {
    checkMobile() {
      this.mobile = window.innerWidth < 960;
      // Atualiza o drawer baseado no estado mobile
      if (this.$el) { // Verifica se o componente está montado
        this.drawer = !this.mobile;
      }
    },

    async handleNavigation(route) {
      // Se for a rota de clientes, abre/fecha a tabela ao invés de navegar
      if (route === '/clientes') {
        this.showTabela = !this.showTabela;
        // Se estiver abrindo a tabela e não tem dados, carrega os dados
        if (this.showTabela && this.items.length === 0) {
          try {
            await this.getItems();
          } catch (error) {
            console.error('Erro ao carregar dados:', error);
          }
        }
      } else if (route === '/usuarios') {
        // Se for a rota de usuários, abre/fecha a tabela de usuários
        this.showTabelaUsuarios = !this.showTabelaUsuarios;
        // Se estiver abrindo a tabela e não tem dados, carrega os dados
        if (this.showTabelaUsuarios && this.itemsUsuarios.length === 0) {
          try {
            await this.getItemsUsuarios();
            await this.getPessoas(); // Carrega pessoas para o autocomplete
          } catch (error) {
            console.error('Erro ao carregar dados de usuários:', error);
          }
        }
      } else {
        // Para outras rotas, navega normalmente
        try {
          await this.$router.push(route);
        } catch (error) {
          console.error('Erro na navegação:', error);
        }
      }
    },

    // Métodos migrados do cadastroPessoa.vue
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
      this.pessoaEdicao = { ...item };
      this.ativo2 = true;
    },

    async create(dadosFormulario) {
      try {
        await this.$api.post("/pessoa/create", dadosFormulario);
        console.log("Criando item");
        await this.getItems();
        this.fecharFormulario();
      } catch (error) {
        console.error("Erro ao criar item:", error);
      }
    },

    async edit(dadosFormulario) {
      try {
        await this.$api.patch(`/pessoa/update/${dadosFormulario.id}`, dadosFormulario);
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
          await this.$api.delete(`/pessoa/delete/${item.id}`);
          console.log("Deletando item");
          await this.getItems();
        } catch (error) {
          console.error("Erro ao excluir item:", error);
        } finally {
          this.loading = false;
        }
      }
    },

    // Métodos para usuários
    abrirCriarUsuario() {
      this.ativoUsuario = true;
    },

    fecharFormularioUsuario() {
      this.ativoUsuario = false;
    },

    fecharFormularioEdicaoUsuario() {
      this.ativoUsuario2 = false;
      this.usuarioEdicao = null;
    },

    async getItemsUsuarios() {
      this.loading = true;
      try {
        const response = await this.$api.get("/usuario");
        this.itemsUsuarios = response.response;
      } catch (error) {
        console.error("Erro ao carregar usuários:", error);
      } finally {
        this.loading = false;
        console.log("usuários carregados");
      }
    },

    async getPessoas() {
      this.loading = true;
      try {
        const response = await this.$api.get("/pessoa");
        this.pessoas = response.response;
      } catch (error) {
        console.error("Erro ao carregar pessoas:", error);
      } finally {
        this.loading = false;
        console.log("pessoas carregadas");
      }
    },

    editItemUsuario(item) {
      this.usuarioEdicao = { ...item };
      this.ativoUsuario2 = true;
    },

    async createUsuario(dadosFormulario) {
      try {
        await this.$api.post("/usuario/create", dadosFormulario);
        console.log("Criando usuário");
        await this.getItemsUsuarios();
        this.fecharFormularioUsuario();
      } catch (error) {
        console.error("Erro ao criar usuário:", error);
      }
    },

    async editUsuario(dadosFormulario) {
      try {
        await this.$api.patch(`/usuario/update/${dadosFormulario.id}`, dadosFormulario);
        console.log("Editando usuário");
        await this.getItemsUsuarios();
        this.fecharFormularioEdicaoUsuario();
      } catch (error) {
        console.error("Erro ao editar usuário:", error);
      }
    },

    async deleteItemUsuario(item) {
      if (confirm(`Deseja deletar o usuário com ID ${item.id}?`)) {
        this.loading = true;
        try {
          await this.$api.delete(`/usuario/delete/${item.id}`);
          console.log("Deletando usuário");
          await this.getItemsUsuarios();
        } catch (error) {
          console.error("Erro ao excluir usuário:", error);
        } finally {
          this.loading = false;
        }
      }
    }
  }
};
</script>

<style scoped>
.shortcut-card {
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  cursor: pointer;
}

.shortcut-card:hover {
  transform: translateY(-4px) scale(1.02);
}

.v-main {
  background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
  min-height: 100vh;
}

.display-1 {
  background: linear-gradient(45deg, #2c3e50, #34495e);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}
</style>
