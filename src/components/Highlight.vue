<script>
    import highlighted from '@/assets/highlighted.jpg';

    export default {
        data() {
            return {
                highlighted,
                apartments: {},
                location: {},
                filters: {},
                highlightedPhoto: {}
            };
        },
        methods: {
            loadApartment() {
                fetch("http://127.0.0.1:8000/api/apartments")
                    .then(res => res.json())
                    .then(data => {
                        if (data.length > 0) {  // Ha van adat
                            this.apartments = data[0];
                            this.location = this.apartments.location;
                            this.highlightedPhoto = this.apartments.photos[0];
                            this.filters = this.apartments.filters;
                        } else {
                            this.apartments = null;  // Ha nincs adat, null-ra állítjuk
                        }
                    });
            },
        },
        mounted() {
            this.loadApartment();
        }
    };
</script>

<template>
    <div v-if="apartments">  <!-- Csak akkor jelenik meg, ha van adat -->
        <div class="container-xxl py-5">
            <div class="container">
                <div class="row g-5 align-items-center">
                    <div class="col-lg-6 wow fadeIn" data-wow-delay="0.1s">
                        <div class="about-img position-relative overflow-hidden p-5 pe-0">
                            <img class="img-fluid w-100" :src="highlightedPhoto.url" alt="Image">
                        </div>
                    </div>
                    <div class="col-lg-6 wow fadeIn" data-wow-delay="0.5s">
                        <h1 class="mb-4">Kiemelt ajánlatunk</h1>
                        <h3 class="mb-4 text-primary">{{apartments.name}}</h3>
                        <p style="font-size: large; font-weight: bold;" class="mb-4"><span class="text-primary">Elhelyezkedés:</span> <br> {{location.city}} {{location.street}}, {{location.address_number}}</p>
                        <p class="mb-4">{{ apartments.description }}</p>
                        <p v-if="filters.all_inclusive == 1"><i class="fa fa-check text-primary me-3"></i>All-inclusive</p>
                        <p v-if="filters.breakfast == 1"><i class="fa fa-check text-primary me-3"></i>Reggeli az árban</p>
                        <p v-if="filters.near_the_beach == 1"><i class="fa fa-check text-primary me-3"></i>Közel a parthoz</p>
                        <p v-if="filters.near_the_center == 1"><i class="fa fa-check text-primary me-3"></i>Közel a központhoz</p>
                        <p v-if="filters.parking == 1"><i class="fa fa-check text-primary me-3"></i>Parkoló</p>
                        <p v-if="filters.pet_friendly == 1"><i class="fa fa-check text-primary me-3"></i>Állatbarát</p>
                        <p v-if="filters.wellness == 1"><i class="fa fa-check text-primary me-3"></i>Wellness</p>
                        <p v-if="filters.wifi == 1"><i class="fa fa-check text-primary me-3"></i>Wifi</p>
                        <a class="btn btn-primary py-3 px-5 mt-3" @click="$emit('show-details', apartments.id)">Tudj meg többet</a>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>
</style>
