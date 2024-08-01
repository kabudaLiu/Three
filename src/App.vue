<template>
    <div id="main"></div>
</template>
<script setup>
import * as THREE from "three";
import { OrbitControls } from "three/addons/controls/OrbitControls.js";
import { onMounted } from "vue";
import * as dat from "dat.gui";

// 创建场景
const scene = new THREE.Scene();
scene.fog = new THREE.Fog(0xcccccc, 1, 3000);
//创建盒子
const geometry = new THREE.BoxGeometry(window.innerWidth, window.innerHeight, 1000);
// 纹理贴图
const texture = new THREE.TextureLoader().load("sky.png");
const material = new THREE.MeshBasicMaterial({
    map: texture,
    side: THREE.DoubleSide,
});
const cube = new THREE.Mesh(geometry, material);
scene.add(cube);

//地球
const earthTexture = new THREE.TextureLoader().load("earth_bg.png");
const earthMaterial = new THREE.MeshPhongMaterial({ map: earthTexture });
const earthGeometry = new THREE.SphereGeometry(20, 32, 32);
const earth = new THREE.Mesh(earthGeometry, earthMaterial);
earth.position.set(-100, 0, -200);
scene.add(earth);
//灯光
const light = new THREE.PointLight(0xffffff, 10000, 10000);
light.position.set(-60, -1, -150);
scene.add(light);
// scene.add(new THREE.PointLightHelper(light, 20));
//平行光
const directionalLight = new THREE.DirectionalLight(0xffffff, 0.5);
scene.add(directionalLight);

//相机
const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
camera.position.z = 10;
camera.lookAt(0, 0, 0);

//星星
const yungeometry = new THREE.BufferGeometry();
const yun2geometry = new THREE.BufferGeometry();

const vertices = [];
const vertices2 = [];

// 创建随机点
for (let i = 0; i < 1500; i++) {
    const x = -window.innerWidth / 2 + window.innerWidth * Math.random();
    const y = -window.innerHeight / 2 + window.innerHeight * Math.random();
    const z = Math.random() * -1000;

    vertices.push(x, y, z);
}
for (let i = 0; i < 1500; i++) {
    const x = -window.innerWidth / 2 + window.innerWidth * Math.random();
    const y = -window.innerHeight / 2 + window.innerHeight * Math.random();
    const z = Math.random() * -1000;

    vertices2.push(x, y, z);
}
// 将点添加到几何体中
yungeometry.setAttribute("position", new THREE.Float32BufferAttribute(vertices, 3));
yun2geometry.setAttribute("position", new THREE.Float32BufferAttribute(vertices2, 3));
const starflake1 = new THREE.TextureLoader().load("starflake1.png");
const starflake2 = new THREE.TextureLoader().load("starflake2.png");

// 创建材质
const yunmaterial = new THREE.PointsMaterial({
    map: starflake1,
    size: 5,
    // blending: THREE.AdditiveBlending,
    transparent: true,
});
const yunmaterial2 = new THREE.PointsMaterial({
    map: starflake2,
    color: 0x7df9ff,
    size: 5,
    blending: THREE.AdditiveBlending,
    transparent: true,
});

// 创建点星
const points = new THREE.Points(yungeometry, yunmaterial);
const points2 = new THREE.Points(yun2geometry, yunmaterial2);

scene.add(points);
scene.add(points2);
//创建云朵
const cloudTexture = new THREE.TextureLoader().load("cloud.png");
const cloudMaterial = new THREE.MeshBasicMaterial({
    map: cloudTexture,
    side: THREE.DoubleSide,
    transparent: true,
});
const cloudGeometry = new THREE.PlaneGeometry(10, 10, 10);
const cloud = new THREE.Mesh(cloudGeometry, cloudMaterial);

scene.add(cloud);
const curve = new THREE.CatmullRomCurve3([new THREE.Vector3(-10, 0, -50), new THREE.Vector3(-5, 5, -40), new THREE.Vector3(-5, 10, -30), new THREE.Vector3(-5, 5, 0), new THREE.Vector3(10, 0, 10)]);

const points3 = curve.getPoints(50);
const geometry3 = new THREE.BufferGeometry().setFromPoints(points3);

const material3 = new THREE.LineBasicMaterial({ color: 0xff0000 });

const curveObject = new THREE.Line(geometry3, material3);
// scene.add(curveObject);

onMounted(() => {
    //创建渲染器
    const renderer = new THREE.WebGLRenderer();
    renderer.setSize(window.innerWidth, window.innerHeight);

    document.getElementById("main").appendChild(renderer.domElement);

    //允许用户使用鼠标来旋转、缩放和移动视角，以便更好地查看 3D 场景。轨道控制器
    const controls = new OrbitControls(camera, renderer.domElement);
    //坐标轴
    const axesHelper = new THREE.AxesHelper(10);
    axesHelper.position.y = 0;
    // scene.add(axesHelper);
    // setInterval(() => {
    //     // if (points.position.z > resetPositionZ) {
    //     points.position.z = -50;
    //     // }
    // }, 7000);
    setInterval(() => {
        if (points.position.z > 400) {
            points.position.z = -600;
        }
    }, 5000);
    setInterval(() => {
        if (points2.position.z > 400) {
            points2.position.z = -500;
        }
    }, 7000);

    // 动画变量
    let t = 0;
    //创建循环
    function animate() {
        requestAnimationFrame(animate);
        //在对相机的变换进行任何手动更改后，必须调用controls .update()
        controls.update();
        earth.rotation.y += 0.01;
        points.position.z += 1;
        points2.position.z += 1;
        // 更新物体沿曲线的位置
        t += 0.01;
        if (t > 1) t = 0; // 循环

        const position = curve.getPointAt(t);
        cloud.position.copy(position);

        // cube.rotation.x += controlData.rotationSpeed;
        // cube.rotation.y += controlData.rotationSpeed;
        renderer.render(scene, camera);
    }
    animate();
});
</script>
<style>
#gui {
    position: absolute;
    right: 0;
    z-index: 100;
}
</style>
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
