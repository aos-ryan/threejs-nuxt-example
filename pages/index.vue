<template>
  <div class="page">
    <canvas ref="canvas"></canvas>
  </div>
</template>
<script setup>
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader.js";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
const canvas = ref();
let camera, scene, renderer;
let controls;

function init() {
  // Camera
  camera = new THREE.PerspectiveCamera(
    70,
    window.innerWidth / window.innerHeight,
    1,
    1000
  );
  camera.position.z = 400;

  // Scene
  scene = new THREE.Scene();

  // Lighting
  const ambientLight = new THREE.AmbientLight(0xffffff, 1);
  scene.add(ambientLight);

  // Models
  const gltfLoader = new GLTFLoader();
  gltfLoader.load("/models/Boulder/boulder.glb", (gltf) => {
    // console.log(gltf);
    scene.add(gltf.scene);
  });

  // Renderer
  renderer = new THREE.WebGLRenderer({
    antialias: true,
    canvas: canvas.value,
    alpha: true
  });
  renderer.setPixelRatio(window.devicePixelRatio);
  renderer.setSize(window.innerWidth, window.innerHeight);

  // Controls
  controls = new OrbitControls(camera, canvas.value);
  controls.enableDamping = true;

  // Resize Event Listener
  window.addEventListener("resize", onWindowResize);
}

// Function for when the window resizes
function onWindowResize() {
  camera.aspect = window.innerWidth / window.innerHeight;
  camera.updateProjectionMatrix();

  renderer.setSize(window.innerWidth, window.innerHeight);
}

// Animate function
function animate() {
  // Update controls
  controls.update();
  // Render Scene
  renderer.render(scene, camera);

  // Update on next frame
  requestAnimationFrame(animate);
}

// Wait for the component to mount then init the scene and animate
onMounted(() => {
  init();
  animate();
});
</script>
<style></style>
