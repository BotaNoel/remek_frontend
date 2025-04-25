<script>
    export default {
      name: 'ContactInfo',
      data() {
        return {
          contactDetails: [
            {
              title: 'Cím',
              value: 'Eger, Cifrakapu u. 158, 3300',
              icon: 'fas fa-map-marker-alt'
            },
            {
              title: 'Telefon',
              value: '+36 30 880 0080',
              icon: 'fas fa-phone-alt'
            },
            {
              title: 'Email',
              value: 'botanoel05gmail.com vagy bekecs.szabolcs@nejanet.hu',
              icon: 'fas fa-envelope'
            },
            {
              title: 'Nyitvatartás',
              value: 'Hétfő - Péntek: 9:00 - 17:00',
              icon: 'fas fa-clock'
            }
          ]
        };
      }
    };
</script>

<template>
    <section class="contact-section py-5">
      <div class="container">
        <div class="text-center mb-5">
          <h2 class="section-title">Elérhetőségeink</h2>
          <p class="section-subtitle">Lépj velünk kapcsolatba bármilyen kérdés esetén!</p>
        </div>
        <div class="row g-4 justify-content-center">
          <div class="col-md-6 col-lg-4" v-for="(info, index) in contactDetails" :key="index">
            <div class="contact-card shadow-sm p-4 h-100 text-center rounded">
              <div class="icon-wrapper mb-3">
                <i :class="info.icon" class="fs-2 text-primary"></i>
              </div>
              <h5 class="mb-2">{{ info.title }}</h5>
              <p class="mb-0 text-muted">{{ info.value }}</p>
            </div>
          </div>
        </div>
      </div>
    </section>
</template>

<style scoped>
.contact-section {
  background-color: #f8f9fa;
}

.section-title {
  font-size: 2.2rem;
  font-weight: 600;
  color: #2c3e50;
}

.section-subtitle {
  color: #6c757d;
  font-size: 1.1rem;
}

.contact-card {
  background: #ffffff;
  border: 1px solid #e0e0e0;
  transition: transform 0.2s ease, box-shadow 0.2s ease;
}

.contact-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.05);
}

.icon-wrapper i {
  color: #0d6efd;
}
</style>