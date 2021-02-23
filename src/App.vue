<template>
  <div id="app">
    <Menu />
    <Header />
    <Content />
  </div>
</template>

<script>
import Menu from './components/layout/Menu'
import Header from './components/layout/Header'
import Content from './components/layout/Content'

export default {
  name: 'App',
  components: {
    Menu,
    Header,
    Content,
  }
}
</script>

<style>
@import url('./styles/index.css');

</style>
