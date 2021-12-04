<template>
  <header class="header container">
    <nav class="nav">
      <a
        v-for="nav in navItems"
        :key="nav"
        @click="select(nav)"
        class="nav-item"
        >{{ nav }}</a
      >
    </nav>
  </header>
</template>

<script>
export default {
  name: 'AppBar',
  data() {
    return {
      navItems: ['Top Rated', 'Popular', 'Now Playing'],
    }
  },
  methods: {
    select(nav) {
      // alert(nav)
      this.$emit('navClick', nav)
    },
  },
}
</script>

<style lang="scss" scoped>
.header {
  z-index: 100;
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  padding: 8px;
}
.nav {
  display: flex;
  align-items: center;
  justify-content: space-around;
  flex-wrap: wrap;
  margin-top: 20px;
  &-item {
    font-weight: 600;
    cursor: pointer;
    font-size: 16px;
  }
}
</style>
