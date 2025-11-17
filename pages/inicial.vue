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
      app
      color="grey-darken-4"
      dark
      elevation="1"
      style="z-index: 10;"
    >
      <template v-if="mobile">
        <v-app-bar-nav-icon @click="drawer = !drawer" />
      </template>
      <v-toolbar-title>Sistema de Gestão</v-toolbar-title>
      <v-spacer />
      <v-btn class="font-weight-bold" style="margin-right: 12px;" color="white" @click="logout">
        Logout
      </v-btn>
    </v-app-bar>


    <v-main>
      <!-- adiciona padding-top para evitar que o app-bar sobreponha o conteúdo -->
      <v-container fluid class="pa-6 page-container">
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

        <!-- Dialog/subtela de Fornecedores (igual à de Cliente, com tipo=Fornecedor) -->
        <v-dialog v-model="showTabelaFornecedores" max-width="1200">
          <TabelaComponent
            titulo="Fornecedor"
            height="600"
            class="mt-13"
            :items="fornecedores"
            :headers="headers"
            @editou="openEditFornecedor"
            @deletou="deleteFornecedor"
            @abrir-dialog="abrirCriarFornecedor"
          />
        </v-dialog>

        <!-- Dialog/subtela de Colaboradores -->
        <v-dialog v-model="showTabelaColaboradores" max-width="1200">
          <TabelaComponent
            titulo="Colaborador"
            height="600"
            class="mt-13"
            :items="colaboradores"
            :headers="headers"
            @editou="openEditColaborador"
            @deletou="deleteColaborador"
            @abrir-dialog="abrirCriarColaborador"
          />
        </v-dialog>

        <!-- Dialog/subtela de Orçamentos (abre como as demais) -->
        <v-dialog v-model="showTabelaOrcamento" max-width="1200">
          <TabelaComponent 
            titulo="Orçamento"
            height="600"
            class="mt-13"
            :items="itemsOrcamento"
            :headers="headersOrcamento || headers" 
            @editou="openEditOrcamento"
            @deletou="deleteOrcamento"
            @abrir-dialog="abrirCriarOrcamento"
          />
        </v-dialog>

        <FormularioComponent
          :ativo="ativo"
          :modo-edicao="false"
          @fechar="fecharFormulario"
          @salvar="create"
        />

        <!-- Formulário Fornecedor: criar -->
        <FormularioComponent
          :ativo="ativoFornecedor"
          :modo-edicao="false"
          :default-tipo="'Fornecedor'"
          @fechar="fecharFormularioFornecedor"
          @salvar="createFornecedor"
        />

        <!-- Formulário Colaborador: criar -->
        <FormularioComponent
          :ativo="ativoColaborador"
          :modo-edicao="false"
          :default-tipo="'Colaborador'"
          @fechar="fecharFormularioColaborador"
          @salvar="createColaborador"
        />

        <FormularioComponent
          :ativo="ativo2"
          :modo-edicao="true"
          :item-edicao="pessoaEdicao"
          @fechar="fecharFormularioEdicao"
          @salvar="edit"
        />

        <!-- Formulário Fornecedor: editar -->
        <FormularioComponent
          :ativo="ativoFornecedor2"
          :modo-edicao="true"
          :item-edicao="fornecedorEdicao"
          @fechar="fecharFormularioEdicaoFornecedor"
          @salvar="editFornecedor"
        />

        <!-- Formulário Colaborador: editar -->
        <FormularioComponent
          :ativo="ativoColaborador2"
          :modo-edicao="true"
          :item-edicao="colaboradorEdicao"
          @fechar="fecharFormularioEdicaoColaborador"
          @salvar="editColaborador"
        />

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

        <FormularioUsuarioComponent
          :ativo="ativoUsuario"
          :modo-edicao="false"
          :pessoas="pessoas"
          @fechar="fecharFormularioUsuario"
          @salvar="createUsuario"
        />

        <FormularioUsuarioComponent
          :ativo="ativoUsuario2"
          :modo-edicao="true"
          :item-edicao="usuarioEdicao"
          :pessoas="pessoas"
          @fechar="fecharFormularioEdicaoUsuario"
          @salvar="editUsuario"
        />

        <!-- Formulários de Orçamento (criar e editar) -->
        <FormularioOrcamentoComponent
          :ativo="ativoOrcamento"
          :modo-edicao="false"
          :pessoas="pessoas"
          @fechar="fecharFormularioOrcamento"
          @salvar="createOrcamento"
        />

        <FormularioOrcamentoComponent
          :ativo="ativoOrcamento2"
          :modo-edicao="true"
          :item-edicao="orcamentoEdicao"
          :pessoas="pessoas"
          @fechar="fecharFormularioEdicaoOrcamento"
          @salvar="editOrcamento"
        />

      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import TabelaComponent from '~/components/TabelaComponent.vue';
