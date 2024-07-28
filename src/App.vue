<template>
    <button @click="moveCamera">移动相机</button>
    <button @click="moveObject">移动物体</button>
    <div id="main"></div>
</template>
<script setup>
import * as THREE from "three";
import { OrbitControls } from "three/addons/controls/OrbitControls.js";
import { onMounted } from "vue";
import * as dat from "dat.gui";
//dat.gui
//创建控制对象
const controlData = {
    rotationSpeed: 0.01,
    color: "#FF0000", // 注意color的初始值，否则会报错
    wireframe: false,
    envMap: false,
};
//创建实例
const gui = new dat.GUI();
const f = gui.addFolder("配置");
f.add(controlData, "rotationSpeed", 0, 0.1);
//颜色选择器
f.addColor(controlData, "color").onChange((value) => {
    cube.material.color = new THREE.Color(value);
});
//下拉列表
f.add(controlData, "envMap", ["无", "全反射", "漫反射"]);
//选择框
f.add(controlData, "wireframe").onChange((value) => {
    cube.material.wireframe = value;
});
//否则每次刷新页面都会增加一个gui窗口，需要将它添加到dom中
f.domElement.id = "gui";
f.open();

// 创建场景
const scene = new THREE.Scene();
// scene.background = new THREE.Color("red");
//纹理贴图
// const loader = new THREE.CubeTextureLoader();
// loader.setPath("/");
// scene.background = loader.load(["3.jpeg", "3.jpeg", "3.jpeg", "3.jpeg", "3.jpeg", "3.jpeg"]);
//雾
scene.fog = new THREE.Fog(0xcccccc, 10, 15);
// 创建相机
const camera = new THREE.PerspectiveCamera();
//透视相机如果不调整位置是和模型重叠在一起的，可以在编辑器中看到
camera.position.z = 15;
camera.position.y = 5;

//创建立方体
const geometry = new THREE.BoxGeometry();
const material = new THREE.MeshBasicMaterial({
    color: 0x00ff00,
});
//网格
const cube = new THREE.Mesh(geometry, material);
cube.position.y = 2;
cube.position.x = 2;

scene.add(cube);
//网格地面
const gridHelper = new THREE.GridHelper(10, 10);
scene.add(gridHelper);
//移动相机
const moveCamera = () => {
    camera.position.y = 10;
    camera.position.x = 10;
    camera.lookAt(2, 2, 0);
};
//移动物体
const moveObject = () => {
    cube.position.x = 5;
    cube.position.y = 5;
    camera.lookAt(cube.position);
};

onMounted(() => {
    //创建渲染器
    const renderer = new THREE.WebGLRenderer();
    renderer.setSize(window.innerWidth, window.innerHeight - 100);
    //将渲染器添加到页面
    // document.body.appendChild(renderer.domElement);
    document.getElementById("main").appendChild(f.domElement);

    document.getElementById("main").appendChild(renderer.domElement);

    //允许用户使用鼠标来旋转、缩放和移动视角，以便更好地查看 3D 场景。轨道控制器
    const controls = new OrbitControls(camera, renderer.domElement);
    //在对相机的变换进行任何手动更改后，必须调用controls .update()
    //对当相机被控件改变时触发（鼠标按住界面移动）
    // controls.addEventListener("change", () => {
    // console.log("Camera position changed.");
    // });
    //阻尼（拖动过后画面有一个惯性移动效果）必须在动画循环中调用.update ()。
    // controls.enableDamping = true;
    //启用或禁用相机的水平和垂直旋转。默认为 true。
    // controls.enableRotate = false;
    //自动旋转
    // controls.autoRotate = true;

    //坐标轴
    // const axesHelper = new THREE.AxesHelper(10);
    // axesHelper.position.y=5
    // scene.add(axesHelper);

    //创建循环
    function animate() {
        requestAnimationFrame(animate);
        //在对相机的变换进行任何手动更改后，必须调用controls .update()
        // controls.update();
        cube.rotation.x += controlData.rotationSpeed;
        cube.rotation.y += controlData.rotationSpeed;
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
