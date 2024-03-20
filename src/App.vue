<script setup>
import { ref } from "vue";
import CocoSsd from "./components/CocoSsd.vue";
import Roboflow from "./components/Roboflow.vue";

const imageMode = ref(true);

function openCamera() {
    imageMode.value = false;
}
</script>

<template>
    <main class="content">
        <div class="switch-button">
            <button
                class="btn"
                :class="{ 'active-btn': imageMode }"
                @click="imageMode = true"
            >
                Tensorflow
            </button>
            <button
                class="btn"
                style="margin-left: 5px"
                :class="{ 'active-btn': !imageMode }"
                @click="openCamera"
            >
                Roboflow
            </button>
        </div>
        <div v-if="imageMode" class="container">
            <CocoSsd />
        </div>
        <div v-else class="container">
            <Roboflow />
        </div>
        <!-- <div v-else class="container">
            <h2 class="text-2xl text-gray-800">
                Detecting objects using a webcam with TensorFlow
            </h2>
            <div>
                <Camera
                    ref="cameraRef"
                    :resolution="{ width: 615, height: 350 }"
                    autoplay
                    v-if="!capturedVideo"
                ></Camera>
                <div class="image-container" v-show="capturedVideo">
                    <img ref="caputredImg" class="image" alt="Captured Image" />
                </div>
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

            <ul class="result" v-if="result.length > 0">
                <li v-for="res in result" class="card">
                    <div class="card-content">
                        <h1>{{ res.class }}</h1>
                        <h3>{{ roundedNumber(res.score) }}</h3>
                    </div>
                </li>
            </ul>
            <ul class="result" v-if="noResult">
                <li class="card">
                    <div class="card-content">
                        <h1>OBJECT BELUM DIKENALI</h1>
                    </div>
                </li>
            </ul>
        </div> -->
    </main>
</template>

<style>
.content {
    height: 100vh;
    padding-top: 50px;
}
.switch-button {
    margin-bottom: 10px;
}
.container {
    padding: 1rem;
}
.btn {
    font-size: 18px;
    padding: 1rem;
    border-radius: 15px;
    margin-left: 10px;
    border: 2px solid antiquewhite;
    color: antiquewhite;
    position: relative;
}
.active-btn {
    background-color: antiquewhite;
    color: black;
}
.btn:hover {
    background-color: antiquewhite;
    color: black;
}

.btn-loading {
    display: flex;
    align-items: center; /* Center loader vertically */
}

.loader {
    width: 15px; /* Adjust loader width as needed */
    height: 15px; /* Adjust loader height as needed */
    border-radius: 50%;
    border: 3px solid;
    border-color: #000 #0000;
    animation: l1 1s infinite;
    margin-right: 10px;
}

@keyframes l1 {
    to {
        transform: rotate(0.5turn);
    }
}

.btn-open-camera {
    margin-bottom: 2rem;
}
.get-result {
    margin-top: 1rem;
}

.image-container {
    position: relative;
    width: 100%; /* Adjust as needed */
    height: 350px; /* Adjust as needed */
}

.image {
    width: 100%; /* Make the image fill its container horizontally */
    height: 100%; /* Make the image fill its container vertically */
    object-fit: cover; /* Maintain aspect ratio and fill container */
}

.skeleton {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: #f0f0f0; /* Adjust as needed */
    animation: pulse 1.5s ease-in-out infinite alternate; /* Animation */
}

@keyframes pulse {
    from {
        opacity: 0.3;
    }
    to {
        opacity: 1;
    }
}
ul.result {
    margin: 0;
    margin-top: 1rem;
    padding: 0;
}

/* Style for the list items */
ul.result li {
    list-style-type: none; /* Remove default list bullets */
    margin-bottom: 20px; /* Add space between list items */
}

/* Style for the card */
.card {
    background-color: #ffffff;
    border-radius: 8px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1); /* Example shadow */
    padding: 20px; /* Example padding */
}

/* Style for the card content */
.card-content {
    padding: 0; /* Remove default padding */
}

/* Style for the class header */
.card-content h1 {
    margin: 0; /* Remove default margin */
    font-size: 24px; /* Example font size */
    color: #333; /* Example color */
}

/* Style for the score header */
.card-content h3 {
    margin: 0; /* Remove default margin */
    font-size: 18px; /* Example font size */
    color: #666; /* Example color */
}
</style>
