<script>
    import { onMount } from "svelte";
    import * as THREE from "three";

    let container;
    let camera, scene, renderer;
    let ghosts = [];

    onMount(() => {
        init();
        animate();
    });

    function init() {
        container = document.getElementById("container");

        camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        camera.position.z = 15;

        scene = new THREE.Scene();
        scene.background = new THREE.Color(0x87ceeb);

        const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
        scene.add(ambientLight);

        const pointLight = new THREE.PointLight(0xffffff, 1);
        pointLight.position.set(10, 10, 10);
        scene.add(pointLight);

        for (let i = 0; i < 8; i++) {
            const ghost = createGhost();
            ghost.position.set(Math.random() * 10 - 5, Math.random() * 10 - 5, Math.random() * 10 - 5);
            ghost.rotation.y = Math.random() * Math.PI;
            ghosts.push(ghost);
            scene.add(ghost);
        }

        renderer = new THREE.WebGLRenderer({ antialias: true });
        renderer.setSize(window.innerWidth, window.innerHeight);
        container.appendChild(renderer.domElement);

        window.addEventListener("resize", onWindowResize);
    }

    function onWindowResize() {
        camera.aspect = window.innerWidth / window.innerHeight;
        camera.updateProjectionMatrix();
        renderer.setSize(window.innerWidth, window.innerHeight);
    }

    function createGhost() {
        const ghost = new THREE.Group();
        const bodyMaterial = new THREE.MeshBasicMaterial({ color: 0xffffff, transparent: true, opacity: 0.7 });

        const bodyGeometry = new THREE.ConeGeometry(1, 2, 16);
        const body = new THREE.Mesh(bodyGeometry, bodyMaterial);
        body.position.y = 1;
        ghost.add(body);

        const eyeMaterial = new THREE.MeshBasicMaterial({ color: 0x000000 });
        const eyeGeometry = new THREE.SphereGeometry(0.15, 16, 16);

        const leftEye = new THREE.Mesh(eyeGeometry, eyeMaterial);
        const rightEye = new THREE.Mesh(eyeGeometry, eyeMaterial);
        leftEye.position.set(-0.3, 1.7, 0.85);
        rightEye.position.set(0.3, 1.7, 0.85);
        ghost.add(leftEye);
        ghost.add(rightEye);

        return ghost;
    }

    function animate() {
        requestAnimationFrame(animate);
        animateGhosts(ghosts);
        renderer.render(scene, camera);
    }

    function animateGhosts(ghosts) {
        ghosts.forEach((ghost, index) => {
            const offset = index * (Math.PI / 4);
            ghost.position.y = 3 * Math.sin(0.5 * (Date.now() * 0.001 + offset));
            ghost.lookAt(camera.position);
        });
    }
</script>

<svelte:window on:resize="{onWindowResize}" />

<div id="container" bind:this="{container}"></div>

<style>
    html,
    body,
    #container {
        margin: 0;
        padding: 0;
        width: 100%;
        height: 100%;
        overflow: hidden;
    }
</style>