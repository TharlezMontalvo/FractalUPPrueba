<template>
  <div class="d-flex">
    <div id="sidebar" class="bg-gray border">
      <div class="p-3 text-center">
        <div class="logo-container">Logo</div>
      </div>
      <ul class="nav flex-column">
        <li class="nav-item">
          <router-link class="nav-link" to="/" :class="{ active: isActive('/') }">Home</router-link>
        </li>
        <li class="nav-item">
          <router-link class="nav-link" to="/vista1" :class="{ active: isActive('/vista1') }">Vista 1</router-link>
        </li>
        <li class="nav-item">
          <router-link class="nav-link" to="/vista2" :class="{ active: isActive('/vista2') }">Vista 2</router-link>
        </li>
      </ul>
    </div>
    <div id="content">
      <router-view></router-view>
    </div>
  </div>
</template>

<script>
export default {
  name: 'SidebarMenu',
  methods: {
    isActive(path) {
      return this.$route.path === path;
    },
  },
};
</script>

<style scoped>
#sidebar {
  width: 250px;
  min-height: 100vh;
  background-color: #3c3c3c;
  position: fixed;
  top: 0;
  left: 0;
  z-index: 1000;
  overflow-y: auto; 
}

.logo-container {
  background-color: #b5b5b5;
  color: #3c3c3c;
  padding: 10px;
  font-weight: bold;
  border-radius: 8px;
  margin-bottom: 20px;
  text-align: center; 
}

.nav-link {
  color: white;
  padding: 10px 16px;
  display: block;
  border-radius: 4px;
  font-weight: bold;
  margin-left: 10px;
  margin-right: 10px;
}

.nav-link.active {
  background-color: #ffffff;
  color: #3c3c3c;
}

.nav-link:hover,
.nav-link.router-link-exact-active {
  background: white;
  color: black;
}

#content {
  margin-left: 250px; 
  padding: 20px;
  flex: 1; 
}

@media (max-width: 768px) {
  #sidebar {
    width: 100%;
    position: relative;
    min-height: auto;
    overflow-y: hidden; 
    border-right: none; 
  }

  #content {
    margin-left: 0; 
    padding: 10px; 
  }

  .logo-container {
    margin-bottom: 10px;
  }

  .nav-link {
    margin-left: 0;
    margin-right: 0;
  }
}
</style>