<template>
  <div>
    <canvas ref="canvas"></canvas>
    <div
      class="point point-0 visible"
      ref="point0"
    >
      <div class="label">1</div>
      <div class="text">
        Approach: the trail / path leading up to the start of a climb.
      </div>
    </div>
  </div>
</template>

<script setup>
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader.js";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
const canvas = ref();
let point0 = ref();
let camera, scene, renderer, controls, points;

function init() {
  // Sizes
  const sizes = {
    width: window.innerWidth,
    height: window.innerHeight
  };

  // Camera
  camera = new THREE.PerspectiveCamera(70, sizes.width / sizes.height, 1, 1000);
  camera.position.z = 10;

  // Scene
  scene = new THREE.Scene();

  // Lighting
  const ambientLight = new THREE.AmbientLight(0xffffff, 1);
  scene.add(ambientLight);

  // Models
  const gltfLoader = new GLTFLoader();
  gltfLoader.load("/models/Boulder/bouldering_rock.glb", (gltf) => {
    const boulder = gltf.scene;
    boulder.scale.set(1, 1, 1);
    boulder.position.y = -8;
    boulder.position.z = 10;
    scene.add(boulder);
  });

  // Points
  points = [
    {
      position: new THREE.Vector3(5, -2.2, 0.76),
      element: point0.value
    }
  ];

  // Renderer
  renderer = new THREE.WebGLRenderer({
    antialias: true,
    canvas: canvas.value,
    alpha: true
  });
  renderer.setPixelRatio(window.devicePixelRatio);
  renderer.setSize(sizes.width, sizes.height);

  // Controls
  controls = new OrbitControls(camera, canvas.value);
  controls.target.set(0, 0.75, 0);
  controls.enableDamping = true;

  // Resize Event Listener
  window.addEventListener("resize", onWindowResize);
}

// Raycaster
const raycaster = new THREE.Raycaster();

// Function for when the window resizes
function onWindowResize() {
  camera.aspect = window.innerWidth / window.innerHeight;
  camera.updateProjectionMatrix();

  renderer.setSize(window.innerWidth, window.innerHeight);
}

// Animate function
function animate() {
  // Go through each point
  for (const point of points) {
    // clone the position of the point
    const screenPosition = point.position.clone();
    // get the 2D screen position of the 3D scene position of the point
    screenPosition.project(camera);
    // ensure point is tied correctly to its 3D position
    const translateX = screenPosition.x * window.innerWidth * 0.5;
    const translateY = -screenPosition.y * window.innerHeight * 0.5;
    point.element.style.transform = `translateX(${translateX}px) translateY(${translateY}px)`;
    // // update raycaster to go from the camera to the point
    // raycaster.setFromCamera(screenPosition, camera);
    // // test raycaster against all the objects in the scene, "true" parameter enables recursive testing
    // const intersects = raycaster.intersectObjects(scene.children, true);
    // // check intersects array, if array is empty then the point should be visible
    // if (intersects.length === 0) {
    //   point.element.classList.add("visible");
    // } else {
    //   // intersection could be behind the point, calculate the distance to the point, then calculate the intersections distance and compare
    //   const intersectionDistance = intersects[0].distance;
    //   const pointDistance = point.position.distanceTo(camera.position);
    //   // if the intersectionDistance is less than the point distance i.e. the point is further away from the camera, it should not be visible
    //   if (intersectionDistance < pointDistance) {
    //     point.element.classList.remove("visible");
    //   } else {
    //     point.element.classList.add("visible");
    //   }
    // }
  }
  // Update controls
  controls.update();

  // Render Scene
  renderer.render(scene, camera);

  // Log Camera position
  // console.log(camera.position);
  // Update on next frame
  requestAnimationFrame(animate);
}

// Wait for the component to mount then init the scene and animate
onMounted(() => {
  init();
  animate();
});
</script>
<style>
.point {
  position: absolute;
  top: 50%;
  left: 50%;
}
.point .label {
  position: absolute;
  top: -20px;
  left: -20px;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background: #00000077;
  border: 1px solid #ffffff77;
  color: #ffffff;
  font-family: Helvetica, Arial, sans-serif;
  text-align: center;
  line-height: 40px;
  font-weight: 100;
  font-size: 14px;
  cursor: help;
  transform: scale(0, 0);
  transition: transform 0.3s;
}
.point.visible .label {
  transform: scale(1, 1);
}
.point .text {
  position: absolute;
  top: 30px;
  left: -120px;
  width: 200px;
  padding: 20px;
  border-radius: 4px;
  background: #00000077;
  border: 1px solid #ffffff77;
  color: #ffffff;
  line-height: 1.3em;
  font-family: Helvetica, Arial, sans-serif;
  font-weight: 100;
  font-size: 14px;
  opacity: 0;
  transition: opacity 0.3s;
  pointer-events: none;
}
.point:hover .text {
  opacity: 1;
}
</style>
