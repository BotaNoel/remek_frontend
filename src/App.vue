<script>
import Search from './components/Search.vue';
import Login from './components/Login.vue';
import Register from './components/Register.vue';
import Footer from './components/Footer.vue';
import ApartmentUpload from '@/components/ApartmentUpload.vue';
import logo from './assets/icon-deal.png';
import user from './assets/user.webp';
import HomePage from './components/HomePage.vue';
import About from './components/About.vue';

export default {
  components: {
    Search,
    Footer,
    ApartmentUpload,
    Login,
    HomePage,
    About,
    Register
  },
  data() {
    return {
      logo,
      user,
      navbarOpen: false,
      dropdownOpen: null,
      apartmentUpload: false,
      login: false,
      register: false,
      showUploadTooltip: false,
      loggedInUser: '',
      showApartmentDetails: false,
      showAbout: false,
      token: null,
      userId: null,
      toast: {
        show: false,
        message: '',
        type: 'success'
      }
    }
  },
  mounted() {
    // Tooltip logika (ha inaktív gombra húzza az egeret)
    const uploadBtnContainer = document.querySelector('.btn-secondary')?.parentElement;
    if (uploadBtnContainer) {
      uploadBtnContainer.addEventListener('mouseenter', () => {
        this.showUploadTooltip = true;
      });
      uploadBtnContainer.addEventListener('mouseleave', () => {
        this.showUploadTooltip = false;
      });
    }
  },
  created() {
    // Ellenőrizzük, hogy van-e bejelentkezett felhasználó a localStorage-ban
    const storedToken = localStorage.getItem("token");
    const storedUser = localStorage.getItem("user");

    if (storedToken && storedUser) {
      this.token = storedToken;
      this.loggedInUser = JSON.parse(storedUser).name;
      this.userId = JSON.parse(storedUser).id;  // Felhasználói ID beállítása
      this.login = false;  // A bejelentkezési oldalt ne jelenítse meg
    }
  },
  methods: {
    toggleNavbar() {
      this.navbarOpen = !this.navbarOpen;
    },
    toggleDropdown(menu) {
      this.dropdownOpen = this.dropdownOpen === menu ? null : menu;
    },
    uploadSite() {
      this.apartmentUpload = !this.apartmentUpload
    },
    loginSite() {
      this.login = true;
      this.register = false;
    },
    registerSite() {
      this.register = true;
      this.login = false;
    },
    logged_in(p) {
      this.loggedInUser = p.name;
    },
    showAboutPage() {
      this.apartmentUpload = false;
      this.login = false;
      this.showAbout = true;
    },
    logout() {
      localStorage.removeItem("token");
      localStorage.removeItem("user");
      this.token = null;
      this.loggedInUser = '';
      this.userId = null;
      this.login = false;
      this.register = false;
      this.apartmentUpload = false;
      this.showAbout = false;
    },
    showToast(message, type = 'success') {
      this.toast.message = message;
      this.toast.type = type;
      this.toast.show = true;

      setTimeout(() => {
        this.toast.show = false;
      }, 3000);
    }
  }
}
</script>

