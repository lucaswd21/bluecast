<template lang="pug">
  div
    v-app-bar(
      app
      dark
      :height="70"
      style="z-index: 9999;"
    )
      v-spacer(class="hidden-md-and-up")
      span.title Bluecast
      v-spacer
</template>
