<template>
  <v-dialog 
    v-model="dialogAtivo"
    max-width="500"
    @update:model-value="fecharDialog"
  >
    <v-card
      style="border: solid grey 4px; border-radius: 4px; background: #fff;"
      class="pa-4"
    >
      <v-card-title class="text-h6" style="color: #795548;">
        {{ modoEdicao ? 'Editar' : 'Criar' }}
      </v-card-title>
      <v-divider class="mb-4" />
      <v-card-text>
        <v-row dense>
          <v-col cols="12" sm="6">
            <v-text-field 
              v-model="formulario.nome"
              label="Nome"
              outlined
              dense
            />
          </v-col>
          <v-col cols="12" sm="6">
            <v-text-field 
              v-model="formulario.email"
              label="Email"
              type="email"
              outlined
              dense
            />
          </v-col>
        </v-row>
        <v-row dense>
          <v-col cols="12" sm="8">
            <v-text-field
              v-model="formulario.senha"
              label="Senha"
              type="password"
              outlined
              dense
              :disabled="modoEdicao && !alterarSenha"
            />
          </v-col>
          <v-col cols="12" sm="4" class="d-flex align-center">
            <v-checkbox
              v-model="alterarSenha"
              label="Alterar senha"
              hide-details
            />
          </v-col>
        </v-row>
        <v-row dense>
          <v-col cols="12" sm="6">
            <v-select
              v-model="formulario.tipo"
              :items="['Administrador','Colaborador']"
              label="Tipo"
              outlined
              dense
            />
          </v-col>
          <v-col cols="12" sm="6">
            <v-autocomplete 
              v-model="formulario.idPessoa"
              :items="pessoas" 
              label="Pessoa vinculada"
              item-title="nome" 
              item-value="idPessoa"
              outlined
              dense
            />
          </v-col>
        </v-row>
      </v-card-text>
      <v-card-actions class="justify-end">
        <v-btn 
          variant="outlined"
          color="brown"
          :loading="loading"
          @click="salvar"
        >
          {{ modoEdicao ? 'Editar' : 'Criar' }}
        </v-btn>
        <v-btn 
          variant="outlined" 
          color="grey"
          @click="fecharDialog"
        >
          Cancelar
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script>
export default {
  name: "FormularioUsuarioComponent",
  
  props: {
    ativo: {
      type: Boolean,
      default: false,
    },
    itemEdicao: {
      type: Object,
      default: null,
    },
    modoEdicao: {
      type: Boolean,
      default: false,
    },
    pessoas: {
      type: Array,
      default: () => [],
    },
  },

  emits: ["fechar", "salvar"],

  data() {
    return {
      loading: false,
      formulario: {
        id_usuario: null,
        idPessoa: null,
        nome: null,
        email: null,
        senha: null,
        tipo: 'Colaborador',
      },
      alterarSenha: false,
    };
  },

  computed: {
    dialogAtivo: {
      get() {
        return this.ativo;
      },
      set(value) {
        if (!value) {
          this.fecharDialog();
        }
      }
    }
  },

  watch: {
    ativo(novoValor) {
      if (novoValor) {
        this.carregarDados();
      } else {
        this.resetFormulario();
      }
    },
    itemEdicao: {
      handler() {
        if (this.ativo && this.itemEdicao) {
          this.carregarDados();
        }
      },
      deep: true,
      immediate: true,
    }
  },

  methods: {
    carregarDados() {
      if (this.modoEdicao && this.itemEdicao) {
        this.formulario = { ...this.itemEdicao };
        this.formulario.senha = '';
        this.alterarSenha = false;
      } else {
        this.resetFormulario();
      }
    },

    resetFormulario() {
        this.formulario = {
        id_usuario: null,
        idPessoa: null,
        nome: null,
        email: null,
        senha: '',
        tipo: 'Colaborador',
      };
      this.alterarSenha = !this.modoEdicao;
    },

    fecharDialog() {
      this.$emit("fechar");
    },

    salvar() {
      const payload = { ...this.formulario };
      if (this.modoEdicao && !this.alterarSenha) {
        delete payload.senha;
      }
      this.$emit("salvar", payload);
    },
  },
};
</script>