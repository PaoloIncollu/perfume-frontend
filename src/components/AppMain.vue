<script>
import axios from 'axios';

export default {
    data() {
        return {
            perfumes: [],
            prevPage: null,
            nextPage: null,
            clickedButton: false
        };
    },
    mounted() {
        this.getPerfumes();
    },
    methods: {
        getPerfumes(url = 'http://127.0.0.1:8000/api/perfumes') {
            axios.get(url)
                .then(res => {
                    this.perfumes = res.data.perfumes.data;
                    this.prevPage = res.data.perfumes.prev_page_url;
                    this.nextPage = res.data.perfumes.next_page_url;

                    this.perfumes.forEach(perfume => {
                        if (perfume.img) {
                            if (perfume.img.startsWith('http') || perfume.img.startsWith('https')) {
                                perfume.img = perfume.img;
                            } else {
                                perfume.img = `http://127.0.0.1:8000/storage/${perfume.img}`;
                            }
                        } else {
                            perfume.img = this.src;
                        }
                    });
                });
        },

        ToPrevPage() {
            if (this.prevPage && !this.clickedButton) {
                this.clickedButton = true;
                this.getPerfumes(this.prevPage);
                this.clickedButton = false;
            }
        },

        ToNextPage() {
            if (this.nextPage && !this.clickedButton) {
                this.clickedButton = true;
                this.getPerfumes(this.nextPage);
                this.clickedButton = false;
            }
        }
    }
};
</script>


<template>
    <main>
        <div class="container p-5">

            <div class="d-flex justify-content-center mb-3" v-if="perfumes && perfumes.length > 0">
                <div>
                    <button @click="ToPrevPage()"
                        :disabled="prevPage == null || clickedButton"
                        class="my-btn p-2 border-dark-subtle mx-2 rounded-circle"
                        type="button"><i class="fa-solid fa-chevron-left fa-2xl"></i>
                    </button>
                </div>
                <div>
                    <button @click="ToNextPage()"
                        :disabled="nextPage == null || clickedButton"
                        class="my-btn p-2 border-dark-subtle mx-2 rounded-circle"
                        type="button"><i class="fa-solid fa-chevron-right fa-2xl"></i>
                    </button>
                </div>
            </div>

            <div class="row" id="perfumes">

                <div class="col-sm-12 col-md-6 col-lg-3 mb-5" v-for="perfume in perfumes" :key="perfume.id">

                    <div class="my-card card d-flex  p-2 align-self-stretch flex-grow-2 mx-0">
                        <img class="card-img-top h-300" :src="perfume.img" :alt="perfume.name_perfume">

                        <div class="card-body text-center">
                            <h6 class="card-title mb-3 fw-bold">{{ perfume.name_perfume }}</h6>
                            <h5><small class="fw-bold">Marca: </small>{{ perfume.brand }}</h5>
                            <h5><small class="fw-bold">Caratteristiche: </small>{{ perfume.description }}</h5>
                            <h5><small class="fw-bold">Prezzo: </small>{{ perfume.price }} &euro;</h5>
                        </div>

                    </div>
                </div>

            </div>

            <div class="d-flex justify-content-center mb-3" v-if="perfumes && perfumes.length > 0">
                <div>
                    <button @click="ToPrevPage()"
                        :disabled="prevPage == null || clickedButton"
                        class="my-btn p-2 border-dark-subtle mx-2 rounded-circle"
                        type="button"><i class="fa-solid fa-chevron-left fa-2xl"></i>
                    </button>
                </div>
                <div>
                    <button @click="ToNextPage()"
                        :disabled="nextPage == null || clickedButton"
                        class="my-btn p-2 border-dark-subtle mx-2 rounded-circle"
                        type="button"><i class="fa-solid fa-chevron-right fa-2xl"></i>
                    </button>
                </div>
            </div>

        </div>
    </main>
</template>

<style lang="scss" scoped>
    main {
        width: 100%;
        height: 85vh;
        overflow-y: auto;
        background-image: url('https://media.douglas.it/medias/ZAxCp7pos-00700105-1-Facciata.jpg?context=bWFzdGVyfHBvaW50LW9mLXNlcnZpY2V8NjEyMjMwfGltYWdlL2pwZWd8YUdaa0wyZzFNQzh4TmpRNU5qazJORGd5T1RJeE5DOWFRWGhEY0Rkd2IzTmZNREEzTURBeE1EVmZNVjlHWVdOamFXRjBZUzVxY0djfDA0M2M3MjAwN2ZmY2E2N2UzMDBiMTkwYzFiZTJiYTU2ZmNjYzliNDg2ZjFhMzc0YTU1MzYwN2RkY2U5NGRkOWE');
        background-size: cover;

        .my-btn{

            background-color: darkgray;

        }

        .my-card{

            background-color: darkgray;
            color: white;
        }
    }
</style>
