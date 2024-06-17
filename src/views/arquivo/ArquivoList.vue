<template>
  <v-container fluid>
    <s-pagebar
      page-title="Arquivos"
      :breadcrumbs="breadcrumbs"
    >
      <v-btn
        color="blue-grey darken-4"
        dark
        fab
        small
        @click="cadastrarArquivo"
      >
        <v-icon>add</v-icon>
      </v-btn>
    </s-pagebar>    
    <v-card flat>
      <v-card-text>
        <v-data-table 
          :headers="headers"
          :items="items"
          :options.sync="options"
          :server-items-length="totalItems"
          :items-per-page="10"
          :footer-props="{'items-per-page-options': [10, 20, 30]}"
        >
          <template #[`item.data_carregamento`]="{ item }">
            <span v-if="!!item.data_carregamento">{{ formatDateTimeToDate(item.data_carregamento) }}</span>
          </template>
          <template #[`item.action`]="{ item }">
            <v-toolbar-items>
              <v-tooltip bottom>
                <template #activator="{ on, attrs }">
                  <v-btn
                    v-bind="attrs"
                    icon
                    v-on="on"
                    @click="GerarArquivo(item.id)"
                  >
                    <v-icon class="material-icons-outlined">
                      print
                    </v-icon>
                  </v-btn>
                </template>
                <span>Gerar Arquivo</span>
              </v-tooltip>
            </v-toolbar-items>
          </template>
        </v-data-table>
      </v-card-text>
    </v-card>
    <ConfirmDialog ref="confirm" />
  </v-container>
</template>

<script>
import ConfirmDialog from '@/components/ConfirmDialog.vue'
import * as documentoService from '@/services/documento.service'
import GenericMixin from '@/mixins/GenericMixin'
import SPagebar from '@/layout/SPagebar.vue'

export default {
    name: "ArquivoList",
    components: { SPagebar, ConfirmDialog },
    mixins: [GenericMixin],
    props: {
        permissao: Object,
        disabledButtons: Boolean
    },
    data: () => ({
      breadcrumbs: [
            {
            'text': 'Lista de Arquivo',
            'to': '/arquivo',
            'exact': true
            },
        ],
        headers: [
            { text: "Arquivo", value: "id" },
            { text: "Data do Carregamento", value: "data_carregamento" },
            { text: "Usuario", value: "usuario" },
            {text: 'Ações', value: 'action'},
        ],
        totalItems: 0,
        items: [],
        options: {},
    }),
    watch: {
        options: {
            handler() {
                this.getArquivos();
            },
            deep: true
        },
    },
    methods: {
      async getArquivos() { 
        const { sortBy, sortDesc, page, itemsPerPage } = this.options   

        const query = {
          arquivo: this.arquivo,
          page: page,
          page_size: itemsPerPage,
          ordering: `${sortDesc && sortDesc[0] ? "-" : ""}${sortBy}`
        }
        const response = await this.$api.list({
            resource: this.$endpoints.ARQUIVO,
            query: query
        });
        this.items = response.data.results;
        this.totalItems = response.data.count;
      },
      cadastrarArquivo() {
        this.$router.push('/arquivo/cadastrar')
      },
      GerarArquivo(arquivoId) {
        let request = documentoService.carregarArquivo({
          resource: `${this.$endpoints.ARQUIVO}/segunda_via/`,
          data: {
            id_arquivo: arquivoId
          }
        })
        request
          .then(response => {
            const downloadUrl = window.URL.createObjectURL(response.data)
            window.open(downloadUrl, '__blank')
          })
          .catch((error) => {
            this.handleBlobError(error)
          });
      },
    }
}
</script>