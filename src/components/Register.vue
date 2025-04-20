<template>
  <div class="container text-center mt-3 w-50">
    <div class="card">
      <h3 class="text-primary">Regisztráció</h3>
      <div class="card-body">
        <!-- Name Field -->
        <div class="mt-2">
          <label for="name" class="form-label">Név:</label>
          <input type="text" id="name" v-model="newProfile.name" class="form-control" required />
          <p v-if="errors.name" class="text-danger mt-2">{{ errors.name }}</p>
        </div>

        <!-- Email Field -->
        <div class="mt-2">
          <label for="email" class="form-label">E-mail:</label>
          <input type="email" id="email" v-model="newProfile.email" class="form-control" required />
          <p v-if="errors.email" class="text-danger mt-2">{{ errors.email }}</p>
        </div>

        <!-- Password Field -->
        <div class="mt-2">
          <label for="password" class="form-label">Jelszó:</label>
          <input type="password" id="password" v-model="newProfile.password" class="form-control" required />
          <p v-if="errors.password" class="text-danger mt-2">{{ errors.password }}</p>
        </div>

        <!-- Confirm Password Field -->
        <div class="mt-2">
          <label for="password_confirmation" class="form-label">Jelszó megerősítése:</label>
          <input type="password" id="password_confirmation" v-model="newProfile.password_confirmation"
            class="form-control" required />
          <p v-if="errors.password_confirmation" class="text-danger mt-2">{{ errors.password_confirmation }}</p>
        </div>

        <!-- Submit Button -->
        <button type="button" @click="register" class="btn btn-primary mt-2">Regisztráció</button>

        <!-- Login Link -->
        <div class="mt-5">
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newProfile: {
        name: "",
        email: "",
        password: "",
        password_confirmation: "",
      },
      errors: {},
    };
  },
  methods: {
    async register() {
      try {
        const response = await fetch("http://127.0.0.1:8000/api/register", {
          method: "POST",
          headers: {
            "Accept": "application/json",
            "Content-Type": "application/json",
          },
          body: JSON.stringify(this.newProfile)
        });

        const data = await response.json();

        if (!response.ok) {
          this.errors = data.errors || {};
          this.$root.showToast('Hiba a regisztráció során. Ellenőrizd az adatokat!', 'danger'); // ❌ Toast hiba
          return;
        }

        // ✅ Sikeres regisztráció
        this.errors = {};
        this.$root.showToast('Sikeres regisztráció! Jelentkezz be az adataiddal.', 'success');
        this.$emit("register"); // visszavált login nézetre

      } catch (error) {
        console.error("Regisztráció sikertelen:", error);
        this.$root.showToast('Hiba történt a regisztráció során.', 'danger'); // ❌ Toast hiba
      }
    }

  }
};
</script>