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
          <v-col cols="12">
            <v-text-field 
              v-model="formulario.descricao"
              label="Descrição"
              outlined
              dense
            />
          </v-col>
        </v-row>
        <v-row dense>
          <v-col cols="12" sm="6">
            <v-text-field 
              v-model="formulario.valorEstimado"
              label="Valor Estimado"
              type="number"
              outlined
              dense
            />
          </v-col>
          <v-col cols="12" sm="6">
            <v-text-field 
              v-model="formulario.data"
              label="Data"
              type="date"
              outlined
              dense
            />
          </v-col>
        </v-row>
        <v-row dense>
          <v-col cols="12" sm="6">
            <v-select
              v-model="formulario.status"
              :items="statusOptions"
              label="Status"
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
          status: 'Levantamento',
        idPessoa: null,
      },
        statusOptions: ['Levantamento', 'Enviado', 'Aguardando Resposta', 'Fechado', 'Cancelado'],
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