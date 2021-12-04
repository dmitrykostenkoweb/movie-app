<template>
  <div class="wrapper">
    <h1>{{ message }}</h1>
  </div>
</template>

<script>
export default {
  name: 'ErrorMessage',
  props: {
    message: {
      type: String,
      require: true,
    },
  },
}
</script>

<style lang="scss" scoped>
.wrapper {
  margin: autos;
  width: 100%;
  text-align: center;
  h1 {
    color: orangered;
    font-size: 40px;
    opacity: .6;
  }
}
</style>
