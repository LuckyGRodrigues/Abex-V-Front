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
              v-model="formulario.descricao"
              placeholder="Descrição"
              item-title="label" 
              item-value="descricao"
            />
          </v-col>
        </v-row>
        <v-row>
          <v-col>
            <v-text-field 
              v-model="formulario.valorEstimado"
              placeholder="Valor Estimado"
              type="number"
              item-title="label" 
              item-value="valorEstimado"
            />
          </v-col>
        </v-row>
        <v-row>
          <v-col>
            <v-text-field 
              v-model="formulario.data"
              placeholder="Data"
              type="date"
              item-title="label" 
              item-value="data"
            />
          </v-col>
        </v-row>
        <v-row>
          <v-col>
            <v-text-field 
              v-model="formulario.status"
              placeholder="Status"
              item-title="label" 
              item-value="status"
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
              item-value="id"
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
  name: "FormularioOrcamentoComponent",
  
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
        descricao: null,
        valorEstimado: null,
        data: null,
        status: null,
        idPessoa: null,
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
        descricao: null,
        valorEstimado: null,
        data: null,
        status: null,
        idPessoa: null,
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