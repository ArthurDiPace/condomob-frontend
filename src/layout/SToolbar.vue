<template>
  <v-toolbar
    flat
    style="overflow-x:auto; white-space: nowrap;"
  >
    <v-btn
      text
      to="/arquivo"
    >
      <v-icon class="material-icons-outlined mr-2">
        folder
      </v-icon>
      Arquivo
    </v-btn>
  </v-toolbar>
</template>

<script>
export default {
    name: 'SToolbar'
}
</script>

<style lang="scss" scoped>
.v-btn {
  text-transform: none;
  letter-spacing: 0;
  font-weight: 400;
}
</style>