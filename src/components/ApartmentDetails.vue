<script>
export default {
    props: ['id'],
    data() {
        return {
            apartment: null,
        };
    },
    computed: {
        activeFilters() {
            if (!this.apartment?.filters) return [];
            return Object.entries(this.apartment.filters)
                .filter(([key, v]) => v && !['id', 'apartment_id', 'created_at', 'updated_at'].includes(key))  // Kizárjuk a nem kívánt mezőket
                .map(([k]) => k.replace(/_/g, ' ').replace(/\b\w/g, l => l.toUpperCase())); // Formázás
        }
    }
    ,
    mounted() {
        this.fetchDetails();
    },
    methods: {
        fetchDetails() {
            fetch(`http://127.0.0.1:8000/api/apartments/${this.id}`)
                .then(res => res.json())
                .then(data => {
                    // Csak a szükséges adatokat töltjük be
                    this.apartment = {
                        ...data,
                        filters: data.filters || {},
                    };
                });
        }
    }
};
</script>

<template>
    <div v-if="apartment" class="container py-5">
        <div class="card shadow-lg border-0 rounded-4 overflow-hidden">
            <div class="row g-0">
                <!-- Bal oldal: Kép -->
                <div class="col-lg-6">
                    <img :src="apartment.photo || 'https://placehold.co/800x600?text=Nincs+kép'" alt="Apartment Image"
                        class="w-100 h-100 object-fit-cover" style="max-height: 500px;" />
                </div>

                <!-- Jobb oldal: Információk -->
                <div class="col-lg-6 p-4 d-flex flex-column justify-content-between">
                    <div>
                        <h2 class="text-primary fw-bold mb-3">{{ apartment.name }}</h2>
                        <p class="text-muted fst-italic mb-2">{{ apartment.type }}</p>

                        <p class="mb-2">
                            <strong>Ár / éj: </strong>
                            <span class="text-success fw-semibold">{{ apartment.price_per_night }} Ft</span>
                        </p>

                        <p class="mb-2"><strong>Max. férőhely:</strong> {{ apartment.max_capacity }} fő</p>

                        <p class="mb-4"><strong>Leírás:</strong> <br />{{ apartment.description || 'Nincs leírás.' }}
                        </p>

                        <div class="bg-light rounded-3 p-3 mb-4">
                            <h6 class="text-primary mb-2">📍 Cím</h6>
                            <p class="mb-0 text-muted">
                                {{ apartment.location.postal_code }} {{ apartment.location.city }},
                                {{ apartment.location.street }} {{ apartment.location.address_number }}
                            </p>
                        </div>
                    </div>

                    <!-- Szolgáltatások -->
                    <div class="mt-3">
                        <h6 class="text-primary mb-2">🛎️ Szolgáltatások</h6>
                        <div v-if="activeFilters.length" class="d-flex flex-wrap gap-2">
                            <span v-for="(filter, index) in activeFilters" :key="index"
                                class="badge bg-primary-subtle text-primary px-3 py-2 rounded-pill shadow-sm">
                                {{ filter }}
                            </span>
                        </div>
                        <p v-else class="text-muted fst-italic">Nincsenek megadott szolgáltatások.</p>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- Betöltés -->
    <div v-else class="text-center py-5">
        <p class="text-muted">Adatok betöltése...</p>
    </div>
</template>

<style scoped>
.card {
    background-color: #ffffff;
    transition: box-shadow 0.3s ease;
}

.card:hover {
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.08);
}

.object-fit-cover {
    object-fit: cover;
}

.badge {
    font-size: 0.9rem;
    font-weight: 500;
}
</style>
