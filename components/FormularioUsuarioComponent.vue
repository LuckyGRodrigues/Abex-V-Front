<template>
  <v-dialog 
    v-model="dialogAtivo"
    max-width="400"
    @update:model-value="fecharDialog"
  >
    <v-card 
      height="650" 
      width="400" 
      theme="dark"
    >
      <v-card-title>
        {{ modoEdicao ? 'Editar' : 'Criar' }}
      </v-card-title>
      <v-card-text>
        <v-row>
          <v-col>
            <v-text-field 
                  v-model="formulario.nome"
                  placeholder="Nome"
                  item-title="label" 
                  item-value="nome"
            />
          </v-col>
        </v-row>
        <v-row>
          <v-col>
            <v-text-field 
              v-model="formulario.email"
              placeholder="Email"
              type="email"
              item-title="label" 
              item-value="email"
            />
          </v-col>
        </v-row>
        <v-row>
          <v-col>
            <v-row align="center">
              <v-col cols="8">
                <v-text-field
                  v-model="formulario.senha"
                  placeholder="Senha"
                  type="password"
                  :disabled="modoEdicao && !alterarSenha"
                />
              </v-col>
              <v-col cols="4">
                <v-checkbox
                  v-model="alterarSenha"
                  label="Alterar senha"
                />
              </v-col>
            </v-row>
          </v-col>
        </v-row>
        <v-row>
          <v-col>
            <v-select
              v-model="formulario.tipo"
              :items="['Administrador','Colaborador']"
              label="Tipo"
              placeholder="Tipo"
            />
          </v-col>
        </v-row>
        <v-row>
          <v-col>
            <v-autocomplete 
              v-model="formulario.idPessoa"
              :items="pessoas" 
              placeholder="Pessoa vinculada"
              item-title="nome" 
              item-value="idPessoa"
            />
          </v-col>
        </v-row>
      </v-card-text>
      <v-card-actions>
        <v-btn 
          variant="outlined"
          :loading="loading"
          @click="salvar"
        >
          {{ modoEdicao ? 'Editar' : 'Criar' }}
        </v-btn>
        <v-btn 
          variant="outlined" 
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
        // map server fields to form where necessary
        this.formulario = { ...this.itemEdicao };
        // when editing, don't prefill senha for security; require explicit change
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
      this.alterarSenha = !this.modoEdicao; // true for create, false for edit
    },

    fecharDialog() {
      this.$emit("fechar");
    },

    salvar() {
      const payload = { ...this.formulario };
      // if in edit mode and user didn't opt to change password, don't include senha
      if (this.modoEdicao && !this.alterarSenha) {
        delete payload.senha;
      }
      this.$emit("salvar", payload);
    },
  },
};
</script>