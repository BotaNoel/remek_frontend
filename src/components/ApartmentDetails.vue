<script>
export default {
    props: ['id'],
    emits: ['back'],
    data() {
        return {
            apartment: null,
            reservations: [], // [{start_date, end_date}]
            checkIn: '',
            checkOut: '',
            today: '',
            guests: 1,
            errorMessage: '',
            userId: null,
            userName: '', // foglaló neve
            showConfirmation: false, // 🔹 popup megjelenítése
            comments: [],
            newComment: '',
            newRating: 5,
            canComment: false
        };
    },
    created() {
        const storedUser = localStorage.getItem("user");
        if (storedUser) {
            const parsedUser = JSON.parse(storedUser); // 🔹 ez hiányzott
            this.userId = parsedUser.id;
            this.userName = parsedUser.name;
        }
    },
    computed: {
        isDateRangeAvailable() {
            if (!this.checkIn || !this.checkOut) return false;
            const start = new Date(this.checkIn);
            const end = new Date(this.checkOut);

            // Ellenőrizze, hogy az új foglalás nem ütközik-e bármely meglévő időszakkal
            return !this.reservations.some(res => {
                const resStart = new Date(res.start_date);
                const resEnd = new Date(res.end_date);
                return (start <= resEnd && end >= resStart); // átfedés
            });
        }
    },
    mounted() {
        this.fetchApartment();
        this.fetchReservations();
        this.fetchComments();
        this.checkIfUserCanComment();

        const now = new Date();
        this.today = now.toISOString().split('T')[0];
    },
    methods: {
        fetchApartment() {
            fetch(`http://127.0.0.1:8000/api/apartments/${this.id}`)
                .then(res => res.json())
                .then(data => {
                    this.apartment = data;
                })
                .catch(() => {
                    this.errorMessage = 'Nem sikerült betölteni az apartman adatait.';
                });
        },
        fetchReservations() {
            fetch(`http://127.0.0.1:8000/api/apartments/${this.id}/orders`)
                .then(res => res.json())
                .then(data => {
                    this.reservations = data;
                });
        },
        bookApartment() {
            this.errorMessage = '';

            if (!this.checkIn || !this.checkOut) {
                this.errorMessage = 'Kérjük, válassza ki az érkezési és távozási dátumot!';
                return;
            }
            if (new Date(this.checkIn) >= new Date(this.checkOut)) {
                this.errorMessage = 'A távozás dátuma később kell legyen, mint az érkezés!';
                return;
            }
            if (!this.isDateRangeAvailable) {
                this.errorMessage = 'Ez az időszak már foglalt!';
                return;
            }
            if (this.guests > this.apartment.max_capacity) {
                this.errorMessage = `A személyek száma nem haladhatja meg a ${this.apartment.max_capacity}-et.`;
                return;
            }

            this.showConfirmation = true; // 🔹 popup megnyitása
        },
        confirmBooking() {
            const days = (new Date(this.checkOut) - new Date(this.checkIn)) / (1000 * 60 * 60 * 24);
            const totalPrice = days * this.apartment.price_per_night;

            fetch(`http://127.0.0.1:8000/api/apartments/${this.id}/orders`, {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({
                    arrival_date: this.checkIn,
                    departure_date: this.checkOut,
                    headcount: this.guests,
                    user_id: this.userId
                })
            })
                .then(res => res.json())
                .then(() => {
                    this.fetchReservations();
                    this.checkIn = '';
                    this.checkOut = '';
                    this.guests = 1;
                    this.showConfirmation = false;
                })
                .catch(() => {
                    this.errorMessage = 'Hiba történt a foglalás során.';
                    this.showConfirmation = false;
                });
        },
        fetchComments() {
            fetch(`http://127.0.0.1:8000/api/apartments/${this.id}/comments`)
                .then(res => res.json())
                .then(data => {
                    this.comments = data.comments;
                });
        },
        checkIfUserCanComment() {
            if (!this.userId) return;

            fetch(`http://127.0.0.1:8000/api/apartments/${this.id}/has-booked/${this.userId}`)
                .then(res => res.json())
                .then(data => {
                    this.canComment = data.hasBooked === true;
                });
        },
        submitComment() {
            if (!this.newComment || !this.newRating) return;

            // 1. rating mentése
            fetch('http://127.0.0.1:8000/api/ratings', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                    Authorization: `Bearer ${localStorage.getItem("token")}`
                },
                body: JSON.stringify({
                    apartment_id: this.id,
                    score: this.newRating
                })
            })
                .then(res => res.json())
                .then(ratingData => {
                    if (ratingData.error) {
                        alert(ratingData.error);
                        return;
                    }

                    // 2. comment mentése rating_id-vel
                    return fetch(`http://127.0.0.1:8000/api/apartments/${this.id}/comments`, {
                        method: 'POST',
                        headers: {
                            'Content-Type': 'application/json',
                            Authorization: `Bearer ${localStorage.getItem("token")}`
                        },
                        body: JSON.stringify({
                            apartment_id: this.id,
                            content: this.newComment,
                            rating_id: ratingData.rating.id
                        })
                    });
                })
                .then(() => {
                    this.newComment = '';
                    this.newRating = 5;
                    this.fetchComments();
                })
                .catch(error => {
                    console.error('Hiba történt:', error);
                });
        }
    }
};
</script>

