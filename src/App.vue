<template></template>
<script setup>
import * as THREE from "three";
import { OrbitControls } from "three/addons/controls/OrbitControls.js";
import { onMounted } from "vue";
onMounted(() => {
    // 创建场景
    const scene = new THREE.Scene();
    // scene.background = new THREE.Color("red");
    const loader = new THREE.CubeTextureLoader();
    loader.setPath("/");
    scene.background = loader.load(["3.jpeg", "3.jpeg", "3.jpeg", "3.jpeg", "3.jpeg", "3.jpeg"]);
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
    //创建渲染器
    const renderer = new THREE.WebGLRenderer();
    renderer.setSize(window.innerWidth, window.innerHeight);
    //将渲染器添加到页面
    document.body.appendChild(renderer.domElement);

    //允许用户使用鼠标来旋转、缩放和移动视角，以便更好地查看 3D 场景。轨道控制器
    const controls = new OrbitControls(camera, renderer.domElement);
    //在对相机的变换进行任何手动更改后，必须调用controls .update()
    //对当相机被控件改变时触发（鼠标按住界面移动）
    controls.addEventListener("change", () => {
        // console.log("Camera position changed.");
    });
    //阻尼（拖动过后画面有一个惯性移动效果）必须在动画循环中调用.update ()。
    controls.enableDamping = true;
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
        controls.update();
        cube.rotation.x += 0.01;
        cube.rotation.y += 0.01;
        renderer.render(scene, camera);
    }
    animate();
});
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
