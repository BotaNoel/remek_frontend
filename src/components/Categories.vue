<script>
import iconApartment from '@/assets/icon-apartment.png';
import iconLuxury from '@/assets/icon-luxury.png';
import iconHotel from '@/assets/icon-condominium.png';
import iconHouse from '@/assets/icon-neighborhood.png';

export default {
    data() {
        return {
            iconApartment,
            iconLuxury,
            iconHotel,
            iconHouse,
            typeCounts: {
                1: 0, // Apartman
                2: 0, // Luxus ingatlan
                3: 0, // Szálloda
                4: 0  // Családi ház
            }
        }
    },
    methods: {
        async fetchApartmentTypes() {
            try {
                const res = await fetch("http://127.0.0.1:8000/api/apartments");
                const data = await res.json();

                // Ürítjük a korábbi értékeket
                this.typeCounts = { 1: 0, 2: 0, 3: 0, 4: 0 };

                // Végigmegyünk az adatbázisból jött apartmanokon
                data.forEach(apartment => {
                    if (this.typeCounts[apartment.type_id] !== undefined) {
                        this.typeCounts[apartment.type_id]++;
                    }
                });

            } catch (error) {
                console.error("Hiba a lekérés során:", error);
            }
        }
    },
    mounted() {
        this.fetchApartmentTypes();
    }
}
</script>

<template>
    <div class="container-xxl py-5">
        <div class="container">
            <div class="text-center mx-auto mb-5 wow fadeInUp" data-wow-delay="0.1s" style="max-width: 600px;">
                <h1 class="mb-3">Szállástípusok</h1>
                <p>Válaszd ki azt a szállástípust, ami megfelelő neked</p>
            </div>
            <div class="row g-4">
                <div class="col-lg-3 col-sm-6 wow fadeInUp" data-wow-delay="0.1s">
                    <a class="cat-item d-block bg-light text-center rounded p-3" href="">
                        <div class="rounded p-4">
                            <div class="icon mb-3">
                                <img class="img-fluid" :src="iconApartment" alt="Apartment Icon">
                            </div>
                            <h6>Apartman</h6>
                            <span>{{ typeCounts[1] }} található</span>
                        </div>
                    </a>
                </div>
                <div class="col-lg-3 col-sm-6 wow fadeInUp" data-wow-delay="0.3s">
                    <a class="cat-item d-block bg-light text-center rounded p-3" href="">
                        <div class="rounded p-4">
                            <div class="icon mb-3">
                                <img class="img-fluid" :src="iconLuxury" alt="Luxury Icon">
                            </div>
                            <h6>Luxus ingatlan</h6>
                            <span>{{ typeCounts[2] }} található</span>
                        </div>
                    </a>
                </div>
                <div class="col-lg-3 col-sm-6 wow fadeInUp" data-wow-delay="0.1s">
                    <a class="cat-item d-block bg-light text-center rounded p-3" href="">
                        <div class="rounded p-4">
                            <div class="icon mb-3">
                                <img class="img-fluid" :src="iconHotel" alt="Hotel Icon">
                            </div>
                            <h6>Szálloda</h6>
                            <span>{{ typeCounts[3] }} található</span>
                        </div>
                    </a>
                </div>
                <div class="col-lg-3 col-sm-6 wow fadeInUp" data-wow-delay="0.3s">
                    <a class="cat-item d-block bg-light text-center rounded p-3" href="">
                        <div class="rounded p-4">
                            <div class="icon mb-3">
                                <img class="img-fluid" :src="iconHouse" alt="House Icon">
                            </div>
                            <h6>Családi ház</h6>
                            <span>{{ typeCounts[4] }} található</span>
                        </div>
                    </a>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>
.cat-item {
    transition: transform 0.2s ease, box-shadow 0.2s ease;
}
.cat-item:hover {
    transform: translateY(-5px);
    box-shadow: 0 10px 20px rgba(0, 0, 0, 0.1);
}
.icon img {
    width: 60px;
    height: 60px;
}
</style>
