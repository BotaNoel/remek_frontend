<script>
import Categories from '@/components/Categories.vue';
import Highlight from '@/components/Highlight.vue';
import Offers from '@/components/Offers.vue';

export default {
    components: {
        Categories,
        Highlight,
        Offers
    },
    data() {
        return {
            types: [],
            showFilters: false,
            searchParams: {
                city: '',
                type_id: null
            },
            filters: {
                wellness: false,
                breakfast: false,
                parking: false,
                wifi: false,
                all_inclusive: false,
                near_the_beach: false,
                near_the_center: false,
                pet_friendly: false,
                smoking_allowed: false
            },
            searchQuery: null
        };
    },
    methods: {
        fetchTypes() {
            fetch("http://127.0.0.1:8000/api/types")
                .then(res => res.json())
                .then(data => { this.types = data; });
        },
        filterSite() {
            this.showFilters = !this.showFilters;
        },
        searchApartments() {
            this.searchQuery = {
                city: this.searchParams.city,
                type_id: this.searchParams.type_id,
                filters: this.filters
            };
        }
    },
    mounted() {
        this.fetchTypes();
    }
}
</script>

<template>
    <!-- Keresősáv -->
    <div class="container-fluid bg-primary text-white py-4 px-3 rounded-bottom shadow wow fadeIn" data-wow-delay="0.1s">
        <div class="container">
            <div class="row g-3 align-items-center">
                <div class="col-md-4">
                    <input type="text" v-model="searchParams.city" class="form-control border-0 py-3 px-4 shadow-sm" placeholder="Város">
                </div>
                <div class="col-md-3">
                    <select v-model="searchParams.type_id" class="form-select border-0 py-3 shadow-sm">
                        <option :value="null">Összes típus</option>
                        <option v-for="type in types" :key="type.id" :value="type.id">
                            {{ type.name }}
                        </option>
                    </select>
                </div>
                <div class="col-md-2">
                    <button @click="filterSite" class="btn btn-outline-light w-100 py-3 shadow-sm">
                        Szűrők
                    </button>
                </div>
                <div class="col-md-3">
                    <button @click="searchApartments" class="btn btn-dark w-100 py-3">Keresés</button>
                </div>
            </div>
        </div>
    </div>

    <!-- Szűrőpanel -->
    <transition name="fade">
        <div class="container my-3" v-if="showFilters">
            <div class="mb-3 p-4 bg-light rounded shadow-sm">
                <h5 class="text-primary mb-3">Szolgáltatások / Szűrők:</h5>
                <div class="row row-cols-1 row-cols-md-2 row-cols-lg-3 g-3">
                    <div v-for="(value, key) in filters" :key="key" class="form-check form-switch">
                        <input type="checkbox" class="form-check-input" v-model="filters[key]" :id="key" />
                        <label class="form-check-label" :for="key">
                            {{ key.replace(/_/g, " ").replace(/\b\w/g, l => l.toUpperCase()) }}
                        </label>
                    </div>
                </div>
            </div>
        </div>
    </transition>

    <Categories />
    <Highlight />
    <Offers :searchQuery="searchQuery"/>
</template>

<style scoped>
.fade-enter-active,
.fade-leave-active {
    transition: all 0.3s ease;
}
.fade-enter-from,
.fade-leave-to {
    opacity: 0;
    transform: translateY(-10px);
}
</style>