import FormularioComponent from '~/components/FormularioComponent.vue';
import FormularioUsuarioComponent from '~/components/FormularioUsuarioComponent.vue';
import FormularioOrcamentoComponent from '~/components/FormularioOrcamentoComponent.vue';

export default {
  components: {
    TabelaComponent,
    FormularioComponent,
    FormularioUsuarioComponent,
    FormularioOrcamentoComponent,
  },
  
  data() {
    return {
      drawer: false,
      userName: 'Fulano',
            // Fornecedores
            showTabelaFornecedores: false,
            ativoFornecedor: false,
            ativoFornecedor2: false,
            fornecedorEdicao: null,
            // Colaboradores
            showTabelaColaboradores: false,
            ativoColaborador: false,
            ativoColaborador2: false,
            colaboradorEdicao: null,
      mobile: false,
      tab: 1,
      valor: 0,
      showTabela: false,
  showTabelaOrcamento: false,
      ativo: false,
      ativo2: false,
  ativoOrcamento: false,
  ativoOrcamento2: false,
  orcamentoEdicao: null,
      loading: false,
      textoUsuario: null,
      search: "",
      pessoaEdicao: null,
      showTabelaUsuarios: false,
      ativoUsuario: false,
      ativoUsuario2: false,
      usuarioEdicao: null,
      pessoas: [],
      itemsUsuarios: [],
      headersUsuarios: [
        { title: 'ID', key: 'id_usuario' },
        { title: 'Pessoa', key: 'id_pessoa' },
        { title: 'Nome', key: 'nome' },
        { title: 'E-mail', key: 'email' },
        { title: 'Tipo', key: 'tipo' },
        { title: '', key: 'action', sortable: false },
      ],
      headers: [
        { title: 'ID', key: 'id_pessoa' },
        { title: 'Nome', key: 'nome' },
        { title: 'E-mail', key: 'email' },
        { title: 'Telefone', key: 'telefone' },
        { title: 'Empresa', key: 'empresa' },
        { title: 'Cidade', key: 'cidade' },
        { title: '', key: 'action', sortable: false },
      ],
      items: [],
      headersOrcamento: [
        { title: 'ID', key: 'id' },
        { title: 'Descrição', key: 'descricao' },
        { title: 'Valor Estimado', key: 'valorEstimado' },
        { title: 'Data', key: 'data' },
        { title: '', key: 'action', sortable: false },
      ],
    itemsOrcamento: [],
      navigationItems: [
        {
          title: 'Clientes',
          description: 'Consulte, adicione e gerencie sua base de clientes.',
          icon: 'mdi-account-group',
          route: '/clientes'
        },
        {
          title: 'Orçamento',
          description: 'Crie e gerencie orçamentos.',
          icon: 'mdi-file-document-outline',
          route: '/orcamento'
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
            title: 'Colaborador',
            description: 'Gerencie colaboradores e seus acessos.',
            icon: 'mdi-account-tie',
            route: '/colaboradores'
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
    },
    fornecedores() {
      // filtra items que tenham tipo = Fornecedor
      return (this.items || []).filter(i => i.tipo === 'Fornecedor');
    },
    colaboradores() {
      return (this.items || []).filter(i => i.tipo === 'Colaborador');
    },
  },

  watch: {
    resetTabela() {
      if (this.loading == false) {
        this.getItems();
      }
    }
  },

  async created() {
    if (typeof window !== 'undefined') {
      this.checkMobile();
    }
  },

  mounted() {
    this.drawer = !this.mobile;
    window.addEventListener('resize', this.checkMobile);
  },

  beforeUnmount() {
    window.removeEventListener('resize', this.checkMobile);
  },

  methods: {
    checkMobile() {
      this.mobile = window.innerWidth < 960;
      if (this.$el) {
        this.drawer = !this.mobile;
      }
    },

    async handleNavigation(route) {
      if (route === '/clientes') {
        this.showTabela = !this.showTabela;
        if (this.showTabela && this.items.length === 0) {
          try {
            await this.getItems();
          } catch (error) {
            console.error('Erro ao carregar dados:', error);
          }
        }
      } else if (route === '/fornecedores') {
        this.showTabelaFornecedores = !this.showTabelaFornecedores;
        if (this.showTabelaFornecedores && this.items.length === 0) {
          try {
            await this.getItems();
          } catch (error) {
            console.error('Erro ao carregar fornecedores:', error);
          }
        }
      } else if (route === '/colaboradores') {
        this.showTabelaColaboradores = !this.showTabelaColaboradores;
        if (this.showTabelaColaboradores && this.items.length === 0) {
          try {
            await this.getItems();
          } catch (error) {
            console.error('Erro ao carregar colaboradores:', error);
          }
        }
      } else if (route === '/usuarios') {
        this.showTabelaUsuarios = !this.showTabelaUsuarios;
        if (this.showTabelaUsuarios && this.itemsUsuarios.length === 0) {
          try {
            await this.getItemsUsuarios();
            await this.getPessoas();
          } catch (error) {
            console.error('Erro ao carregar dados de usuários:', error);
          }
        }
      } else if (route === '/orcamento') {
        this.showTabelaOrcamento = !this.showTabelaOrcamento;
        if (this.showTabelaOrcamento && this.itemsOrcamento.length === 0) {
          try {
            await this.getItemsOrcamento();
            await this.getPessoas();
          } catch (error) {
            console.error('Erro ao carregar dados de orçamentos:', error);
          }
        }
      } else {
        try {
          await this.$router.push(route);
        } catch (error) {
          console.error('Erro na navegação:', error);
        }
      }
    },

    // atalho para abrir fornecedores
    async handleNavigationFornecedores(route) {
      // kept for backwards-compat if needed
      return this.handleNavigation(route);
    },

    abrirCriar() {
      this.ativo = true;
    },

    abrirCriarOrcamento() {
      this.ativoOrcamento = true;
    },

    fecharFormularioOrcamento() {
      this.ativoOrcamento = false;
    },

    fecharFormularioEdicaoOrcamento() {
      this.ativoOrcamento2 = false;
      this.orcamentoEdicao = null;
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
        this.items = (response.response || []).map(p => ({
          id_pessoa: p.id_pessoa || p.id || null,
          cpf_cnpj: p.cpf_cnpj || p.cpfCnpj || p.cpf || null,
          cpfCnpj: p.cpfCnpj || p.cpf_cnpj || p.cpf || null,
          nome: p.nome,
          email: p.email,
          telefone: p.telefone,
          empresa: p.empresa,
          cidade: p.cidade,
          tipo: p.tipo || p.tipo_pessoa || 'Cliente',
        }));
      } catch (error) {
        console.error("Erro ao carregar itens:", error);
      } finally {
        this.loading = false;
        console.log("dados carregados");
      }
    },

    async getItemsOrcamento() {
      this.loading = true;
      try {
        const response = await this.$api.get('/orcamento');
        this.itemsOrcamento = response.response;
      } catch (error) {
        console.error('Erro ao carregar orçamentos:', error);
      } finally {
        this.loading = false;
      }
    },

    openEditOrcamento(item) {
      this.orcamentoEdicao = { ...item };
      this.ativoOrcamento2 = true;
    },

    async createOrcamento(dadosFormulario) {
      try {
        const payload = {
          ...dadosFormulario,
          id_pessoa: dadosFormulario.idPessoa || dadosFormulario.id_pessoa || null,
          idPessoa: dadosFormulario.idPessoa || dadosFormulario.id_pessoa || null,
        };

        await this.$api.post('/orcamento/create', payload);
        await this.getItemsOrcamento();
        this.fecharFormularioOrcamento();
      } catch (error) {
        console.error('Erro ao criar orçamento:', error);
      }
    },

    async editOrcamento(dadosFormulario) {
      try {
        const payload = {
          ...dadosFormulario,
          id_pessoa: dadosFormulario.idPessoa || dadosFormulario.id_pessoa || null,
          idPessoa: dadosFormulario.idPessoa || dadosFormulario.id_pessoa || null,
        };
        if (payload.id_pessoa) payload.id_pessoa = Number(payload.id_pessoa);
        if (payload.idPessoa) payload.idPessoa = Number(payload.idPessoa);

        const allowed = ['Levantamento', 'Enviado', 'Aguardando Resposta', 'Fechado', 'Cancelado'];
        if (!allowed.includes(payload.status)) payload.status = 'Levantamento';

        await this.$api.patch(`/orcamento/update/${dadosFormulario.id}`, payload);
        await this.getItemsOrcamento();
        this.fecharFormularioEdicaoOrcamento();
      } catch (error) {
        console.error('Erro ao editar orçamento:', error);
      }
    },

    async deleteOrcamento(item) {
      if (confirm(`Deseja deletar o registro com ID ${item.id}?`)) {
        this.loading = true;
        try {
          await this.$api.delete(`/orcamento/delete/${item.id}`);
          await this.getItemsOrcamento();
        } catch (error) {
          console.error('Erro ao excluir orçamento:', error);
        } finally {
          this.loading = false;
        }
      }
    },

    editItem(item) {
      this.pessoaEdicao = { ...item };
      this.ativo2 = true;
    },

    async create(dadosFormulario) {
      try {
        const payload = { 
          ...dadosFormulario, 
          tipo: 'Cliente',
          cpfCnpj: dadosFormulario.cpf_cnpj || dadosFormulario.cpfCnpj || null,
        };
        await this.$api.post("/pessoa/create", payload);
        console.log("Criando item");
        await this.getItems();
        this.fecharFormulario();
      } catch (error) {
        console.error("Erro ao criar item:", error);
      }
    },

    async edit(dadosFormulario) {
      try {
        const id = dadosFormulario.id;
        const payload = { 
          ...dadosFormulario, 
          tipo: 'Cliente',
          cpfCnpj:   dadosFormulario.cpfCnpj || null,
        };
        await this.$api.patch(`/pessoa/update/${id}`, payload);
        console.log("Editando item");
        await this.getItems();
        this.fecharFormularioEdicao();
      } catch (error) {
        console.error("Erro ao editar item:", error);
      }
    },

    async deleteItem(item) {
      const idToDelete = item.id_pessoa || item.id || item.id_pessoa || null;
      if (!idToDelete) {
        alert('ID não encontrado para exclusão.');
        return;
      }

      if (confirm(`Deseja deletar o registro com ID ${idToDelete}?`)) {
        this.loading = true;
        try {
          console.log('Enviando DELETE para /pessoa/delete/', idToDelete);
          await this.$api.delete(`/pessoa/delete/${idToDelete}`);
          console.log("Deletando item");
          await this.getItems();
        } catch (error) {
          console.error("Erro ao excluir item:", error);
        } finally {
          this.loading = false;
        }
      }
    },

    abrirCriarUsuario() {
      this.ativoUsuario = true;
    },

    abrirCriarFornecedor() {
      this.ativoFornecedor = true;
    },

    fecharFormularioFornecedor() {
      this.ativoFornecedor = false;
    },

    fecharFormularioEdicaoFornecedor() {
      this.ativoFornecedor2 = false;
      this.fornecedorEdicao = null;
    },

    async createFornecedor(dadosFormulario) {
      try {
        const payload = {
          ...dadosFormulario,
          tipo: 'Fornecedor',
          cpfCnpj: dadosFormulario.cpf_cnpj || dadosFormulario.cpfCnpj || null,
        };
        await this.$api.post('/pessoa/create', payload);
        await this.getItems();
        this.fecharFormularioFornecedor();
      } catch (error) {
        console.error('Erro ao criar fornecedor:', error);
      }
    },

    async editFornecedor(dadosFormulario) {
      try {
        const id = dadosFormulario.id || dadosFormulario.id_pessoa || dadosFormulario.idPessoa;
        if (!id) {
          alert('ID do fornecedor não informado para edição.');
          return;
        }
        const payload = {
          ...dadosFormulario,
          tipo: 'Fornecedor',
          cpfCnpj: dadosFormulario.cpfCnpj || dadosFormulario.cpf_cnpj || null,
        };
        await this.$api.patch(`/pessoa/update/${id}`, payload);
        await this.getItems();
        this.fecharFormularioEdicaoFornecedor();
      } catch (error) {
        console.error('Erro ao editar fornecedor:', error);
      }
    },

    openEditFornecedor(item) {
      this.fornecedorEdicao = { 
        ...item,
        id: item.id_pessoa || item.id || item.id_pessoa,
      };
      this.ativoFornecedor2 = true;
    },

    async deleteFornecedor(item) {
      const idToDelete = item.id_pessoa || item.id || null;
      if (!idToDelete) {
        alert('ID não encontrado para exclusão.');
        return;
      }
      if (confirm(`Deseja deletar o fornecedor com ID ${idToDelete}?`)) {
        this.loading = true;
        try {
          await this.$api.delete(`/pessoa/delete/${idToDelete}`);
          await this.getItems();
        } catch (error) {
          console.error('Erro ao excluir fornecedor:', error);
        } finally {
          this.loading = false;
        }
      }
    },

    abrirCriarColaborador() {
      this.ativoColaborador = true;
    },

    fecharFormularioColaborador() {
      this.ativoColaborador = false;
    },

    fecharFormularioEdicaoColaborador() {
      this.ativoColaborador2 = false;
      this.colaboradorEdicao = null;
    },

    async createColaborador(dadosFormulario) {
      try {
        const payload = {
          ...dadosFormulario,
          tipo: 'Colaborador',
          cpfCnpj: dadosFormulario.cpf_cnpj || dadosFormulario.cpfCnpj || null,
        };
        await this.$api.post('/pessoa/create', payload);
        await this.getItems();
        this.fecharFormularioColaborador();
      } catch (error) {
        console.error('Erro ao criar colaborador:', error);
      }
    },

    async editColaborador(dadosFormulario) {
      try {
        const id = dadosFormulario.id || dadosFormulario.id_pessoa || dadosFormulario.idPessoa;
        if (!id) {
          alert('ID do colaborador não informado para edição.');
          return;
        }
        const payload = {
          ...dadosFormulario,
          tipo: 'Colaborador',
          cpfCnpj: dadosFormulario.cpfCnpj || dadosFormulario.cpf_cnpj || null,
        };
        await this.$api.patch(`/pessoa/update/${id}`, payload);
        await this.getItems();
        this.fecharFormularioEdicaoColaborador();
      } catch (error) {
        console.error('Erro ao editar colaborador:', error);
      }
    },

    async deleteColaborador(item) {
      const idToDelete = item.id_pessoa || item.id || null;
      if (!idToDelete) {
        alert('ID não encontrado para exclusão.');
        return;
      }
      if (confirm(`Deseja deletar o colaborador com ID ${idToDelete}?`)) {
        this.loading = true;
        try {
          await this.$api.delete(`/pessoa/delete/${idToDelete}`);
          await this.getItems();
        } catch (error) {
          console.error('Erro ao excluir colaborador:', error);
        } finally {
          this.loading = false;
        }
      }
    },

    openEditColaborador(item) {
      this.colaboradorEdicao = { 
        ...item,
        id: item.id_pessoa || item.id || item.id_pessoa,
      };
      this.ativoColaborador2 = true;
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
        this.itemsUsuarios = (response.response || []).map(u => ({
          id_usuario: u.id_usuario || u.id || null,
          id_pessoa: u.id_pessoa || u.idPessoa || u.id_pessoa || null,
          nome: u.nome,
          email: u.email,
          tipo: u.tipo || 'Colaborador',
        }));
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
        this.pessoas = (response.response || []).map(p => ({
          idPessoa: p.idPessoa || p.id || null,
          cpfCnpj: p.cpfCnpj || p.cpf_cnpj || p.cpf || null,
          nome: p.nome,
          email: p.email,
          telefone: p.telefone,
          empresa: p.empresa,
          cidade: p.cidade,
        }));
      } catch (error) {
        console.error("Erro ao carregar pessoas:", error);
      } finally {
        this.loading = false;
        console.log("pessoas carregadas");
      }
    },

    editItemUsuario(item) {
      this.usuarioEdicao = {
        id:  item.id,
        idPessoa: item.cpfCnpj || item.idPessoa || item.idPessoa,
        nome: item.nome,
        email: item.email,
        senha: item.senha || '',
        tipo: item.tipo || 'Colaborador',
      };
      this.ativoUsuario2 = true;
    },

    async createUsuario(dadosFormulario) {
      try {
        const payload = {
          id_pessoa: dadosFormulario.idPessoa,
          idPessoa: dadosFormulario.idPessoa,
          nome: dadosFormulario.nome,
          email: dadosFormulario.email,
          senha: dadosFormulario.senha,
          tipo: dadosFormulario.tipo || 'Colaborador',
        };
        await this.$api.post("/usuario/create", payload);
        console.log("Criando usuário");
        await this.getItemsUsuarios();
        this.fecharFormularioUsuario();
      } catch (error) {
        console.error("Erro ao criar usuário:", error);
      }
    },

    async editUsuario(dadosFormulario) {
      try {
        const id = dadosFormulario.id_usuario || dadosFormulario.id;
        const payload = {
          id_pessoa: dadosFormulario.idPessoa,
          idPessoa: dadosFormulario.idPessoa,
          nome: dadosFormulario.nome,
          email: dadosFormulario.email,
          tipo: dadosFormulario.tipo || 'Colaborador',
        };
        if (dadosFormulario.senha && String(dadosFormulario.senha).trim() !== '') {
          payload.senha = dadosFormulario.senha;
        }

        await this.$api.patch(`/usuario/update/${id}`, payload);
        console.log("Editando usuário");
        await this.getItemsUsuarios();
        this.fecharFormularioEdicaoUsuario();
      } catch (error) {
        console.error("Erro ao editar usuário:", error);
      }
    },

    async deleteItemUsuario(item) {
      const id = item.id_usuario || item.id;
      if (confirm(`Deseja deletar o usuário com ID ${id}?`)) {
        this.loading = true;
        try {
          await this.$api.delete(`/usuario/delete/${id}`);
          console.log("Deletando usuário");
          await this.getItemsUsuarios();
        } catch (error) {
          console.error("Erro ao excluir usuário:", error);
        } finally {
          this.loading = false;
        }
      }
    },
    logout() {
      this.$router.replace('/');
    }
  }
};
</script>

<style scoped>
.shortcut-card {
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  cursor: pointer;
  min-height: 220px;
  display: flex;
  flex-direction: column;
}

.shortcut-card:hover {
  transform: translateY(-4px) scale(1.02);
}

.shortcut-card .v-card-text {
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: center;
  padding: 20px !important;
  padding-top: 28px !important;
}

.shortcut-card .v-card-text h3 {
  margin: 8px 0 12px;
}
.shortcut-card .v-card-text p {
  margin: 0;
  color: #9aa3ad;
  display: -webkit-box;
  line-clamp: 3;
  -webkit-line-clamp: 3;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

.shortcut-card .v-icon {
  font-size: 56px !important;
  color: #6b6b6b !important;
  margin-bottom: 16px !important;
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

.page-container {
  padding-top: 72px;
}

@media (max-width: 960px) {
  .page-container {
    padding-top: 56px;
  }
}
</style>
