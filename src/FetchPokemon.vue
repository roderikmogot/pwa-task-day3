<script setup>
import { onMounted, ref } from "vue";

const title = ref('Fetching data example')
const items = ref("")

const fetchData = async () => {
    try {
        const response = await fetch("https://pokeapi.co/api/v2/pokemon")
        const data = await response.json();
        items.value = data;
    } catch (e) {
        console.error("Failed to fetch data")
    }
}

onMounted(fetchData);
</script>

<template>
    <div>
        {{ title }}
    </div>
    <ul>
        <li v-for="(item, index) in items.results">
            <span :key="index">
                {{ item.name }}
            </span>
        </li>
    </ul>
    <hr />
</template>
