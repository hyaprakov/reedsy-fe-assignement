<template>
    <div class="book" v-if="book">
        <nuxt-link
            to="/">

            Go back
        </nuxt-link>

        <div class="is-flex align-center justify-space-between">
            <div>
                <h1>
                    {{ book.title }}
                </h1>
                <div class="author">
                    {{ book.author }}
                </div>
            </div>

            <upvote
                :upvoted="book.upvoted"
                :upvotes="book.upvotes"
                :reversed="true"
            ></upvote>
        </div>

        <div class="book-img">
            <img 
                :src="book.cover" 
                :alt="book.title">
        </div>

        <h2>
            Synopsis
        </h2>
        <div class="synopsis">
            <p
                v-html="book.synopsis">
            </p>
        </div>

        <p class="rating">
            Rating: {{ book.rating }}/10
        </p>

        <h2>
            Comments
        </h2>

        <div class="comments">
            <div 
                class="comment"
                :key="'commment-' + i"
                v-for="(comment, i) in comments">

                <h3>
                    {{ comment.name }}
                </h3>
                <p>
                    {{ comment.msg }}
                </p>
            </div>
        </div>

        <form 
            action="#"
            method="post"
            @submit="postComment">

            <input 
                :class="[
                    {'has-error': errors.name && !name}
                ]"
                type="text"
                name="name"
                placeholder="Your name *"
                v-model="name">
            <p class="error" v-if="errors.name && !name">
                This field is required
            </p>

            <textarea 
                :class="[
                    {'has-error': errors.msg && !msg}
                ]"
                name="message" 
                placeholder="What do you think about this book?"
                v-model="msg"
            ></textarea>
            <p class="error" v-if="errors.msg && !msg">
                This field is required
            </p>

            <input
                class="button" 
                type="submit"
                value="Post your comment">
        </form>
    </div>
</template>

<style lang="sass" scoped>
@import '~/assets/settings.sass'

h1
    margin-bottom: 0
    font-weight: bold
    color: $primary-dark

h2
    font-size: $size-4

.author
    font-size: $size-6
    font-style: italic

.book
    padding: 3rem

.book-img
    height: 50rem
    display: flex
    justify-content: center
    margin: 3rem 0

    img
        width: auto
        height: 100%

.rating
    margin: 2rem 0 3rem 0
    font-weight: bold

.comments
    margin: .5rem 0

    .comment:last-child
        border-bottom: none

.comment
    padding: 1.5rem 0
    border-bottom: .1rem solid $grey

    h3,
    p
        margin-bottom: 0

form
    input,
    textarea
        width: 30rem
        display: block
        margin: 1rem 0
        padding: 1rem 1.5rem
        font-size: $size-6
        border-radius: $radius
        border: .1rem solid $grey-light
        transition: all .3s

        &:hover
            border-color: $grey-dark
        &:focus
            outline: none
            border-color: $primary
        
        &.has-error
            border-color: $danger
    textarea
        height: 10rem
    .error
        margin: -.5rem 0 1.5rem 0
        font-size: $size-7
        color: $danger


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
            book: null,

            comments: [
                {
                    name: 'Borat Sagdiev',
                    msg: 'Great Success! Lorem ipsum dolor sit amet consectetur adipisicing elit.'
                }
            ],

            name: '',
            msg: '',

            errors: {
                name: false,
                msg: false,
            },
        }
    },

    methods: {
        getBook: function (slug) {
            axios
                .get(`http://localhost:3001/books/${slug}`)
                .then(response => {
                    if (response.status == 200) {
                        this.book = response.data
                    } else {
                        console.log(response)
                    }
                })
        },

        postComment: function (e) {
            e.preventDefault()
            this.errors.name = false
            this.errors.msg = false

            if (!this.name) {
                this.errors.name = true
                return
            }
            if (!this.msg) {
                this.errors.msg = true
                return
            }
            
            this.comments.push({
                name: this.name,
                msg: this.msg,
            })

            this.name = ''
            this.msg = ''
        }
    },
    mounted: function () {
        this.getBook(this.$route.params.slug)
    }
}
</script>