<template>
    <div class="container">
        <h1>{{ item.name || item.title }}</h1>
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
        }
    },
    mounted() {
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
    },
}
</script>
