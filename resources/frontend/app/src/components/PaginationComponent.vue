<template>
    <nav class="text-dark" v-if="pagination.last_page!==1">
        <ul class="pagination" v-if="pagination.last_page > 0" :class="sizeClass">
            <li class="page-item" v-if="showPrevious()" :class="{ 'disabled' : pagination.current_page <= 1 }">
                <a href="#" class="page-link text-dark" v-if="pagination.current_page > 1 " :aria-label="config.ariaPrevioius" @click.prevent="changePage(pagination.current_page - 1)">
                    <span aria-hidden="true">{{ config.previousText }}</span>
                </a>
            </li>

            <li class="page-item" v-for="(num, key) in array" v-bind:key="key" >
                <a href="#" :class="{ 'bg-info' : num === pagination.current_page }" class="page-link text-dark" @click.prevent="changePage(num)">{{ num }}</a>
            </li>

            <li class="page-item" v-if="showNext()" :class="{ 'disabled' : pagination.current_page === pagination.last_page || pagination.last_page === 0 }">
                <a href="#" class="page-link text-dark" v-if="pagination.current_page < pagination.last_page" :aria-label="config.ariaNext" @click.prevent="changePage(pagination.current_page + 1)">
                    <span aria-hidden="true">{{ config.nextText }}</span>
                </a>
            </li>
        </ul>
    </nav>

</template>

<script>
export default {
    props: {
        pagination: {
            type: Object,
            required: true,
        },
        callback: {
            type: Function,
            required: true,
        },
        options: {
            type: Object,
        },
        size:{
            type: String,
        }
    },
    computed: {
        array() {
            if (this.pagination.last_page <= 0) {
                return [];
            }
            let from = this.pagination.current_page - this.config.offset;
            if (from < 1) {
                from = 1;
            }
            let to = from + (this.config.offset * 2);
            if (to >= this.pagination.last_page) {
                to = this.pagination.last_page;
            }
            const arr = [];
            while (from <= to) {
                arr.push(from);
                from += 1;
            }
            return arr;
        },
        config() {
            return Object.assign({
                offset: 3,
                ariaPrevious: 'Previous',
                ariaNext: 'Next',
                previousText: '«',
                nextText: '»',
                alwaysShowPrevNext: false,
            }, this.options);
        },
        sizeClass() {
            if (this.size === 'large') {
                return 'pagination-lg';
            } else if (this.size === 'small') {
                return 'pagination-sm';
            }
            return '';
        },
    },
    watch: {
        'pagination.per_page'(newVal, oldVal) { // eslint-disable-line
            if (+newVal !== +oldVal) {
                this.callback();
            }
        },
    },
    methods: {
        showPrevious() {
            return this.config.alwaysShowPrevNext || this.pagination.current_page > 1;
        },
        showNext() {
            return this.config.alwaysShowPrevNext ||
                this.pagination.current_page < this.pagination.last_page;
        },
        changePage(page) {
            if (this.pagination.current_page === page) {
                return;
            }
            this.callback(page);
        },
    },
};
</script>

<style>

</style>
