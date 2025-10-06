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
            <v-text-field 
              v-model="formulario.telefone"
              placeholder="Telefone"
              item-title="label" 
              item-value="telefone"
            />
          </v-col>
        </v-row>
        <v-row>
          <v-col>
            <v-text-field 
              v-model="formulario.empresa"
              placeholder="Empresa"
              item-title="label" 
              item-value="empresa"
            />
          </v-col>
        </v-row>
        <v-row>
          <v-col>
            <v-text-field 
              v-model="formulario.cidade"
              placeholder="Cidade"
              item-title="label" 
              item-value="cidade"
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
  name: "FormularioComponent",
  
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
  },

  emits: ["fechar", "salvar"],

  data() {
    return {
      loading: false,
      formulario: {
        nome: null,
        email: null,
        telefone: null,
        empresa: null,
        cidade: null,
      },
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
      } else {
        this.resetFormulario();
      }
    },

    resetFormulario() {
      this.formulario = {
        nome: null,
        email: null,
        telefone: null,
        empresa: null,
        cidade: null,
      };
    },

    fecharDialog() {
      this.$emit("fechar");
    },

    salvar() {
      this.$emit("salvar", { ...this.formulario });
    },
  },
};
</script>