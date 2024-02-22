<script setup>

import { reactive } from 'vue';

const emit = defineEmits(['place-data'])
const searchTerm = reactive({
    query: '',
    timeOut: null,
    result: null
})
const handleSearch = () => {
    clearTimeout(searchTerm.timeOut)
    searchTerm.timeOut = setTimeout(async () => {
        if (searchTerm.query !== '') {
            const res = await fetch(`http://api.weatherapi.com/v1/search.json?key=99c8ddfbbcd14ae984093940242102&q=${searchTerm.query}`)
            const data = await res.json()
            searchTerm.result = data

        }
        else {
            searchTerm.result = null
        }
    }, 500)

}
const getWheather = async (id) => {
    const res = await fetch(`http://api.weatherapi.com/v1/forecast.json?key=99c8ddfbbcd14ae984093940242102&q=id:${id}&days=3&aqi=no&alerts=no`)
    const data = await res.json()
    emit('place-data', data)
    searchTerm.query = ''
    searchTerm.result = null

}

</script>
<template>
    <div>
        <!-- search field -->
        <form>
            <div class="bg-white border border-indigo-600/30 rounded-lg shadow-lg flex items-center">
                <i class="fa-solid fa-magnifying-glass p-2 text-indigo-600"></i>
                <input type="text" placeholder="Search for a place"
                    class="rounded-r-lg p-2 border-0 outline-0 focus:ring-2 focus:ring-indigo-600 ring-inset w-full"
                    v-model="searchTerm.query" @input="handleSearch" />
            </div>
        </form>
        <!-- search suggestions -->
        <div class="bg-white my-2 rounded-lg shadow-lg" v-if="searchTerm.result !== null">
            <div v-for="place in searchTerm.result" :key="place.id">
                <button @click="getWheather(place.id)"
                    class="px-3 my-2 hover:text-indigo-600 hover:font-bold w-full text-left">
                    {{ place.name }}, {{ place.region }}, {{ place.country }}
                </button>
            </div>
        </div>
    </div>
</template>