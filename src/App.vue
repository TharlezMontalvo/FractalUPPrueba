<template>
  <div>
    <SidebarMenu />
  </div>
</template>

<script>
import SidebarMenu from './components/SidebarMenu.vue';

export default {
  name: 'App',
  components: {
    SidebarMenu,
  },
};
</script>

<style>
body {
  font-family: Arial, sans-serif;
}

#content {
  flex: 1;
  padding: 20px;
}
</style>
