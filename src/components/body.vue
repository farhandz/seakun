<template>
  <body class="container">
    <Header />
      <SidebarNavigation />
       <router-view></router-view>
  </body>
</template>

<script>
import Header from './header'
import SidebarNavigation from './SidebarNavigation'
export default {
  components: {
    Header,
    SidebarNavigation
  }
}
</script>

<style>

</style>
