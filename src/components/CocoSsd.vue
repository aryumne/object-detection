<script setup>
import { ref } from "vue";
import "@tensorflow/tfjs-backend-cpu";
import "@tensorflow/tfjs-backend-webgl";
import * as cocoSsd from "@tensorflow-models/coco-ssd";
import Result from "./Result.vue";

const loading = ref(false);
const imgRef = ref("");
const imgUrl = ref("https://source.unsplash.com/random/?transportation");
const imgLoaded = ref(false);
const caputredImg = ref("");
const result = ref([]);

function imageLoaded() {
    imgLoaded.value = true;
}

function reloadImage() {
    imgLoaded.value = false;
    imgUrl.value = "https://source.unsplash.com/random/" + new Date().getTime();
}

async function detect(isImage = true) {
    try {
        loading.value = true;
        const img = isImage ? imgRef.value : caputredImg.value;
        const model = await cocoSsd.load();
        const predictions = await model.detect(img);
        result.value = predictions;
        if (result.value.length === 0) result.value = [];
    } catch (error) {
        console.error(error.message);
    } finally {
        loading.value = false;
    }
}
</script>

<template>
    <h2 class="text-2xl text-gray-800">
        Detecting objects in random images using TensorFlow with Coco SSD model.
    </h2>
    <div class="image-container">
        <div class="skeleton" v-if="!imgLoaded"></div>
        <img
            ref="imgRef"
            class="image"
            :src="imgUrl"
            alt="scissors"
            crossorigin="anonymous"
            :style="{ display: imgLoaded ? 'block' : 'none' }"
            @load="imageLoaded"
        />
    </div>
    <button
        class="btn get-result"
        :class="{ 'active-btn': loading }"
        @click="detect"
    >
        <div class="btn-loading" v-if="loading">
            <div class="loader"></div>
            <span class="btn-text">Loading</span>
        </div>
        <span v-else>Detect</span>
    </button>
    <button
        class="btn get-result"
        :class="{ 'active-btn': loading }"
        @click="reloadImage"
    >
        Change Image
    </button>

    <Result :results="result" />
</template>
