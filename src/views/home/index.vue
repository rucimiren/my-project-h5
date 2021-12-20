<template>
  <div class="home">
    <div id="nav">
      <router-link to="/drag">drag</router-link>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Home',
}
</script>
<style lang="less" scoped>
.home {
  font-size: 20px;
  #nav {
    padding: 30px;

    a {
      font-weight: bold;
      color: #2c3e50;

      &.router-link-exact-active {
        color: #42b983;
      }
    }
  }
}
</style>
