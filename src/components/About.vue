<script>
export default {
  name: 'About'
}
</script>
<template>
    <section class="about-section py-5 bg-light">
      <div class="container">
        <div class="row justify-content-center">
          <div class="col-lg-10 text-center">
            <h2 class="text-primary mb-4">Rólunk</h2>
            <p class="lead mb-4">
              A <strong>SleepIn</strong> egy modern és felhasználóbarát webalkalmazás, amelynek célja, hogy segítsen a látogatóknak könnyedén megtalálni a számukra ideális szállást Magyarországon. Legyen szó nyaralásról, üzleti útról vagy egy hétvégi kiruccanásról – oldalunkon egyszerűen böngészhetsz apartmanok, családi házak, luxus ingatlanok és szállodák között.
            </p>
            <p class="mb-4">
              Az oldal a Neumann János Gimnázium, Technikum és Kollégium két végzős 13.C osztályos tanulója által készült, a 2025-ös <strong>Vizsgaremek projektfeladat</strong> részeként. A fejlesztés során célunk az volt, hogy egy valós igényekre reflektáló, működőképes és letisztult megjelenésű alkalmazást hozzunk létre.
            </p>
            <p class="mb-4">
              A projekt során <strong>Laravel</strong> alapú backendet, <strong>Vue.js</strong> frontend keretrendszert, valamint <strong>MySQL</strong> adatbázist használtunk. Ezek a technológiák biztosították az alkalmazás modern és megbízható működését, a hatékony adatkezelést és a könnyen bővíthető struktúrát.
            </p>
            <p class="mb-4">
              A rendszer saját keresőmotorral, szűrési lehetőségekkel és foglalási rendszerrel lett felszerelve. A felhasználók egyszerűen kereshetnek város, filterek vagy szállástípus alapján, majd közvetlenül a felületen keresztül tudják leadni foglalásaikat.
            </p>
            <p class="fst-italic">A projekt készítői: Bóta Balázs Noel & Bekecs Szabolcs</p>
          </div>
        </div>
      </div>
    </section>
</template>
<style scoped>
.about-section {
  background-color: #f8f9fa;
}
.about-section p {
  font-size: 1.1rem;
  line-height: 1.7;
}
.about-section h2 {
  font-weight: bold;
}
</style>