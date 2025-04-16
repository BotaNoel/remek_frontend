<script>
    import hatter from '@/assets/carousel-1.jpg'; // Kép importálása
    export default{
        data(){
            return{
                hatter
            }
        }
    }
</script>
<template>
    <div class="container-fluid header bg-white p-0 mt-1">
        <div class="row g-0 align-items-center flex-column-reverse flex-md-row">
            <div class="col-md-6 p-5 mt-lg-5">
                <h1 class="display-5 animated fadeIn mb-4">Oldalunkon megtalálhatod a <span
                        class="text-primary">tökéletes
                        szállást</span> ahol élvezheted nyaralásod</h1>
                <p class="animated fadeIn mb-4 pb-2">Az oldalunkon a számodra legkedvezőbb magyar szálláshelyeket
                    találhatod.</p>
            </div>
            <div class="col-md-6 animated fadeIn">
                <div class="owl-carousel header-carousel">
                    <div class="owl-carousel-item">
                        <img class="img-fluid" :src="hatter" alt="">
                    </div>
                    <div class="owl-carousel-item">
                        <img class="img-fluid" src="" alt="">
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<style></style>