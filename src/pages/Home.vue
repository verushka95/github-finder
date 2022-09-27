<template>
    <div class="wrapper-content wrapper-content--fixed">
        <section>
        <div class="container">
            <!-- errors -->
            <div class="error" v-if="error">
                <p>{{ error }}</p>
            </div>

            <!-- search -->
            <search 
                :value="search"
                placeholder="Type username"
                @search="search = $event"/>

                <!-- buttons -->
                <button v-if="!repos" class="btn btnPrimary" @click="getInfo">Search!</button>
                <button v-else class="btn btnPrimary" @click="getInfo">Search again!</button>

                <!-- user__wrapper -->
                <div class="user__wrapper" v-if="user">
                    <div class="user-item">
                        <div class="user-item__photo">
                            <img :src="user.avatar_url" :alt="user.login">
                        </div>
                        <div class="user-item__info">
                            <p>
                                <span class="user-item__title">Name:</span> {{ user.name }}
                            </p>
                            <p>
                                <span class="user-item__title">Number of repositories:</span> {{ countRepos }}
                            </p>
                        </div>
                    </div>
                </div>

                <!-- repos wrapper -->
                <div class="repos__wrapper" v-if="repos">

                    <!-- sort -->
                    <div class="sort">
                        <div class="sort__item" @click="sortedRepo('name')">Name</div>
                        <div class="sort__item" @click="sortedRepo('stars')">Count of stars</div>
                    </div>

                    <!-- item -->
                    <div class="repos-item" v-for="repo in reposFilter" :key="repo.id">
                        <div class="repos-info">
                            <a class="link" target="_blank" :href="repo.html_url">{{ repo.name }}</a>
                            <span>{{ repo.stargazers_count}}⭐</span>
                        </div>
                    </div>

                    <!-- pagination -->
                    <div class="button-list">
                        <div 
                            class="btn btnPrimary" 
                            :class="{'btnDisabled': page.current == 1 }"
                            @click="prevPage">
                                &#8592;
                        </div>
                        <div 
                            class="btn btnPrimary" 
                            :class="{'btnDisabled': (countRepos <= page.current * page.count)}"
                            @click="nextPage" >
                                &#8594;
                        </div>
                    </div>
                </div>
        </div>
        </section>
  </div>
</template>

<script>
import search from '@/components/Search.vue'
import axios from 'axios'
export default {
    components: { search },
    data () {
        return {
            search: '',
            error: null,
            repos: null,
            user: null,
            sort: {
                name: 'asc',
                stars: null
            },
            page: {
                current: 1,
                count: 4
            }
        }
    },
    computed: {
        countRepos () {
            return this.repos.length
        },
        reposFilter () {
            return this.repos.filter ((value, index) => {
                let start = (this.page.current - 1) * this.page.count
                let end = this.page.current * this.page.count
                if (index >=start && index < end) return true
            })
        }
    },
    methods: {
        // сортировку сделала иначе,
        //  насколько верно так делать? или лучше всё-таки способ, который был показан в другом модуле?
        // тогда вопрос, почему метод сортировки должен быть computed?
        sortedRepo (param) {
            if (param == 'name') this.getSort('name', 'name')
            if (param == 'stars') this.getSort('stars', 'stargazers_count')
        },
        // обязательно ли подобные методы переносить в отдельный файл js ?
        getSort (key, field) {
            let mod = 1;

            // сортируем по убыванию
            if (this.sort[key] == 'asc') mod = -1

            // порядок сортировки
            this.sort[key] = (mod == -1) ? 'desc' : 'asc'

            this.repos.sort((a,b) => {
                if (a[field] > b[field]) {
                    return 1*mod
                }
                if(a[field] < b[field]) {
                    return -1*mod
                }
                return 0
            })
        },
        getInfo () {
           Promise.all(
            [ 
                axios.get(`https://api.github.com/users/${this.search}`),
                axios.get(`https://api.github.com/users/${this.search}/repos`)
            ])
           .then(([user, repos]) => {
                this.error = null
                this.user = user.data
                this.repos = repos.data
           })
            .catch( err => {
                console.log(err)
                this.repos = null
                this.user = null
                this.error = `Can't find this user`
            })
        },
        prevPage () {
            if (this.page.current > 1) this.page.current -= 1
        },
        nextPage () {
            if (this.countRepos > (this.page.current* this.page.count)) this.page.current += 1
        }
    }
}
</script>

<style lang="scss" scoped>
    .container {
        display: flex;
        align-items: center;
        flex-direction: column;
    }
    button {
        margin-top: 40px;
    }
    .repos__wrapper, .user__wrapper {
        width: 400px;
        margin: 30px 0px;
    }
    .repos-info {
        display: flex;
        justify-content: space-between;
        align-items: center;
        margin-bottom: 10px;
        padding: 10px 0px;
        border-bottom: 1px solid #dbdbdb;
    }
    .user-item {
        display: flex;
        justify-content: space-between;
        align-items: center;
        &__info p {
            margin-bottom: 10px;
        }
        &__photo {
            width: 100px;
            height: 100px;
            img {
                border-radius: 50%;
            }

        }
        &__title {
            font-weight: bold;
        }
    }
    
    .error {
        margin-bottom: 20px;
        color: red;
    }

    .sort {
        display: flex;
        justify-content: space-between;
        &__item {
            cursor: pointer;
        }
    }

    .button-list {
        width: 100%;
        text-align: center;

        .btn {
            border-radius: 60px;
            margin: 0px 20px;
        }
    }
</style>