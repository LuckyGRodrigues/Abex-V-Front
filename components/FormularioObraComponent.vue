<template>
  <v-card flat>
    <v-card-title>{{ isEdit ? 'Editar Controle de Obra' : 'Criar Controle de Obra' }}</v-card-title>
    <v-card-text>
      <v-row>
        <v-col cols="12">
          <v-text-field v-model="form.descricao" label="Descrição (opcional)" />
        </v-col>

        <v-col cols="12" md="6">
          <v-autocomplete
            v-model="form.idOrcamento"
            :items="orcamentosMapped"
            item-title="title"
            item-value="value"
            label="Orçamento"
            clearable
            hide-details
          />
        </v-col>

        <v-col cols="12" md="6">
          <v-autocomplete
            v-model="form.responsavel"
            :items="usuariosMapped"
            item-title="title"
            item-value="value"
            label="Responsável (usuário)"
            clearable
            hide-details
          />
        </v-col>

        <v-col cols="12" md="6">
          <v-text-field
            v-model="form.horasTrabalhadas"
            label="Horas Trabalhadas"
            type="number"
            hide-details
          />
        </v-col>

        <v-col cols="12" md="6">
          <v-menu :close-on-content-click="false" v-slot:activator="{ props }">
            <v-text-field
              v-bind="props"
              v-model="form.dataRegistro"
              label="Data Registro"
              readonly
            />
            <v-date-picker v-model="form.dataRegistro" scrollable />
          </v-menu>
        </v-col>

        <v-col cols="12" md="6">
          <v-select
            :items="statusOptions"
            v-model="form.status"
            label="Status"
            hide-details
          />
        </v-col>

      </v-row>
    </v-card-text>

    <v-card-actions>
      <v-spacer />
      <v-btn variant="outlined" color="primary" @click="onSave">Salvar</v-btn>
      <v-btn variant="outlined" color="secondary" @click="onCancel">Cancelar</v-btn>
    </v-card-actions>
  </v-card>
</template>

<script>
export default {
  name: 'FormularioObraComponent',
  props: {
    obra: {
      type: Object,
      default: null,
    },
    orcamentos: {
      type: Array,
      default: () => [],
    },
    usuarios: {
      type: Array,
      default: () => [],
    },
    statusOptions: {
      type: Array,
      default: () => ['Em andamento', 'Parado', 'Concluído', 'Cancelado'],
    },
  },
  data() {
    return {
      form: {
        id: null,
        descricao: null,
        idOrcamento: null,
        dataRegistro: null,
        horasTrabalhadas: null,
        responsavel: null,
        status: 'Em andamento',
      },
    };
  },
  computed: {
    isEdit() {
      return !!(this.form && (this.form.id || this.form.idOrcamento));
    },
    orcamentosMapped() {
      return (this.orcamentos || []).map(o => ({
        title: o.descricao || o.title || `#${o.id || o.id_orcamento}`,
        value: o.id || o.id_orcamento || o.idOrcamento,
      }));
    },
    usuariosMapped() {
      return (this.usuarios || []).map(u => ({
        title: u.nome || u.email || `#${u.id || u.id_usuario}`,
        value: u.id || u.id_usuario || u.idUsuario,
      }));
    },
  },
  watch: {
    obra: {
      handler(v) {
        if (v) {
          this.form = {
            id: v.id || v.id_controle || v.id_controle || v.id || null,
            descricao: v.descricao || v.nome || null,
            idOrcamento: v.idOrcamento || v.id_orcamento || v.id_orcamento || null,
            dataRegistro: v.dataRegistro || v.data_registro || v.dataRegistro || null,
            horasTrabalhadas: v.horasTrabalhadas || v.horas_trabalhadas || null,
            responsavel: v.responsavel || v.responsavel || v.responsavel || null,
            status: v.status || 'Em andamento',
          };
        } else {
          this.reset();
        }
      },
      immediate: true,
    },
  },
  methods: {
    reset() {
      this.form = {
        id: null,
        descricao: null,
        idOrcamento: null,
        dataRegistro: null,
        horasTrabalhadas: null,
        responsavel: null,
        status: 'Em andamento',
      };
    },
    onSave() {
      const payload = {
        ...this.form,
        idOrcamento: this.form.idOrcamento ? Number(this.form.idOrcamento) : null,
        responsavel: this.form.responsavel ? Number(this.form.responsavel) : null,
      };
      this.$emit('salvar', payload);
    },
    onCancel() {
      this.$emit('cancel');
    },
  },
};
</script>

<style scoped>
/* small spacing tweaks if necessary */
</style>