<template>
  <div class="container-xxl bg-white p-0">
    <!-- Navbar -->
    <div class="container-xxl bg-white p-0">
      <div class="container-fluid nav-bar bg-transparent">
        <nav class="navbar navbar-expand-lg bg-white navbar-light py-0 px-4">
          <a href="" class="navbar-brand d-flex align-items-center text-center">
            <h1 class="m-0 text-primary">SleepIn</h1>
          </a>
          <button type="button" class="navbar-toggler" @click="toggleNavbar">
            <span class="navbar-toggler-icon"></span>
          </button>
          <div class="collapse navbar-collapse" :class="{ 'show': navbarOpen }" id="navbarCollapse">
            <div class="navbar-nav ms-auto">
              <a href="" class="nav-item nav-link active">Kezdőlap</a>
              <a href="" class="nav-item nav-link" @click.prevent="showAboutPage">Rólunk</a>
              <div class="nav-item dropdown" @click="toggleDropdown('kinálatunk')">
                <a href="#" class="nav-link dropdown-toggle">Kínálatunk</a>
                <div class="dropdown-menu rounded-0 m-0" :class="{ 'show': dropdownOpen === 'kinálatunk' }">
                  <a href="" class="dropdown-item">Apartmanok</a>
                  <a href="" class="dropdown-item">Családi házak</a>
                  <a href="" class="dropdown-item">Szállodák</a>
                  <a href="" class="dropdown-item">Luxus ingatlanok</a>
                </div>
              </div>
              <a href="" class="nav-item nav-link">Elérhetőség</a>
              <a v-if="!loggedInUser" href="#" @click.prevent="loginSite"
                class="nav-item nav-link text-primary pointer">
                Bejelentkezés
              </a>
              <a v-if="!loggedInUser" href="#" @click.prevent="registerSite"
                class="nav-item nav-link text-primary pointer">
                Regisztráció
              </a>
              <div v-else class="nav-item dropdown" @click="toggleDropdown('user')">
                <a href="#" class="nav-link dropdown-toggle">Üdv, {{ loggedInUser }}</a>
                <div class="dropdown-menu rounded-0 m-0" :class="{ 'show': dropdownOpen === 'user' }">
                  <a href="#" class="dropdown-item" @click.prevent="logout">Kijelentkezés</a>
                </div>
              </div>
            </div>
            <!-- Szálláshely feltöltése gomb -->
            <span v-if="!loggedInUser" class="position-relative d-inline-block">
              <button v-if="!loggedInUser" class="btn btn-secondary" disabled>
                Szálláshely feltöltése
              </button>
              <div class="position-absolute bg-dark text-white px-2 py-1 rounded shadow-sm"
                style="top: 35px; left: 50%; transform: translateX(-50%); font-size: 14px; white-space: nowrap;"
                v-show="showUploadTooltip">
                Először be kell jelentkezned az oldalra
              </div>
            </span>

            <!-- Aktív gomb ha be van jelentkezve -->
            <button v-else class="btn btn-primary" @click="uploadSite">
              Szálláshely feltöltése
            </button>
          </div>
        </nav>
      </div>
      <!-- Navbar -->
    </div>

    <!-- Különböző oldalak dinamikus megjelenítése -->
    <div v-if="!login && !register">
      <div v-if="showAbout">
        <About />
      </div>
      <div v-else-if="!apartmentUpload">
        <HomePage v-if="!showApartmentDetails" />
        <Search @view-apartment="showApartmentDetails = $event" />
      </div>
      <div v-else>
        <ApartmentUpload />
      </div>
    </div>

    <!-- Login és Register oldalak -->
    <div v-if="login">
      <Login @login="login = false" @logged_in="logged_in" @register="registerSite" />
    </div>
    <div v-if="register">
      <Register @register="() => { register = false; login = true; }" />
    </div>

    <!-- Toast értesítés -->
    <div v-if="toast.show"
      :class="['toast-container position-fixed top-0 end-0 p-3', toast.type === 'success' ? 'text-bg-success' : 'text-bg-danger']"
      style="z-index: 9999;">
      <div class="toast show align-items-center">
        <div class="d-flex">
          <div class="toast-body">{{ toast.message }}</div>
        </div>
      </div>
    </div>

    <Footer />
  </div>
</template>

<style>
.pointer {
  cursor: pointer;
}

.fade-slide-enter-active,
.fade-slide-leave-active {
  transition: opacity 0.5s ease, transform 0.5s ease;
}

.fade-slide-enter-from,
.fade-slide-leave-to {
  opacity: 0;
  transform: translateY(20px);
}

.toast-container {
  position: fixed;
  top: 20px;
  right: 20px;
  z-index: 9999;
  min-width: 250px;
  max-width: 400px;
  background-color: #f9f9f9;
  border-left: 6px solid;
  border-radius: 10px;
  box-shadow: 0 6px 16px rgba(0, 0, 0, 0.1);
  font-family: 'Segoe UI', sans-serif;
  animation: slideIn 0.4s ease-out;
  overflow: hidden;
}

.toast-body {
  padding: 14px 18px;
  font-size: 16px;
  color: #333;
}

.toast-container.text-bg-success {
  border-color: #198754;
}

.toast-container.text-bg-danger {
  border-color: #dc3545;
}

@keyframes slideIn {
  from {
    opacity: 0;
    transform: translateY(-20px);
  }

  to {
    opacity: 1;
    transform: translateY(0);
  }
}
</style>