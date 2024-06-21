<template>
  <div class="d-flex">
    <!-- Icono de menú para dispositivos móviles -->
    <button class="btn btn-outline-light d-md-none" @click="toggleSidebar">
      <i class="bi bi-list"></i>
    </button>

    <div id="sidebar" class="bg-gray border" :class="{ 'sidebar-hidden': sidebarHidden }">
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

    <div id="content" class="flex-grow-1">
      <router-view></router-view>
    </div>
  </div>
</template>

<script>
export default {
  name: 'SidebarMenu',
  data() {
    return {
      sidebarHidden: true, // Estado inicial del sidebar
    };
  },
  methods: {
    isActive(path) {
      return this.$route.path === path;
    },
    toggleSidebar() {
      this.sidebarHidden = !this.sidebarHidden;
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
  transition: transform 0.3s ease;
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
  .btn-menu {
    position: fixed;
    top: 10px;
    left: 10px;
    z-index: 1100; 
  }

  #sidebar {
    transform: translateX(-100%); 
    position: fixed;
    top: 0;
    left: 0;
    z-index: 1000;
    overflow-y: auto; 
  }

  .sidebar-hidden {
    transform: translateX(-100%); 
  }

  #content {
    margin-left: 0;
    padding: 20px;
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
