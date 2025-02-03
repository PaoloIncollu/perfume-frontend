<script>
import axios from 'axios';

export default {
    data() {
        return {
            perfumes: [],
            filteredPerfumes: [],
            selectedBrand: [], // Cambia in un array per supportare selezione multipla
            brands: [],
            currentPage: 1,
            perfumesForPage: 12,
            currentSlideBrands: 0,
            isChanging: false
        };
    },
    mounted() {
        this.getPerfumes();
    },
    computed: {
        paginatedPerfumes() {
            const startIndex = (this.currentPage - 1) * this.perfumesForPage;
            return this.filteredPerfumes.slice(startIndex, startIndex + this.perfumesForPage);
        },
        totalPages() {
            return Math.ceil(this.filteredPerfumes.length / this.perfumesForPage);
        },
        // Raggruppare i marchi in blocchi per il carosello
        groupedBrands() {
            const chunkSize = 5;
            let groups = [];
            for (let i = 0; i < this.brands.length; i += chunkSize) {
                groups.push(this.brands.slice(i, i + chunkSize));
            }
            return groups;
        }
    },
    methods: {
        getPerfumes(url = 'http://127.0.0.1:8000/api/perfumes') {
            let filterUrl = url;
            if (this.selectedBrand.length > 0) {
                filterUrl = `${url}?brands=${encodeURIComponent(this.selectedBrand.join(','))}`;
            }

            axios.get(filterUrl)
                .then(res => {
                    this.perfumes = res.data.perfumes;
                    this.brands = [...new Set(this.perfumes.map(perfume => perfume.brand))];
                    this.filteredPerfumes = this.perfumes;

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
        filterByBrand() {
            this.currentPage = 1;
            if (this.selectedBrand.length > 0) {
                this.filteredPerfumes = this.perfumes.filter(perfume =>
                    this.selectedBrand.includes(perfume.brand)
                );
            } else {
                this.filteredPerfumes = this.perfumes;
            }
        },
        goToNextPage() {
            if (this.currentPage < this.totalPages) {
                this.currentPage++;
            }
        },
        goToPrevPage() {
            if (this.currentPage > 1) {
                this.currentPage--;
            }
        },
        prevSlide() {
            if (this.currentSlideBrands > 0) {
                this.isChanging = true;
                this.currentSlideBrands--;
                setTimeout(() => {
                    this.isChanging = false;
                }, 500);
            }
        },
        nextSlide() {
            if (this.currentSlideBrands < this.groupedBrands.length - 1) {
                this.isChanging = true;
                this.currentSlideBrands++;
                setTimeout(() => {
                    this.isChanging = false;
                }, 500); 
            }
        },
        clearSelectedBrands() {
            this.selectedBrand = [];
            this.filterByBrand(); // Reset dei filtri
        }
    }
};
</script>


<template>
    <main>
        
        <div class="container mt-2 my-auto">

            <h2 class="text-center text-white text-uppercase fw-bold">

                Filtra per Brand

            </h2>

            <div class="d-flex align-items-center">

                <div class="col-auto">

                    <button @click="goToPrevPage" :disabled="currentPage === 1" class="btn btn-light p-2 mx-2 rounded-circle">

                        <i class="fa-solid fa-chevron-left fa-2xl"></i>

                    </button> 

                </div>
             
                <!-- Filtro per il Brand -->

                <div class="d-flex justify-content-center w-100">
                    

                    <div id="brandCarousel" class="carousel slide w-75">

                        <div class="d-flex justify-content-center align-items-center">

                            <div>

                                <button 
                                    class="btn btn-light mx-2 rounded-circle" 
                                    type="button" 
                                    data-bs-target="#brandCarousel" 
                                    data-bs-slide="prev"
                                    :disabled="currentSlideBrands === 0 || isChanging"
                                    @click="prevSlide">

                                        <i class="fa-solid fa-chevron-left"></i>

                                </button>

                            </div>
                            
                            <div class="carousel-inner">
                                <div v-for="(group, index) in groupedBrands" 
                                    :key="index" 
                                    class="carousel-item" 
                                    :class="{ active: index === currentSlideBrands }">
                                    
                                    <div class="d-flex flex-wrap justify-content-center">
                                        <div v-for="(brand, i) in group" :key="i">
                                            <div class="form-check">
                                                <input 
                                                    class="btn-check" 
                                                    type="checkbox" 
                                                    :id="'brand-' + (index * 5 + i)" 
                                                    :value="brand" 
                                                    v-model="selectedBrand" 
                                                    @change="filterByBrand"/>
                                                <label 
                                                    class="btn rounded-pill py-0 fw-bold"
                                                    :class="selectedBrand.includes(brand) ? 'bg-black text-white border-none' : 'bg-white border-black'"  
                                                    :for="'brand-' + (index * 5 + i)">
                                                    {{ brand }}
                                                </label>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </div>

                        
                            <div>

                                <button 
                                    class="btn btn-light mx-2 rounded-circle" 
                                    type="button" 
                                    data-bs-target="#brandCarousel" 
                                    data-bs-slide="next"
                                    :disabled="currentSlideBrands === groupedBrands.length - 1 || isChanging"
                                    @click="nextSlide">
                                    <i class="fa-solid fa-chevron-right"></i>
                                </button> 

                            </div>
                        </div>
                    </div>
                </div>

                <div class="col-auto">

                    <button @click="goToNextPage" :disabled="currentPage === totalPages" class="btn btn-light p-2 mx-2 rounded-circle">

                        <i class="fa-solid fa-chevron-right fa-2xl"></i>

                    </button>

                </div>
                
            </div>
            <div class="d-flex justify-content-center">
                <button 
                    class="btn btn-dark rounded-pill" 
                    @click="clearSelectedBrands"
                    v-if="selectedBrand.length > 0">
                    <i class="fa-solid fa-trash"></i>
                </button>
            </div>
        </div>

        <div class="container p-3 my-auto">

            <div class="row gy-3" id="perfumes">
                <div class="my-col col-sm-6 col-md-4 col-lg-3 col-xl-2" v-for="perfume in paginatedPerfumes" :key="perfume.id">
                    <div class="my-card rounded">
                        <img class="py-2" :src="perfume.img" :alt="perfume.name_perfume">

                        <div class="my-card-info rounded pt-4 px-2">
                            <div class="d-flex flex-column justify-content-center align-items-center text-center h-100">
                                <h4 class="text-uppercase">{{ perfume.brand }}</h4>
                                <h5>{{ perfume.name_perfume }}</h5>
                                <h6>{{ perfume.description }}</h6>
                                <h6>{{ perfume.size }} ml</h6>
                                <h6>{{ perfume.price }} &euro;</h6>
                            </div>
                        </div>

                    </div>
                </div>
            </div>


        </div>
        
        
    </main>
</template>

<style lang="scss" scoped>
    main {
        width: 100%;
        height: 90vh;
        overflow-y: auto;
        background-image: url('https://media.douglas.it/medias/ZAxCp7pos-00700105-1-Facciata.jpg?context=bWFzdGVyfHBvaW50LW9mLXNlcnZpY2V8NjEyMjMwfGltYWdlL2pwZWd8YUdaa0wyZzFNQzh4TmpRNU5qazJORGd5T1RJeE5DOWFRWGhEY0Rkd2IzTmZNREEzTURBeE1EVmZNVjlHWVdOamFXRjBZUzVxY0djfDA0M2M3MjAwN2ZmY2E2N2UzMDBiMTkwYzFiZTJiYTU2ZmNjYzliNDg2ZjFhMzc0YTU1MzYwN2RkY2U5NGRkOWE');
        background-size: cover;

        .my-card{
            width: 100%;
            height: 250px;
            position: relative;
            background-color: white;
            color: white;

            img{
                width: 90%;
                height: 250px;
                position: absolute;
                top: 0;
                left: 5%;
                object-fit: contain;
                object-position: center;
            }

            .my-card-info{
                display: none;
                background-color: rgba($color: #000000, $alpha: 0.5);
                width: 100%;
                height: 250px;
                position: absolute;
                top: 0;
                left: 0;
            }
        }

        .my-card:hover .my-card-info {
            display: block;
        }


    }
        
    @media (max-width: 576px) {
        .my-col{
            width: 70%;
            margin-left: auto;
            margin-right: auto;
        }
    }

</style>