<template>
    <div v-if="apartment" class="container py-5">
        <div class="mb-3">
            <button class="btn btn-outline-primary" @click="$emit('back')">
                Vissza
            </button>
        </div>
        <div class="card shadow-lg border-0 rounded-4 overflow-hidden">
            <div class="row g-0">
                <!-- Bal oldal: Kép -->
                <div class="col-lg-6">
                    <div v-if="apartment.photos.length > 1" id="photoCarousel" class="carousel slide"
                        data-bs-ride="carousel">
                        <div class="carousel-inner">
                            <div v-for="(photo, index) in apartment.photos" :key="index"
                                :class="['carousel-item', { active: index === 0 }]">
                                <img :src="photo.url" class="d-block w-100"
                                    style="max-height: 500px; object-fit: cover;" alt="Kép">
                            </div>
                        </div>
                        <button class="carousel-control-prev" type="button" data-bs-target="#photoCarousel"
                            data-bs-slide="prev">
                            <span class="carousel-control-prev-icon" aria-hidden="true"></span>
                            <span class="visually-hidden">Előző</span>
                        </button>
                        <button class="carousel-control-next" type="button" data-bs-target="#photoCarousel"
                            data-bs-slide="next">
                            <span class="carousel-control-next-icon" aria-hidden="true"></span>
                            <span class="visually-hidden">Következő</span>
                        </button>
                    </div>

                    <div v-else>
                        <img :src="apartment.photos[0]?.url || 'https://placehold.co/800x600?text=Nincs+kép'"
                            alt="Apartment Image" class="w-100 h-100 object-fit-cover" style="max-height: 500px;" />
                    </div>
                    <!-- 🔹 Értékelések & kommentek -->
                    <div class="mt-5">
                        <h5 class="text-primary mb-3">⭐ Értékelések és hozzászólások</h5>

                        <!-- Komment lista -->
                        <div v-if="comments.length">
                            <div v-for="(comment, index) in comments" :key="index" class="border-bottom pb-3 mb-3">
                                <p class="mb-1 d-flex align-items-center gap-2">
                                    <i class="bi bi-person-circle text-primary fs-5"></i>
                                    <strong>{{ comment.user }}</strong>
                                    <span class="text-warning">
                                        <span v-for="n in comment.rating" :key="n">★</span>
                                        <span v-for="n in 5 - comment.rating" :key="'empty-' + n"
                                            class="text-muted">★</span>
                                    </span>
                                </p>
                                <p class="mb-0">{{ comment.content }}</p>
                            </div>

                        </div>
                        <p v-else class="text-muted">Még nem érkezett hozzászólás ehhez az apartmanhoz.</p>

                        <!-- Új komment beküldése -->
                        <div v-if="canComment" class="mt-4">
                            <h6 class="mb-2">Írj te is véleményt!</h6>
                            <div class="mb-2">
                                <label for="rating" class="form-label">Értékelés (1–5):</label>
                                <select v-model="newRating" id="rating" class="form-select" style="max-width: 100px;">
                                    <option v-for="n in 5" :key="n" :value="n">{{ n }}</option>
                                </select>
                            </div>
                            <div class="mb-2">
                                <label class="form-label">Hozzászólás:</label>
                                <textarea v-model="newComment" class="form-control" rows="3"></textarea>
                            </div>
                            <button class="btn btn-primary" @click="submitComment">Küldés</button>
                        </div>
                        <div v-else class="text-muted mt-2">
                            Csak akkor írhat véleményt, ha már foglalt ennél az apartmannál.
                        </div>
                    </div>

                </div>

                <!-- Jobb oldal: Információk -->
                <div class="col-lg-6 p-4 d-flex flex-column justify-content-between">
                    <div>
                        <h2 class="text-primary fw-bold mb-3">{{ apartment.name }}</h2>
                        <p class="text-muted fst-italic mb-2">{{ apartment.type }}</p>
                        <p class="mb-2">
                            <strong>Feltöltötte:</strong> {{ apartment.uploader }}
                        </p>

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

                        <!-- Szűrők -->
                        <div v-if="apartment.filters" class="mb-4">
                            <h6 class="text-primary mb-2">🧩 Szolgáltatások</h6>
                            <div class="d-flex flex-wrap gap-2">
                                <span v-if="apartment.filters.wellness" class="badge bg-primary">Wellness</span>
                                <span v-if="apartment.filters.breakfast" class="badge bg-primary">Reggeli</span>
                                <span v-if="apartment.filters.parking" class="badge bg-primary">Parkoló</span>
                                <span v-if="apartment.filters.wifi" class="badge bg-primary">Wi-Fi</span>
                                <span v-if="apartment.filters.all_inclusive" class="badge bg-primary">All
                                    Inclusive</span>
                                <span v-if="apartment.filters.near_the_beach" class="badge bg-primary">Tengerpart
                                    közelében</span>
                                <span v-if="apartment.filters.near_the_center" class="badge bg-primary">Belváros
                                    közelében</span>
                                <span v-if="apartment.filters.pet_friendly" class="badge bg-primary">Állatbarát</span>
                                <span v-if="apartment.filters.smoking_allowed" class="badge bg-primary">Dohányzás
                                    megengedett</span>
                            </div>
                        </div>
                    </div>

                    <!-- Foglalási űrlap -->
                    <div class="mt-3">
                        <h6 class="text-primary mb-3">🗓️ Foglalás</h6>

                        <div class="mb-3">
                            <label class="form-label">Érkezés:</label>
                            <input type="date" v-model="checkIn" class="form-control" :min="today" />
                        </div>

                        <div class="mb-3">
                            <label class="form-label">Távozás:</label>
                            <input type="date" v-model="checkOut" class="form-control" :min="checkIn || today" />
                        </div>

                        <div class="mb-3">
                            <label class="form-label">Személyek száma:</label>
                            <input type="number" v-model.number="guests" class="form-control"
                                :max="apartment.max_capacity" />
                        </div>

                        <button class="btn btn-primary w-100" @click="bookApartment">Foglalás</button>
                        <p v-if="errorMessage" class="text-danger mt-2">{{ errorMessage }}</p>
                    </div>

                    <!-- Foglalási állapot -->
                    <div class="mt-4">
                        <h6 class="text-primary mb-2">🔖 Foglalási állapot</h6>
                        <ul class="list-group">
                            <li v-for="(reservation, index) in reservations" :key="index"
                                class="list-group-item d-flex justify-content-between align-items-center">
                                {{ reservation.arrival_date }} – {{ reservation.departure_date }}
                                <span class="badge bg-danger text-white">Foglalt</span>
                            </li>
                        </ul>
                        <p v-if="!reservations.length" class="text-muted mt-2">Még nincsenek foglalások.</p>
                    </div>
                </div>
            </div>
        </div>
        <!-- Pop-up -->
        <div v-if="showConfirmation"
            class="position-fixed top-0 start-0 w-100 h-100 bg-dark bg-opacity-50 d-flex justify-content-center align-items-center z-3">
            <div class="bg-white rounded-4 shadow-lg p-4" style="max-width: 500px; width: 100%;">
                <h4 class="text-primary mb-3">Foglalás megerősítése</h4>
                <ul class="list-group list-group-flush mb-3">
                    <li class="list-group-item"><strong>Felhasználó:</strong> {{ userName }}</li>
                    <li class="list-group-item"><strong>Apartman:</strong> {{ apartment.name }}</li>
                    <li class="list-group-item"><strong>Érkezés:</strong> {{ checkIn }}</li>
                    <li class="list-group-item"><strong>Távozás:</strong> {{ checkOut }}</li>
                    <li class="list-group-item"><strong>Személyek:</strong> {{ guests }}</li>
                    <li class="list-group-item">
                        <strong>Fizetendő összeg:</strong>
                        {{ (new Date(checkOut) - new Date(checkIn)) / (1000 * 60 * 60 * 24) * apartment.price_per_night
                        }}
                        Ft
                    </li>
                </ul>
                <div class="d-flex justify-content-end gap-2">
                    <button class="btn btn-secondary" @click="showConfirmation = false">Mégsem</button>
                    <button class="btn btn-success" @click="confirmBooking">Rendben, fizetek</button>
                </div>
            </div>
        </div>
    </div>

    <!-- Betöltés -->
    <div v-else class="text-center py-5">
        <p class="text-muted">Loading...</p>
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
    font-size: 0.85rem;
    padding: 0.5em 0.75em;
}
</style>
