<script>
import Search from './components/Search.vue';
import Login from './components/Login.vue';
import Footer from './components/Footer.vue';
import ApartmentUpload from '@/components/ApartmentUpload.vue';
import logo from './assets/icon-deal.png';
import user from './assets/user.webp'
import HomePage from './components/HomePage.vue';
import About from './components/About.vue';


export default {
  components: {
    Search,
    Footer,
    ApartmentUpload,
    Login,
    HomePage,
    About
  },
  data() {
    return {
      logo,
      user,
      navbarOpen: false,
      dropdownOpen: null,
      apartmentUpload: false,
      login: false,
      loggedInUser: '',
      showApartmentDetails: false,
      showAbout: false,
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
      this.login = !this.login
    },
    logged_in(p) {
      this.loggedInUser = p.name;
    },
    showAboutPage() {
      this.apartmentUpload = false;
      this.login = false;
      this.showAbout = true;
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
              <a href="http://127.0.0.1:8000/login" class="nav-item nav-link text-primary pointer"
                id="login">Bejelentkezés/Profil</a>
                <a href="http://127.0.0.1:8000/register" class="nav-item nav-link text-primary pointer"
                id="login">Regisztráció</a>
            </div>
            <input type="button" class="btn btn-primary " value="Szálláshely feltöltése" @click="uploadSite()">
          </div>
        </nav>
      </div>
      <!-- Navbar -->
    </div>

    <div v-if="!login">
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
    <div v-else>
      <Login @login="loginSite" @logged_in="logged_in" />
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
</style>
