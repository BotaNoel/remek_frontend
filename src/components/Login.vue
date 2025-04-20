<template>
  <div class="container mt-3 text-center w-50">
    <div class="card">
      <h3 class="card-title text-primary">Bejelentkezés</h3>
      <div class="card-body">
        <!-- Email Field -->
        <div class="mt-2">
          <label for="email" class="form-label">E-mail:</label>
          <input ref="emailInput" type="email" id="email" v-model="user.email" class="form-control" required />
          <p v-if="errors.email" class="text-danger mt-2">{{ errors.email }}</p>
        </div>

        <!-- Password Field -->
        <div class="mt-4">
          <label for="password" class="form-label">Jelszó:</label>
          <input type="password" id="password" v-model="user.password" class="form-control" required />
          <p v-if="errors.password" class="text-danger mt-2">{{ errors.password }}</p>
        </div>

        <!-- Login Button -->
        <button type="button" @click="login" class="btn btn-primary mt-4">Bejelentkezés</button>

        <!-- Register Link -->
        <div class="mt-4">
          <p>Még nincs fiókod?
            <button class="btn btn-link p-0 m-0 align-baseline text-primary" @click="$emit('register')">Regisztrálj
              most!</button>
          </p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      user: {
        email: "",
        password: "",
        remember: false,
      },
      errors: {},
    };
  },
  methods: {
    async login() {
      this.errors = {};  // Clear previous errors
      console.log("Attempting to log in with:", this.user);

      try {
        const response = await fetch("http://127.0.0.1:8000/api/login", {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify({
            email: this.user.email,
            password: this.user.password,
          }),
        });

        const data = await response.json();
        console.log("Login response data:", data);

        if (response.ok && data.token) {
          localStorage.setItem("token", data.token);
          localStorage.setItem("user", JSON.stringify(data.user));

          this.$emit("login"); // Login panel eltűnik
          this.$emit("logged_in", data.user); // Felhasználói adat továbbítva
          this.$root.showToast('Sikeres bejelentkezés! Üdv újra itt!', 'success');
        } else {
          this.errors = data.errors || { general: 'Hibás e-mail vagy jelszó' };
          this.$root.showToast('Hibás e-mail vagy jelszó!', 'danger'); // <--- Itt jelenik meg a toast hiba
        }
      } catch (error) {
        console.error("Bejelentkezési hiba:", error);
        alert("Hiba történt a bejelentkezés során");
      }
    },
  },
  mounted() {
    // Focus the email input field after the component is mounted
    this.$refs.emailInput.focus();
  },
};
</script>