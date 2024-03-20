<script setup>
import { roundedNumber } from "../utils";
defineProps({
    results: {
        type: Array,
    },
});
</script>
<template>
    <ul class="result" v-if="results.length > 0">
        <li v-for="res in results" class="card">
            <div class="card-content">
                <h1>{{ res.class }}</h1>
                <h3>{{ roundedNumber(res.score) }}</h3>
            </div>
        </li>
    </ul>
</template>
