import * as THREE from 'https://cdn.jsdelivr.net/npm/three@0.160.1/build/three.module.js';

// Scene setup
const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
camera.position.z = 5;

const renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.setPixelRatio(window.devicePixelRatio);
document.getElementById('bg').appendChild(renderer.domElement);

// Create particles
const particleCount = 2000;
const particles = new THREE.BufferGeometry();
const posArray = new Float32Array(particleCount * 3);
const colorArray = new Float32Array(particleCount * 3);

for (let i = 0; i < particleCount * 3; i += 3) {
  posArray[i] = (Math.random() - 0.5) * 20;     // x
  posArray[i + 1] = (Math.random() - 0.5) * 20; // y
  posArray[i + 2] = (Math.random() - 0.5) * 20; // z

  colorArray[i] = 0.8 + Math.random() * 0.2;     // r
  colorArray[i + 1] = 0.8 + Math.random() * 0.2; // g
  colorArray[i + 2] = 0.9 + Math.random() * 0.1; // b
}

particles.setAttribute('position', new THREE.BufferAttribute(posArray, 3));
particles.setAttribute('color', new THREE.BufferAttribute(colorArray, 3));

const material = new THREE.PointsMaterial({
  size: 0.05,
  vertexColors: true,
  transparent: true,
  opacity: 0.7,
});

const pointCloud = new THREE.Points(particles, material);
scene.add(pointCloud);

// Mouse interaction
let mouseX = 0, mouseY = 0;
document.addEventListener('mousemove', (e) => {
  mouseX = (e.clientX / window.innerWidth) * 2 - 1;
  mouseY = -(e.clientY / window.innerHeight) * 2 + 1;
});

// Animation
const animate = () => {
  requestAnimationFrame(animate);

  const time = Date.now() * 0.0005;
  const positions = particles.attributes.position.array;
  for (let i = 0; i < particleCount; i++) {
    const i3 = i * 3;
    positions[i3 + 1] += Math.sin(time + i * 0.1) * 0.002; // subtle wave
  }
  particles.attributes.position.needsUpdate = true;

  pointCloud.rotation.x += (mouseY * 0.05 - pointCloud.rotation.x) * 0.05;
  pointCloud.rotation.y += (mouseX * 0.05 - pointCloud.rotation.y) * 0.05;

  renderer.render(scene, camera);
};

animate();

// Resize handling
window.addEventListener('resize', () => {
  camera.aspect = window.innerWidth / window.innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(window.innerWidth, window.innerHeight);
});