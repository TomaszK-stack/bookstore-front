<template>
    <div class="page-search">
        <div class="columns is-multiline">
            <div class="column is-12">
                <h1 class="title">Search</h1>

                <h2 class="is-size-5 has-text-grey">Search term: "{{ query }}"</h2>
            </div>

            <ProductBox 
                v-for="product in products"
                v-bind:key="product.id"
                v-bind:product="product"
             />
        </div>
    </div>
</template>

<script>
import axios from 'axios'
import ProductBox from '@/components/ProductBox.vue'

export default {
    name: 'Search',
    components: {
        ProductBox
    },
    data() {
        return {
            proddict: {},
            products: [],
            query: ''
        }
    },
    mounted() {
        document.title = 'Search | Djackets'

        let uri = window.location.search.substring(1)
        let params = new URLSearchParams(uri)

        if (params.get('query')) {
            this.query = params.get('query')

            this.performSearch()
        }
    },
    methods: {
        async performSearch() {
            const myHandler = {
                get: function(target, prop) {
                    return target[prop];
                },
                set: function(target, prop, value) {
                    console.log(`Ustawiam wartość ${prop} na ${value}`);
                    target[prop] = value;
                    return true;
                }
                };
                        

            this.$store.commit('setIsLoading', true)

            await axios
                .get('/api/v1/books/list')
                .then(response => {
                    

                            this.proddict = response.data
                            this.products = Object.values(this.proddict)
                            

                })
                .catch(error => {
                    console.log(error)
                })

            this.$store.commit('setIsLoading', false)
        }
    }
}
</script>