<template>
    <div class="news-view" :class="{ loading: !items.length }">
        <item
            v-for="item in items"
            :item="item"
            :index="$index|formatItemIndex"
            track-by="id">
        </item>

        <div class="nav" v-show="items.length > 0">
            <a v-if="page > 1" :href="'#/news/'+(page-1)">&lt; prev</a>
            &nbsp;&nbsp;
            <a v-if="page < 4" :href="'#/news/'+(page+1)">more...</a>
        </div>
    </div>
</template>

<script>
    import store from '../store'
    import Item from './Item.vue'

    export default {

        name: 'NewsView',

        components: {
            Item
        },

        data(){
            return {
                page: 1,
                items: []
            }
        },

        created(){
            store.on('topstories-updated', this.update)
        },

        destroyed(){
            store.removeListener('topstories-updated', this.update)
        },

        methods: {
            update(){
                store.fetchItemsByPage(this.page).then(items => {
                    this.items = items
            })
            }
        },

        route: {
            data({to}){
                this.items = []
                const page = +to.params.page
                return store.fetchItemsByPage(page).then(items => ({page, items})
            )
            }
        },

        filters: {
            formatItemIndex(index){
                return (this.page - 1) * store.storiesPerPage + index + 1
            }
        }
    }
</script>

<style lang="stylus">
.news-view
    padding-left 5px
    padding-right 15px
    &.loading:before
        content "Loading..."
        position absolute
        top 16px
        left 20px
    .nav
        padding 10px 10px 10px 40px
        margin-top 10px
        border-top 2px solid #f60
        a
            margin-right 10px
            &:hover
                text-decoration underline
</style>