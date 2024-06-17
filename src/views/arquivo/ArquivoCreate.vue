<template>
  <v-container fluid>
    <s-pagebar
      page-title="Incluir Arquivo"
      :breadcrumbs="breadcrumbs"
    />
    <v-card flat>
      <v-card-text>
        <v-row dense>
          <v-col
            cols="12"
            md="6"
            sm="4"
          >
            <v-file-input
              label="Arquivo"
              :error-messages="errors.documento"
              accept=".csv"
              :loading="carregando"
              @change="handleFileUpload"
            />
          </v-col>
        </v-row>
      </v-card-text>
      <v-card-actions>
        <v-spacer />
        <v-btn
          outlined
          color="blue-grey darken-4"
          @click="$router.back()"
        >
          Voltar
        </v-btn>
        <v-btn
          color="blue-grey darken-4"
          dark
          @click="salvar"
        >
          Carregar
        </v-btn>
      </v-card-actions>
      <prompt-dialog ref="prompt" />
    </v-card>
  </v-container>
</template>

<script>
import SPagebar from '@/layout/SPagebar.vue'
import PromptDialog from '@/components/PromptDialog.vue'

export default {
  name: 'ArquivoCreate',
  components: { SPagebar, PromptDialog },
  data: () => ({
    carregando: false,
    breadcrumbs: [
      {
        'text': 'Arquivo',
        'to': '/arquivo',
        'exact': true
      },
      {
        'text': 'Detalhes',
        'disabled': true
      },
      {
        'text': 'Incluir arquivo',
        'disabled': true
      }
    ],
    arquivo: {},
    errors: {}
  }),
  methods: {
    handleFileUpload(file) {
      this.arquivo = file
    },
    async salvar() { 
      this.carregando = true;

      const formData = new FormData();
      formData.append('documento', this.arquivo);

      const response = this.$api.create({
        resource: this.$endpoints.ARQUIVO,
        data: formData,
        headers: { 'Content-Type': 'multipart/form-data' }
      })
      response
        .then(()=>{
          this.$toast.open({
              message: 'Arquivo gerado com sucesso',
              type: 'success',
          })
          this.carregando = false
          this.$router.back()
        })
        .catch(error=>{
          this.carregando = false
          this.errors = this.handleError(error)
        })
    }
  }
}
</script>

<style>
.required label::after {
    content: "*";
    color: red;
}
</style>