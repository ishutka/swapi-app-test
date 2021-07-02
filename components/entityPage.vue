<template>
    <div class="container">
        <h1>{{ item.name || item.title }}</h1>
        <div
            v-if="itemIsLoading"
            class="spinner-border mx-auto mb-3"
            role="status"
        >
            <span class="sr-only">Loading...</span>
        </div>
        <div v-for="(option, opKey) in item" :key="opKey" class="mb-1">
            <strong>{{ opKey }}</strong> : {{ option }}
        </div>
    </div>
</template>

<script>
export default {
    props: {
        entity: {
            type: String,
            required: true,
        },
    },
    data() {
        return {
            item: {},
            itemIsLoading: false,
        }
    },
    mounted() {
        this.itemIsLoading = true
        this.$axios
            .$get(
                `https://swapi.dev/api/${this.entity}/?search=${this.$route.query.name}`
            )
            .then(({ results }) => {
                if (results.length) this.item = results[0]
            })
            .catch((err) => {
                // eslint-disable-next-line no-console
                console.log(err)
            })
            .finally(() => {
                this.itemIsLoading = false
            })
    },
}
</script>
