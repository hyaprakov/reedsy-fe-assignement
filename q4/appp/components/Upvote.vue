<template>
    <div 
        :class="[
            'button-group',
            {'is-reversed': reversed}
        ]">

        <a 
            href="javascript:;"
            :class="[
                'button',
                {'is-outlined': is_upvoted}
            ]"
            @click="upvote()">

            {{ is_upvoted ? 'Upvoted' : 'Upvote' }}
        </a>

        <p>
            {{ upvotes_formatted }}
        </p>
    </div>
</template>

<style lang="sass" scoped>
@import '~/assets/settings.sass'

.button-group    
    display: flex
    align-items: center

    &.is-reversed
        flex-direction: row-reverse
    
    p
        margin: 0 2rem

</style>

<script>
export default {
    props: [
        'upvoted',
        'upvotes',
        'reversed',
    ],
    data: function () {
        return {
            is_upvoted: false,
        }
    },

    methods: {
        upvote: function () {
            this.is_upvoted = !this.is_upvoted

            // An axios post request should go here...
        }
    },
    computed: {
        upvotes_formatted: function () {
            return `Upvoted ${this.upvotes + (this.is_upvoted != this.upvoted ? (this.is_upvoted ? 1 : -1) : 0)} times`
        }
    },
    created: function () {
        this.is_upvoted = this.upvoted
    }
}
</script>

