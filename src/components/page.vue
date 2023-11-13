<script setup>

import { ref, computed, onMounted, onBeforeMount } from "vue";
const genres = ref([
    {
        "id": 28,
        "name": "Action"
    },
    {
        "id": 12,
        "name": "Adventure"
    },
    {
        "id": 16,
        "name": "Animation"
    },
    {
        "id": 35,
        "name": "Comedy"
    },
    {
        "id": 80,
        "name": "Crime"
    },
    {
        "id": 99,
        "name": "Documentary"
    },
    {
        "id": 18,
        "name": "Drama"
    },
    {
        "id": 10751,
        "name": "Family"
    },
    {
        "id": 14,
        "name": "Fantasy"
    },
    {
        "id": 36,
        "name": "History"
    },
    {
        "id": 27,
        "name": "Horror"
    },
    {
        "id": 10402,
        "name": "Music"
    },
    {
        "id": 9648,
        "name": "Mystery"
    },
    {
        "id": 10749,
        "name": "Romance"
    },
    {
        "id": 878,
        "name": "Science Fiction"
    },
    {
        "id": 10770,
        "name": "TV Movie"
    },
    {
        "id": 53,
        "name": "Thriller"
    },
    {
        "id": 10752,
        "name": "War"
    },
    {
        "id": 37,
        "name": "Western"
    }]);
const search = ref('')
const dataList = ref(null);
const movieDetail = ref(null);
const movieId = ref(330081);

const getOffice = computed(() => {
    let price = movieDetail.value.revenue.toString()
    let price1 = price.substring(0, 2)
    let price2 = price.substring(2, 5)
    let price3 = price.substring(5, 8)

    return `$${price1},${price2},${price3}`
})

const getId = (data) => {
    console.log(data);
    movieId.value = data.id
    detail()
    dataList.value = null
    search.value = ''
}

const detail = () => {
    const options = {
        method: 'GET',
        headers: {
            accept: 'application/json',
            Authorization: 'Bearer eyJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJjZWI5NTg3ZGZmMGIyZjFkZmUxYmY2ZTlmZTY1NmExYyIsInN1YiI6IjY1NTBjMzU2NjdiNjEzNDVkYmJlYjkxMiIsInNjb3BlcyI6WyJhcGlfcmVhZCJdLCJ2ZXJzaW9uIjoxfQ.hW3o83FtfBCECF9NmsEVMe4nXtDMLTi1pyOwOeEDLSU'
        }
    };

    fetch(`https://api.themoviedb.org/3/movie/${movieId.value}?language=en-US`, options)
        .then(response => response.json())
        .then(response => {
            console.log(response)
            movieDetail.value = response
        })
        .catch(err => console.error(err));
};

const fetchData = () => {
    // console.log(111);
    const options = {
        method: 'GET',
        headers: {
            accept: 'application/json',
            Authorization: 'Bearer eyJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJjZWI5NTg3ZGZmMGIyZjFkZmUxYmY2ZTlmZTY1NmExYyIsInN1YiI6IjY1NTBjMzU2NjdiNjEzNDVkYmJlYjkxMiIsInNjb3BlcyI6WyJhcGlfcmVhZCJdLCJ2ZXJzaW9uIjoxfQ.hW3o83FtfBCECF9NmsEVMe4nXtDMLTi1pyOwOeEDLSU'
        }
    };
    fetch(`https://api.themoviedb.org/3/search/movie?query=${search.value}&include_adult=false&language=en-US&page=1`, options)
        .then(res => res.json())
        .then(res => {
            // console.log(res);
            dataList.value = res.results.splice(0, 5)
        })
        .catch(error => {
            console.error('Error fetching data:', error);
        })
};

onMounted(() => {
    // fetchData()
    detail()
});
</script>

