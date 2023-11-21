<script setup>
import { ref,onMounted } from "vue"

const canvas = ref(null);
const video=ref(null);
const ctx = ref(null);
const constraints = ref({
    audio:false,
    video:true
})

onMounted(async() => {
    if(video.value && canvas.value) {
        ctx.value = canvas.value.getContext("2d");

        await navigator.mediaDevices
            .getUserMedia(constraints.value)
            .then(SetStream)
            .catch(e=>{
                console.error(e);
            })
    }
})

function SetStream(stream) {
    video.value.srcObject = stream;
    video.value.play();
    requestAnimationFrame(Draw);
}

function Draw() {
    ctx.value.drawImage(video.value,0,0, canvas.value.width, canvas.value.height);
    requestAnimationFrame(Draw);
}

function TakePicture() {
    var link = document.createElement("a");
    link.download = `vue-cam-${new Date().toISOString()}.png`;
    link.href = canvas.value.toDataURL();
    link.click();
}

</script>
<template>
    <div>
        <video ref="video" autoplay playsinline webkit-playsinline muted hidden></video>
        <canvas ref="canvas" width="1200" height="700" class="bg-black rounded-3xl" ></canvas>
        <div class="flex items-center justify-center py-4">
            <button @click="TakePicture" class="px-6 py-4 bg-green-500 rounded text-white text-2xl uppercase font-bold hover:bg-green-600">
                Take Picture
            </button>
        </div>
    </div>
</template>

