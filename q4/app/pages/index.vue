<template>
    <div>
        <h1>
            Top books of all time
        </h1>

        <div class="books-search">
            <input 
                type="text" 
                placeholder="Search books by title and synopsis..."
                v-model="search">
            <p v-if="search">
                {{ total_count }} books out of {{ total_books_count }} match your search query.
            </p>
        </div>

        <div class="books-list" v-if="filtered_books.length > 0">
            <div 
                class="book"
                :key="'book-' + i"
                
                v-for="(book, i) in filtered_books">

                <div class="book-info">
                    <h2>
                        <nuxt-link
                            :to="`/books/${book.slug}`">

                            {{ page * per_page + i + 1 }}. {{ book.title }}
                        </nuxt-link>
                        <span class="rating">
                            ({{ book.rating }}/10)
                        </span>
                    </h2>

                    <div class="author">
                        {{ book.author }}
                    </div>

                    <div class="synopsis">
                        <p
                            v-html="truncate(book.synopsis)">
                        </p>
                    </div>

                    <upvote
                        :upvoted="book.upvoted"
                        :upvotes="book.upvotes"
                    ></upvote>
                </div>
                
                <nuxt-link
                    class="book-img"
                    :to="`/books/${book.slug}`">

                    <img 
                        :src="book.cover" 
                        :alt="book.title">
                </nuxt-link>
            </div>
        </div>
        <div class="no-books" v-else>
            <p>
                There is nothing to show.
            </p>
        </div>

        <div class="pagination">
            <a 
                href="javascript:;" 
                :class="[
                    'button',
                    {'is-disabled': is_first_page}
                ]"
                @click="prevPage()">

                Prev
            </a>

            <a 
                href="javascript:;" 
                :class="[
                    'button',
                    {'is-disabled': page == i - 1}
                ]"
                @click="setPage(i - 1)"
                :key="'page-' + i"
                v-for="i in (last_page + 1)">

                {{ i }}
            </a>

            <a 
                href="javascript:;" 
                :class="[
                    'button',
                    {'is-disabled': is_last_page}
                ]"
                @click="nextPage()">
                
                Next
            </a>
        </div>
    </div>
</template>

<style lang="sass" scoped>
@import '~/assets/settings.sass'

h1
    margin: 0
    padding: 2rem 0
    text-align: center
    font-weight: bold
    color: $primary
    background: $primary-light

.books-search
    padding: 0 3rem

    input
        width: 100%
        display: block
        padding: 2rem 0
        font-size: $size-4
        border: none
        border-bottom: .2rem solid $grey-light
        transition: all .3s

        &:hover
            border-bottom-color: $grey
        &:focus
            outline: none
            border-bottom-color: $primary
    
.books-list
    .book:nth-child(even)
        background: $primary-light

.no-books
    padding: 5rem 0
    text-align: center
    font-size: $size-5

.book
    display: flex
    padding: 3rem

    .book-info
        flex: 1
        margin-right: 1rem

        h2
            a
                color: $primary-dark
                border-bottom: .3rem dashed transparent

                &:hover
                    border-bottom-color: $primary-dark
            span
                display: inline-block
                margin-top: -.5rem
                font-size: $size-6
        .author
            margin: 1rem 0 2rem 0
            font-style: italic
        .synopsis
            margin-bottom: 2rem

    .book-img
        width: 20%
        
        &:hover
            img
                transform: scale(1.05)

        img
            transition: all .3s

.pagination
    display: flex
    align-items: center
    justify-content: center
    padding: 2rem

    .button
        margin: 0 .5rem

</style>

<script>
import axios from 'axios'

import Upvote from "~/components/Upvote.vue"

export default {
    components: {
        Upvote
    },

    data: function () {
        return {
            books: [],
            
            search: '',

            page: 0,
            per_page: 4,
            total_books_count: 0,
            total_count: 0
        }
    },

    computed: {
        filtered_books: function () {
            let books = this.books
                .reduce((acc, book) => {
                    if (!this.search) {
                        acc.push(book)    
                    } else if (book.title.toLowerCase().includes(this.search.toLowerCase()) || book.synopsis.toLowerCase().includes(this.search.toLowerCase())) {
                        acc.push(book)
                    }
                    
                    return acc
                }, [])

            this.total_count = books.length

            if (this.page > this.last_page) {
                this.page = 0
            }

            return books.slice(this.page * this.per_page, (this.page + 1) * this.per_page)
        },

        is_first_page: function () {
            return this.page == 0
        },
        last_page: function () {
            return Math.floor(this.total_count / this.per_page)
        },
        is_last_page: function () {
            return this.page == this.last_page
        }
    },
    methods: {
        getBooks: function () {
            axios
                .get('http://localhost:3001/books')
                .then(response => {
                    if (response.status == 200) {
                        this.books = response.data.books ? response.data.books : []
                        this.total_books_count = response.data.meta.count
                        this.total_count = response.data.meta.count
                    } else {
                        console.log(response)
                    }
                })
        },

        nextPage: function () {
            this.page = !this.is_last_page ? this.page + 1 : this.page
        },
        prevPage: function () {
            this.page = !this.is_first_page ? this.page - 1 : this.page
        },
        setPage: function (ind) {
            this.page = ind
        },

        truncate: function (text) {
            return text.substring(0, 200).trim() + '...'
        }
    },
    mounted: function () {
        this.getBooks()
    }
}
</script>
