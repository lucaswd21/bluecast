<template lang="pug">
  div
    v-dialog(v-model="dialogSave" persistent max-width="75vh")
      v-card.rounded-xl
        v-card-title
          v-icon(left color="primary") mdi-account
          span(class="headline") Novo cliente
        v-card-text
          v-container
            v-row
              v-col.mb-n8(cols="12" md="12")
                v-text-field(
                  v-model="editedClient.name"
                  rounded
                  outlined
                  label="Nome"
                  required
                )
              v-col.mb-n8(cols="12" md="12")
                v-text-field(
                  v-model="editedClient.value"
                  rounded
                  outlined
                  label="Valor"
                  required
                )
              v-col.mb-n4(cols="12" md="12")
                v-text-field(
                  v-model="editedClient.date"
                  rounded
                  outlined
                  label="Desde"
                  required
                )
        v-card-actions
          v-spacer
          v-btn.mb-2(color="primary" text @click="$root.$emit('closeModal', false);clear()")
            span.px-4.py-2 Fechar
          v-btn.mb-2(color="primary" :loading="loading" :disabled="loading" rounded @click="save()")
            span.px-4.py-2 Salvar

    v-snackbar(
      v-model="snackbar"
      top
      rounded="pill"
      :color="snackbarColor"
      :timeout="1500"
      style="z-index: 9999;"
    )
      v-spacer
      p(class="text-center body-1 font-weight-bold ma-n3") {{ text }}
      v-spacer
</template>
<script>
import moment from 'moment'

export default {
  props: {
    dialogSave: {
      type: Boolean,
      default () { return false }
    }
  },
  data () {
    return {
      editedClient: {
        name: '',
        value: '',
        date: ''
      },
      loading: false,
      snackbar: null,
      snackbarColor: 'primary',
      text: null
    }
  },

  methods: {

    clear () {
      this.editedClient = {
        name: '',
        value: '',
        date: ''
      }
    },

    async save () {
      this.loading = true
      this.editedClient.date = moment(this.editedClient.date).format('YYYY-MM-DD')
      this.editedClient.value = parseFloat(this.editedClient.value)
      try {
        await this.$axios.$post('/client', {
          name: this.editedClient.name,
          value: this.editedClient.value,
          date: this.editedClient.date
        })
        this.loading = false
        this.$root.$emit('closeModal', false)
        this.clear()
        this.text = 'Cliente cadastrado com sucesso'
        this.snackbarColor = 'primary'
        this.snackbar = true
      } catch (error) {
        console.log(error)
        this.loading = false
        this.$root.$emit('closeModal', false)
        this.clear()
        this.text = 'Falha ao cadastrar'
        this.snackbarColor = 'red'
        this.snackbar = true
      }
    }
  }
}
</script>
