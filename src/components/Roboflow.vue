<script setup>
import { ref } from "vue";
import Camera from "simple-vue-camera";
import axios from "axios";
import Result from "./Result.vue";
import UploadImage from "./UploadImage.vue";

const loading = ref(false);
const cameraRef = ref(null);
const capturedVideo = ref(false);
const caputredImg = ref("");
const result = ref([]);
const selectedFile = ref(null);
const previewImage = ref(null);

async function snapshot() {
    try {
        const blob = await cameraRef.value?.snapshot();
        const url = URL.createObjectURL(blob);
        caputredImg.value.src = url;
        const base64data = await blobToBase64(blob);
        await detectWithRoboFlow(base64data);
        capturedVideo.value = true;
    } catch (error) {
        console.error(error.message);
    } finally {
        loading.value = false;
    }
}

async function handleFileInputChange(event) {
    const file = event.target.files[0]; // Get the first selected file
    if (file) {
        const imagebase64 = await blobToBase64(file);
        await detectWithRoboFlow(imagebase64);
    }
}

// Fungsi untuk mengonversi blob menjadi base64
async function blobToBase64(blob) {
    return new Promise((resolve, reject) => {
        const reader = new FileReader();
        reader.onload = (e) => {
            previewImage.value = e.target.result;
        };
        reader.readAsDataURL(blob);
        reader.onloadend = () => {
            const base64data = reader.result.split(",")[1];
            resolve(base64data);
        };
        reader.onerror = () => {
            reject(new Error("Failed to convert blob to base64"));
        };
    });
}

function reset() {
    capturedVideo.value = false;
    caputredImg.value = null;
    result.value = [];
    loading.value = false;
    selectedFile.value = null;
    previewImage.value = null;
}

async function detectWithRoboFlow(image) {
    try {
        loading.value = true;
        const res = await axios({
            method: "POST",
            url: "https://detect.roboflow.com/deteksi-spidol/4",
            params: {
                api_key: "DQRDZ5w5OBBPDhZ20PaJ",
            },
            data: image,
            headers: {
                "Content-Type": "application/x-www-form-urlencoded",
            },
        });
        console.log(res.data);
        if (res.data.predictions.length > 0) {
            res.data.predictions.forEach((predicted) => {
                result.value.push({
                    class: predicted.class,
                    score: predicted.confidence,
                });
            });
        } else {
            result.value = [];
        }
    } catch (error) {
        console.error(error.message);
    } finally {
        loading.value = false;
    }
}
</script>

<template>
    <h2 class="text-2xl text-gray-800">
        Detecting objects using RoboFlow with Yolov8 model
    </h2>
    <div>
        <Camera
            ref="cameraRef"
            :resolution="{ width: 615, height: 350 }"
            autoplay
            v-if="!capturedVideo && previewImage === null"
        ></Camera>
        <img
            v-else
            :src="previewImage"
            class="image"
            alt="selected image"
            crossorigin="anonymous"
        />
        <div class="image-container" v-show="capturedVideo">
            <img ref="caputredImg" class="image" alt="Captured Image" />
        </div>
        <input
            type="file"
            id="file-input"
            class="w-full h-11 border rounded-md text-gray-700"
            @change="handleFileInputChange"
        />
    </div>
    <button
        class="btn get-result"
        :class="{ 'active-btn': loading }"
        @click="snapshot"
    >
        <div class="btn-loading" v-if="loading">
            <div class="loader"></div>
            <span class="btn-text">Loading</span>
        </div>
        <span v-else>Snapshot & detect</span>
    </button>
    <button
        class="btn get-result"
        :class="{ 'active-btn': loading }"
        @click="reset"
    >
        Reset
    </button>
    <label for="file-input" class="btn get-result">Choose Image</label>

    <!-- <UploadImage /> -->

    <Result :results="result" />
</template>
