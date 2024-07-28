<template></template>
<script setup>
import * as THREE from "three";
// 创建场景
const scene = new THREE.Scene();
// 创建相机
const camera = new THREE.PerspectiveCamera();
camera.position.z = 15;
camera.position.y = 5;

//创建立方体
const geometry = new THREE.BoxGeometry();
const material = new THREE.MeshBasicMaterial({
    color: 0x00ff00,
});
//网格
const cube = new THREE.Mesh(geometry, material);
cube.position.y = 10;
scene.add(cube);
//网格地面
const gridHelper = new THREE.GridHelper(10, 10);
scene.add(gridHelper);
//创建渲染器
const renderer = new THREE.WebGLRenderer();
renderer.setSize(window.innerWidth, window.innerHeight);
//将渲染器添加到页面
document.body.appendChild(renderer.domElement);
//创建循环
function animate() {
    requestAnimationFrame(animate);
    cube.rotation.x += 0.01;
    cube.rotation.y += 0.01;
    renderer.render(scene, camera);
}
animate();
</script>
<style scoped>
.logo {
    height: 6em;
    padding: 1.5em;
    will-change: filter;
    transition: filter 300ms;
}
.logo:hover {
    filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
    filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
