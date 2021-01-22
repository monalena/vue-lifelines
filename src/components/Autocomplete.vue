<template>
    <div class="autocomplete">
        <input type="text" v-model="search" @input="onChange"/>
        <ul class="autocomplete-results">
            <li class="autocomplete-result">
            </li>
        </ul>
    </div>
</template>

<script>
    export default {
        name: "Autocomplete",
        data() {
            return {
                search: '',
            }
        },
        methods: {
            onChange() {

            }
        }
    }
</script>

<style scoped>
    .autocomplete {
        position: relative;
        width: 130px;
    }

    .autocomplete-results {
        padding: 0;
        margin: 0;
        border: 1px solid #eeeeee;
        height: 120px;
        overflow: auto;
    }

    .autocomplete-result {
        list-style: none;
        text-align: left;
        padding: 4px 2px;
        cursor: pointer;
    }

    .autocomplete-result:hover {
        background-color: #4AAE9B;
        color: white;
    }
</style>
