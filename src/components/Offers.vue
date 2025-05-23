<script>
export default {
    props: {
        searchQuery: Object
    },
    data() {
        return {
            apartments: [],
            currentApartmentId: null
        }
    },
    watch: {
        searchQuery: {
            handler(newVal) {
                if (newVal) this.loadFiltered(newVal);
            },
            immediate: true
        }
    },
    methods: {
        loadAll() {
            fetch("http://127.0.0.1:8000/api/apartments")
                .then(res => res.json())
                .then(data => {
                    this.apartments = data;
                    this.fetchRatingsForApartments();
                });
        },
        loadFiltered(query) {
            fetch("http://127.0.0.1:8000/api/apartments/search", {
                method: "POST",
                headers: {
                    "Content-Type": "application/json"
                },
                body: JSON.stringify(query)
            })
                .then(res => res.json())
                .then(data => {
                    this.apartments = data;
                    this.fetchRatingsForApartments();
                });
        },
        fetchRatingsForApartments() {
            this.apartments.forEach((apartment, index) => {
                fetch(`http://127.0.0.1:8000/api/apartments/${apartment.id}/ratings`)
                    .then(res => res.json())
                    .then(data => {
                        const ratings = data.comments.map(c => c.rating).filter(r => r !== null);
                        const avg = ratings.length > 0
                            ? (ratings.reduce((sum, r) => sum + r, 0) / ratings.length).toFixed(1)
                            : null;
                    
                        this.apartments[index].average_rating = avg;
                    });
            });
        }
    },
    mounted() {
        this.loadAll();
    }
}
</script>

<template>
    <div class="container-xxl py-5 bg-light">
        <div class="container">
            <div class="text-center mx-auto mb-5 wow fadeInUp" data-wow-delay="0.1s" style="max-width: 600px;">
                <h1 class="mb-3">Elérhető Apartmanok</h1>
                <p>Fedezd fel legnépszerűbb szálláshelyeinket, melyek tökéletes kényelmet és kiváló elhelyezkedést kínálnak.</p>
            </div>

            <div class="row g-4">
                <div v-for="apartment in apartments" :key="apartment.id" class="col-lg-4 col-md-6">
                    <div class="card h-100 shadow-sm border-0 rounded-4">
                        <img :src="apartment.cover_photo || 'https://placehold.co/400x250?text=Apartman+Kép'" class="card-img-top rounded-top-4" alt="Apartment Image">
                        <div class="card-body d-flex flex-column justify-content-between">
                            <h5 class="card-title text-primary">{{ apartment.name }}</h5>

                            <!-- Rating -->
                            <div class="mt-2">
                                <template v-if="apartment.average_rating">
                                    <span class="text-warning">
                                        <template v-for="n in 5" :key="n">
                                            <span>
                                                {{ n <= Math.round(apartment.average_rating) ? '★' : '☆' }}
                                            </span>
                                        </template>
                                    </span>
                                    <small class="text-muted ms-2">({{ apartment.average_rating }} / 5)</small>
                                </template>
                                <template v-else>
                                    <span class="text-muted">Nincs még értékelés</span>
                                </template>
                            </div>


                            <p class="card-text text-muted mt-3" style="min-height: 70px;">{{ apartment.description || 'Nincs leírás megadva.' }}</p>
                            <div class="mt-3 d-flex justify-content-between align-items-center">
                                <span class="fw-bold text-success fs-5">{{ apartment.price_per_night }} Ft / éj</span>
                                <span class="badge bg-secondary">{{ apartment.max_capacity }} fő</span>
                            </div>
                        </div>
                        <div class="card-footer bg-white border-top-0 text-end">
                            <button class="btn btn-outline-primary btn-sm rounded-pill" @click="$emit('show-details', apartment.id)">
                                Részletek
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>
.card {
    transition: transform 0.2s ease-in-out, box-shadow 0.2s ease-in-out;
}

.card:hover {
    transform: translateY(-5px);
    box-shadow: 0 8px 20px rgba(0, 0, 0, 0.12);
}

.card-img-top {
    object-fit: cover;
    height: 250px;
}
</style>
