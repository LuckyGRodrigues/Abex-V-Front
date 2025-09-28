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
      </v-container>
    </v-main>
  </v-app>
</template>

<script setup>
import { ref, computed, watch } from 'vue'
import { useDisplay } from 'vuetify'

definePageMeta({
  title: 'Início - Sistema de Gestão',
  description: 'Dashboard principal do sistema de gestão com atalhos para clientes, obras, financeiro e mais.',
  layout: 'default'
})

useHead({
  title: 'Início - Sistema de Gestão',
  meta: [
    { name: 'description', content: 'Dashboard principal do sistema de gestão empresarial' },
    { name: 'keywords', content: 'gestão, dashboard, clientes, obras, financeiro' }
  ]
})

const { mobile } = useDisplay()

const drawer = ref(!mobile.value)
const userName = ref('Fulano')

const navigationItems = Object.freeze([
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
])

const menuItems = computed(() => navigationItems)
const shortcuts = computed(() => navigationItems)

const handleNavigation = async (route) => {
  await navigateTo(route)
}

watch(mobile, (newVal) => {
  drawer.value = !newVal
}, { immediate: true })
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
