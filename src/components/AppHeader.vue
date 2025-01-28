<script>
export default {
  data() {
    return { 
      message: 'Template Vite + Vue'
    }
  }
}
</script>

<template>
  <header>
    <h1>
      {{ message }}
    </h1>
  </header>
</template>

<style lang="scss" scoped>

header {
  background-color: red;
  text-align: center;
  padding: 20px 0;
}

h1 {
  color: blue;
}
</style>
