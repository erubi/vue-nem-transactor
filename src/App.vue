<template>
  <div id="app">
    <div class="top-bar">
      <img src="./assets/nem-xem.svg" />
      <span>NEM Transactor</span>
    </div>
    <Dashboard/>
  </div>
</template>

<script>
import Dashboard from './components/Dashboard';

export default {
  name: 'app',
  components: {
    Dashboard,
  },
};
</script>

<style>
html, body, #app {
  height: 100%;
  width: 100%;
  margin: 0;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

.top-bar {
  height: 40px;
  line-height: 40px;
  border-bottom: 1px solid gray;
  padding: 5px;
}

.top-bar img {
  float: left;
  display: inline-block;
  height: 100%;
}
</style>
