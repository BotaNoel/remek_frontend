<script>
import Categories from '@/components/Categories.vue';
import Highlight from '@/components/Highlight.vue';
import Comments from '@/components/Comments.vue';

export default {
    components: {
        Categories,
        Highlight,
        Comments
    },
    data() {
        return {
            showFilters: false,
            types: [],
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
            }
        }
    },
    methods: {
        filterSite() {
            this.showFilters = !this.showFilters;
        },
        fetchTypes() {
            fetch("http://127.0.0.1:8000/api/types")
                .then(response => response.json())
                .then(data => {
                    this.types = data;
                })
                .catch(error => {
                    console.error("Hiba a típusok lekérésekor:", error);
                });
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
                    <input type="text" class="form-control border-0 py-3 px-4 shadow-sm" placeholder="Város">
                </div>
                <div class="col-md-3">
                    <select class="form-select border-0 py-3 shadow-sm">
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
                    <button class="btn btn-dark w-100 py-3 shadow-sm">
                        Keresés
                    </button>
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
                <div class="text-end mt-4">
                    <button class="btn btn-primary px-4 py-2">Alkalmazás</button>
                </div>
            </div>
        </div>
    </transition>

    <Categories />
    <Highlight />
    <Comments />
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
