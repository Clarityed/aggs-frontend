<template>
  <div class="app">
    <router-view />
  </div>
</template>

<style>
#app {
  padding: 20px;
  /* 居中 */
  margin: 0 auto;
  /* 限制最大宽度 */
  max-width: 1024px;
}

nav {
  padding: 30px;
}

nav a {
  font-weight: bold;
  color: #2c3e50;
}

nav a.router-link-exact-active {
  color: #42b983;
}
</style>
