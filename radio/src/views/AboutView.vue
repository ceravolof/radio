<template>
  <div ref="container"></div>
</template>

<script>

import * as THREE from 'three';
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';


export default {

  
  mounted() {
    const w = window.innerWidth;
    const h = window.innerHeight;

    // Creazione della scena, della camera e del renderer
    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(75, w / h, 0.1, 1000);
    camera.position.z = 5;
    const renderer = new THREE.WebGLRenderer({ antialias: true });
    renderer.setSize(w, h);
    this.$refs.container.appendChild(renderer.domElement);

    // Aggiunta dei controlli orbita
    const controls = new OrbitControls(camera, renderer.domElement);

    // Caricamento della texture
    const loader = new THREE.TextureLoader();
    loader.load('../assets/8k_earth_daymap.jpg',
      
      

      function (texture) {
        const geometry = new THREE.IcosahedronGeometry(1, 12);
        const material = new THREE.MeshStandardMaterial({ map: texture });
        const earthMesh = new THREE.Mesh(geometry, material);
        scene.add(earthMesh);
      },
      undefined,
      function (error) {
        console.error('Errore nel caricamento della texture', error);
      }
    );

    // Funzione di animazione
    function animate() {
      requestAnimationFrame(animate);
      controls.update(); // Aggiornamento dei controlli orbita
      renderer.render(scene, camera);
    }
    
    animate();
  },

  methods:{
    
  }
  /*
  methods: {
    mondo() {
      const w = window.innerWidth;
      const h = window.innerHeight;
      const scene = new THREE.scene();
      const camera = new THREE.ProspectiveCamera(75, w / h, 0.1, 1000)
      camera.position.z = 5;
      const render = new THREE.WebGLRenderTarget({ antialias: true });
      render.setSize(w, h)
      document.body.appendChild(render.domElement);
      new OrbitControls(camera, render.domElement)

      const loader = new THREE.TextureLoader();

      const geometry = new THREE.IcosahedronGeometry(1, 12);
      const material = new THREE.MeshStandardMaterial({
        map: loader.load("radio/src/views/img/earthmap1k.jpg"),
        color: 0xffff00,

      });
      const earthMesh = new THREE.Mesh(geometry, material);
      scene.add(earthMesh);
      //const hemiLight = new THREE.//dafinire
      // scene.add(hemiLight);

      function animate() {
        requestAnimationFrame(animate);

        earthMesh.rotation.x += 0.002;
        earthMesh.rotation.y += 0.002;
        render.render(scene, camera);
      }

      animate();
    


  }
 },*/
}


</script>
<style>
  /* Stile opzionale per il contenitore del canvas */
  #container {
    width: 100%;
    height: 100%;
    overflow: hidden;
  }
</style>
