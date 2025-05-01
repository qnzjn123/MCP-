<template>
  <div class="hello">
    <h2>{{ msg }}</h2>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  }
}
</script>

<style scoped>
.hello {
  margin: 20px 0;
}
</style> 