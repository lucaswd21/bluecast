<template lang="pug">
  v-app
    Navbar
    v-main
      nuxt
    Footer
</template>
<script>
import Navbar from '../components/Navbar'
import Footer from '../components/Footer'

export default {
  components: {
    Navbar,
    Footer
  }
}
</script>
