<template lang="pug">
  div
    v-container.my-4(fluid)
      newClientModal(:dialogSave="dialogSave" style="z-index: 999;")

      h1.headline.mx-6.mt-4.mb-6
        v-icon.primary--text.left.mb-1.ml-3.mr-2 mdi-account-alert
        span.font-weight-bold Clientes inadimplentes

      v-layout.align-center.justify-center.wrap.mx-md-8.mx-2
        v-flex.xs12.sm12.md4
          v-text-field(
            v-model="search"
            rounded
            clearable
            flat
            outlined
            color="primary"
            prepend-inner-icon="mdi-account-search"
            label="Buscar"
          )
        v-spacer
        v-flex.xs4.sm4.md2
          v-btn.mb-md-6.mb-2.mr-md-n16(dark depressed rounded color="primary" @click="sortBy='name'")
            span.px-4.py-2 Nome
        v-flex.xs4.sm4.md2
          v-btn.mb-md-6.mb-2.ml-md-n8(dark depressed rounded color="primary" @click="sortBy='value'")
            span.px-4.py-2 Valor
        v-flex.xs4.sm4.md2
          v-btn.mb-md-6.mb-2.ml-md-n15(dark depressed rounded color="primary" @click="sortBy='date'")
            span.px-4.py-2 Desde
        v-spacer

      v-data-iterator(
        :items="clients"
        :items-per-page.sync="itemsPerPage"
        :page="page"
        :search="search"
        :sort-by="sortBy.toLowerCase()"
        :sort-desc="sortDesc"
        hide-default-footer
        class="mx-8 my-4"
        :loading="change" loading-text="Carregando dados..."
      )
        template(v-slot:default="props")
          v-row
            v-col(
              v-for="item in props.items"
              :key="item.name"
              cols="12"
              sm="6"
              md="4"
              lg="4"
            )
              v-card.rounded-xl
                v-card-text
                  v-layout.align-center.justify-center.wrap
                    v-flex.xs5.sm5.md4
                      v-avatar(size="15vh" color="grey lighthen-1")
                        v-icon(color="grey darken-3" style="font-size: 10vh;") mdi-account
                    v-flex.xs7.sm7.md8
                      p.body-2
                        span.font-weight-bold Nome:
                        |  {{item.name}}
                      p.body-2
                        span.font-weight-bold Valor:
                        |  {{item.value}}
                      p.body-2
                        span.font-weight-bold Desde:
                        |  {{item.dateFormated}}

        template(v-slot:footer)
          v-row(class="mt-2 mx-4" align="center" justify="center")
            span(class="grey--text") Clientes por página
            v-menu(offset-y)
              template(v-slot:activator="{ on, attrs }")
                v-btn(
                  dark
                  text
                  color="primary"
                  class="ml-2"
                  v-bind="attrs"
                  v-on="on"
                )
                  | {{ itemsPerPage }}
                  v-icon mdi-chevron-down
              v-list
                v-list-item(
                  v-for="(number, index) in itemsPerPageArray"
                  :key="index"
                  @click="updateItemsPerPage(number)"
                )
                  v-list-item-title {{ number }}

            v-spacer

            span(class="mr-4 grey--text")
              | Página {{ page }} de {{ numberOfPages }}
            v-btn(
              fab
              dark
              depressed
              color="primary"
              class="mr-1"
              @click="formerPage"
              small
            )
              v-icon mdi-chevron-left
            v-btn(
              fab
              dark
              depressed
              color="primary"
              class="ml-1"
              @click="nextPage"
              small
            )
              v-icon mdi-chevron-right
        template(v-slot:no-data)
          div(class="font-weight-bold mt-4")
            v-alert(:value="true" dark class="text-xs-center primary")
              v-icon(left class="fas fa-info-circle")
              | Não há clientes inadimplentes no momento
        template(v-slot:no-results)
          v-alert(:value="true" dark class="primary" icon="fas fa-info-circle")
            | Sua busca por "{{ search }}" não obteve resultados.
    v-btn.my-md-1.my-2(
      color="primary"
      fab
      fixed
      large
      dark
      bottom
      right
      style="z-index: 99;"
      @click="dialogSave=true"
    )
      v-icon(large) mdi-plus

</template>
<script>
import moment from 'moment'
import NewClientModal from '~/components/newClientModal'

export default {
  components: {
    NewClientModal
  },

  data: () => ({
    dialogSave: false,
    items: [],
    itemsPerPageArray: [3, 6, 9],
    search: '',
    filter: {},
    sortDesc: false,
    page: 1,
    itemsPerPage: 3,
    sortBy: 'name',
    clients: [],
    editedPost: {
      name: '',
      value: null,
      date: null
    },
    change: true,
    loading: false
  }),

  computed: {
    numberOfPages () {
      return Math.ceil(this.clients.length / this.itemsPerPage)
    },
    filteredKeys () {
      return this.keys.filter(key => key !== 'Name')
    }
  },

  mounted () {
    this.getClients()
    this.$root.$on('closeModal', ($event) => {
      this.dialogSave = $event
      this.getClients()
      this.clear()
    })
  },

  methods: {
    async getClients () {
      try {
        const response = await this.$axios.$get('/client')
        response.forEach((client) => {
          client.dateFormated = moment(client.date).format('DD/MM/YYYY')
        })
        this.clients = response
        this.change = false
        this.loading = false
      } catch (error) {
        console.log(error)
        this.change = false
        this.loading = false
      }
    },

    nextPage () {
      if (this.page + 1 <= this.numberOfPages) {
        this.page += 1
      }
    },
    formerPage () {
      if (this.page - 1 >= 1) {
        this.page -= 1
      }
    },
    updateItemsPerPage (number) {
      this.itemsPerPage = number
    },

    clear () {
      this.editedClient = {
        name: '',
        value: '',
        date: ''
      }
    }
  }
}
</script>
