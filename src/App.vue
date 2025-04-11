<script>
import Search from './components/Search.vue';
import Login from './components/Login.vue';
import Footer from './components/Footer.vue';
import ApartmentUpload from '@/components/ApartmentUpload.vue';
import logo from './assets/icon-deal.png';
import user from './assets/user.webp'
import hatter from './assets/carousel-1.jpg'; // Kép importálása


export default {
  components: {
    Search,
    Footer,
    ApartmentUpload,
    Login
  },
  data() {
    return {
      logo,
      hatter,
      user,
      navbarOpen: false,
      dropdownOpen: null,
      apartmentUpload: false,
      login :false,
      loggedInUser: '',
    }
  },
  methods: {
    toggleNavbar() {
      this.navbarOpen = !this.navbarOpen;
    },
    toggleDropdown(menu) {
      this.dropdownOpen = this.dropdownOpen === menu ? null : menu;
    },
    uploadSite(){
      this.apartmentUpload = !this.apartmentUpload
    },
    loginSite(){
      this.login = !this.login
    },
    logged_in(p) {
      this.loggedInUser = p.name;
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
            <div class="icon p-2 me-2">
              <img class="img-fluid" :src="logo" alt="Icon" style="width: 30px; height: 30px;">
            </div>
            <h1 class="m-0 text-primary">SleepIn</h1>
          </a>
          <button type="button" class="navbar-toggler" @click="toggleNavbar">
            <span class="navbar-toggler-icon"></span>
          </button>
          <div class="collapse navbar-collapse" :class="{ 'show': navbarOpen }" id="navbarCollapse">
            <div class="navbar-nav ms-auto">
              <a href="" class="nav-item nav-link active">Kezdőlap</a>
              <a href="" class="nav-item nav-link">Rólunk</a>
              <div class="nav-item dropdown" @click="toggleDropdown('kinálatunk')">
                <a href="#" class="nav-link dropdown-toggle">Kínálatunk</a>
                <div class="dropdown-menu rounded-0 m-0" :class="{ 'show': dropdownOpen === 'kinálatunk' }">
                  <a href="" class="dropdown-item">Apartmanok</a>
                  <a href="" class="dropdown-item">Családi házak</a>
                  <a href="" class="dropdown-item">Szállodák</a>
                </div>
              </div>
              <div class="nav-item dropdown" @click="toggleDropdown('oldalak')">
                <a href="#" class="nav-link dropdown-toggle">Oldalak</a>
                <div class="dropdown-menu rounded-0 m-0" :class="{ 'show': dropdownOpen === 'oldalak' }">
                  <a href="" class="dropdown-item">Testimonial</a>
                  <a href="" class="dropdown-item">404 Error</a>
                </div>
              </div>
              <a href="" class="nav-item nav-link">Elérhetőség</a>
              <a href="http://127.0.0.1:8000/login" class="nav-item nav-link text-primary pointer" id="login">Bejelentkezés/Profil</a>
            </div>
            <input type="button" class="btn btn-primary " value="Szálláshely feltöltése" @click="uploadSite()">
          </div>
        </nav>
      </div>
      <!-- Navbar -->
    </div>

    <div v-if="!login">
      <div v-if="!apartmentUpload">
            <!-- App -->
            <div class="container-fluid header bg-white p-0 mt-1">
        <div class="row g-0 align-items-center flex-column-reverse flex-md-row">
          <div class="col-md-6 p-5 mt-lg-5">
            <h1 class="display-5 animated fadeIn mb-4">Oldalunkon megtalálhatod a <span class="text-primary">tökéletes
                szállást</span> ahol élvezheted nyaralásod</h1>
            <p class="animated fadeIn mb-4 pb-2">Az oldalunkon a számodra legkedvezőbb magyar szálláshelyeket
              találhatod.</p>
          </div>
          <div class="col-md-6 animated fadeIn">
            <div class="owl-carousel header-carousel">
              <div class="owl-carousel-item">
                <img class="img-fluid" :src="hatter" alt="">
              </div>
              <div class="owl-carousel-item">
                <img class="img-fluid" src="" alt="">
              </div>
            </div>
          </div>
        </div>
      </div>
      <!-- App -->
      <Search />
    </div>
    <div v-else>
        <apartmentUpload/>
    </div>
    </div>
    <div v-else>
      <Login @login="loginSite" @logged_in="logged_in"/>
    </div>
    <Footer />
  </div>
</template>
<style>
  .pointer{
    cursor: pointer;
  }
  .fade-slide-enter-active, .fade-slide-leave-active {
    transition: opacity 0.5s ease, transform 0.5s ease;
}

.fade-slide-enter-from, .fade-slide-leave-to {
    opacity: 0;
    transform: translateY(20px);
}

</style>
