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
              v-model="formulario.cpfCnpj"
              label="CPF/CNPJ"
              outlined
              dense
            />
          </v-col>
          <v-col cols="12" sm="6">
            <v-text-field 
              v-model="formulario.nome"
              label="Nome"
              outlined
              dense
            />
          </v-col>
        </v-row>
        <v-row dense>
          <v-col cols="12" sm="6">
            <v-text-field 
              v-model="formulario.email"
              label="Email"
              type="email"
              outlined
              dense
            />
          </v-col>
          <v-col cols="12" sm="6">
            <v-text-field 
              v-model="formulario.telefone"
              label="Telefone"
              outlined
              dense
            />
          </v-col>
        </v-row>
        <v-row dense>
          <v-col cols="12" sm="6">
            <v-text-field 
              v-model="formulario.empresa"
              label="Empresa"
              outlined
              dense
            />
          </v-col>
          <v-col cols="12" sm="6">
            <v-text-field 
              v-model="formulario.cidade"
              label="Cidade"
              outlined
              dense
            />
          </v-col>
        </v-row>
        <v-row dense>
          <v-col cols="12">
            <v-select
              v-model="formulario.tipo"
              :items="['Cliente','Colaborador','Fornecedor']"
              label="Tipo"
              outlined
              dense
              disabled
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
    defaultTipo: {
      type: String,
      default: null,
    },
  },

  emits: ["fechar", "salvar"],

  data() {
    return {
      loading: false,
      formulario: {
        cpfCnpj: null,
        nome: null,
        email: null,
        telefone: null,
        empresa: null,
        cidade: null,
        tipo: 'Cliente',
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
        this.formulario = {
          idPessoa: this.itemEdicao.id,
          cpfCnpj: this.itemEdicao.cpfCnpj || this.itemEdicao.cpf || null,
          nome: this.itemEdicao.nome || null,
          email: this.itemEdicao.email || null,
          telefone: this.itemEdicao.telefone || null,
          empresa: this.itemEdicao.empresa || null,
          cidade: this.itemEdicao.cidade || null,
          tipo: this.itemEdicao.tipo || 'Cliente',
        };
      } else {
        this.resetFormulario();
        if (this.defaultTipo) {
          this.formulario.tipo = this.defaultTipo;
        }
      }
    },

    resetFormulario() {
        this.formulario = {
        idPessoa: null,
        cpfCnpj: null,
        nome: null,
        email: null,
        telefone: null,
        empresa: null,
        cidade: null,
        tipo: 'Cliente',
      };
    },

    fecharDialog() {
      this.$emit("fechar");
    },

    salvar() {
      this.$emit("salvar", { ...this.formulario, cpfCnpj: this.formulario.cpfCnpj });
    },
  },
};
</script>