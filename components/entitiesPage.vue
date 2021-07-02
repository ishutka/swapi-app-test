<template>
    <div class="d-flex flex-column entities container">
        <h1 class="mb-3">{{ entities.toUpperCase() }}</h1>
        <div class="input-group my-3 entities__search">
            <input
                v-model="input"
                type="text"
                class="form-control"
                :placeholder="`Search for ${subPage}`"
                :aria-label="`Search for ${subPage}`"
                @keydown.enter="search"
                @input="closeSearchPanel"
            />
            <div
                v-if="searchingIsLoaded"
                class="dropdown-menu show entities__search-variants"
            >
                <b-button-close
                    class="ml-auto mr-2"
                    @click="closeSearchPanel"
                ></b-button-close>
                <div
                    v-if="searchingIsLoaded && !searchVariants.length"
                    class="dropdown-item alert alert-warning"
                >
                    Any {{ entities }} were not found
                </div>
                <nuxt-link
                    v-for="variant in searchVariants"
                    :key="variant.name || variant.title"
                    :to="`/${entities}/${subPage}/?name=${
                        variant.name || variant.title
                    }`"
                    class="dropdown-item"
                >
                    {{ variant.name || variant.title }}
                </nuxt-link>
            </div>
            <div class="input-group-append">
                <button
                    class="btn btn-outline-secondary"
                    type="button"
                    @click="search"
                >
                    Search
                </button>
            </div>
        </div>
        <b-list-group>
            <b-list-group-item
                v-for="item in pageEntities"
                :key="item.name || item.title"
                class="my-2 font-weight-bolder"
            >
                {{ item.name || item.title }}
            </b-list-group-item>
            <div v-if="entitiesAreLoading" class="spinner-border mx-auto mb-3" role="status">
                <span class="sr-only">Loading...</span>
            </div>
            <b-button v-if="nextUrl" class="mx-auto" @click="fetchData">
                Load More
            </b-button>
        </b-list-group>
    </div>
</template>
<script>
export default {
    props: {
        entities: {
            type: String,
            required: true,
        },
    },
    data() {
        return {
            input: '',
            pageEntities: [],
            searchVariants: [],
            nextUrl: `https://swapi.dev/api/${this.entities}/`,
            searchingIsLoaded: false,
            entitiesAreLoading: false,
        }
    },
    computed: {
        subPage() {
            if (this.entities === 'people') return 'person'
            if (this.entities === 'films') return 'film'
            return 'starship'
        },
    },
    mounted() {
        if (this.nextUrl === `https://swapi.dev/api/${this.entities}/`)
            this.fetchData()
    },
    methods: {
        fetchData() {
            this.entitiesAreLoading = true
            this.$axios
                .$get(this.nextUrl)
                .then((result) => {
                    this.pageEntities = [
                        ...this.pageEntities,
                        ...result.results,
                    ]
                    this.nextUrl = result.next
                })
                .catch((err) => {
                    // eslint-disable-next-line no-console
                    console.log(err)
                })
                .finally(() => {
                    this.entitiesAreLoading = false
                })
        },
        search() {
            if (this.input.length) {
                this.$axios
                    .$get(
                        `https://swapi.dev/api/${this.entities}/?search=${this.input}`
                    )
                    .then(({ results }) => {
                        this.searchingIsLoaded = true
                        if (results.length) this.searchVariants = results
                    })
                    .catch((err) => {
                        // eslint-disable-next-line no-console
                        console.log(err)
                    })
            }
        },
        closeSearchPanel() {
            this.searchingIsLoaded = false
        },
    },
}
</script>
<style lang="scss">
.list-group-item + .list-group-item {
    border-top-width: 1px;
}
.entities {
    &__search {
        width: auto;
    }

    &__search-variants {
        width: 100%;
    }
}
</style>