<template>
    <div class="wrap" v-if="movieDetail">
        <div class="backImg">
            <img :src="`https://image.tmdb.org/t/p/original/` + movieDetail.backdrop_path" alt=""
                v-if="movieDetail.backdrop_path">
            <div class="backColor" v-else>

            </div>
        </div>

        <div class="ctx">
            <div class="search">
                <!-- <h1>title</h1> -->
                <img class="logo" src="https://raw.githubusercontent.com/zisiszikos/the-movie-db-example/master/tmdb.png"
                    alt="">
                <input type="text" placeholder="Search Movie Title.." v-model="search" @keyup.enter="fetchData"
                    list="movies">
                <transition name="fade">
                    <div class="more" v-if="dataList">
                        <p v-for="data in dataList" @click="getId(data)">{{ data.original_title }}</p>
                    </div>
                </transition>
            </div>
            <div class="content">
                <div class="pic">
                    <img :src="`https://image.tmdb.org/t/p/w400/` + movieDetail.poster_path" alt="">
                </div>
                <div class="txt">
                    <div class="title">
                        <h1>{{ movieDetail.original_title }}</h1>
                        <h2 v-if="movieDetail.tagline">{{ movieDetail.tagline }}</h2>
                        <h2 v-else></h2>
                        <p>{{ movieDetail.overview }}</p>
                    </div>
                    <div class="info">
                        <p v-for="genre in movieDetail.genres">{{ genre.name }}</p>
                    </div>
                    <div class="detail">
                        <div class="release">
                            <span>Original Release:</span>
                            <p>{{ movieDetail.release_date }}</p>
                        </div>
                        <div class="running">
                            <span>Running Time:</span>
                            <p>{{ movieDetail.runtime }} mins</p>
                        </div>
                        <div class="office">
                            <span>Box Office:</span>
                            <p v-if="movieDetail.revenue">{{ getOffice }}</p>
                            <p v-else>-</p>
                        </div>
                        <div class="average">
                            <span>Vote Average:</span>
                            <p>{{ movieDetail.vote_average }} / 10</p>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <div v-else>
        Loading data...
    </div>
</template>


<style lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Open+Sans&display=swap');

* {
    margin: 0;
    padding: 0;
}

.fade-enter-active,
.fade-leave-active {
    transition: opacity 1s ease, bottom 1s ease;
}

.fade-enter-from,
.fade-leave-to {
    bottom: -150%;
    opacity: 0;
}

.wrap {
    background-image: linear-gradient(rgba(0, 0, 0, .85) 15%, rgba(0, 0, 0, .2) 40%, #000 90%);
    height: 100vh;
    background-attachment: fixed;
    font-family: 'Open Sans', sans-serif;

    .backImg {
        width: 100%;
        height: 100%;
        position: absolute;
        z-index: -99;
        top: 0;
        left: 0;

        img {
            width: 100%;
            height: 100%;
            vertical-align: bottom;
        }

        .backColor {
            width: 100%;
            height: 100%;
            background-color: #000;
        }
    }

    .ctx {
        width: 1000px;
        margin: auto;

        .search {
            padding: 50px 0;
            display: flex;
            position: relative;

            // border: 1px solid red;
            .logo {
                width: 150px;
                vertical-align: bottom;
            }

            input {
                margin-left: auto;
                background-color: transparent;
                border: none;
                border-bottom: 3px solid #fff;
                width: 45%;
                padding: 5px 10px;
                outline: none;
                color: #fff;
                font-size: 20px;
            }

            .more {
                color: #fff;
                position: absolute;
                bottom: -115%;
                right: 0;
                padding: 5px 10px;
                width: 45%;
                background-color: rgb(0, 0, 0, 0.6);
                border: 1px solid transparent;

                :hover {
                    background-color: #f60;
                }

                p {
                    padding: 10px 0;
                    font-size: 14px;
                    cursor: pointer;
                    border: 1px solid transparent;
                }
            }



        }

        .content {
            background-color: #000;
            color: #fff;
            display: flex;

            .pic {
                img {
                    vertical-align: bottom;
                }
            }

            .txt {
                width: 100%;

                .title {
                    // border: 1px solid red;
                    padding: 20px;

                    h1 {
                        font-size: 40px;
                        margin-bottom: 15px;
                    }

                    h2 {
                        color: rgba(97, 214, 82, 0.959);
                        margin-bottom: 15px;
                        font-size: 1.2rem;
                    }

                    p {
                        line-height: 1.5rem;
                    }
                }

                .info {
                    // border: 1px solid red;
                    display: flex;
                    padding: 20px;
                    color: rgba(97, 214, 82, 0.959);
                    font-size: 1.2rem;

                    p {
                        margin-right: 20px;
                    }
                }

                .detail {
                    // border: 1px solid red;
                    padding: 20px;
                    display: grid;
                    grid-template-columns: repeat(2, 1fr);
                    grid-template-rows: repeat(2, 1fr);
                    gap: 10px;

                    p {
                        font-size: 1.5rem;
                        color: rgba(97, 214, 82, 0.959);

                    }
                }
            }
        }
    }
}
</style>
