<template>
  <div>
    <div ref="container" style="height: 100vh;"></div>
  </div>
</template>

<script>
import * as THREE from 'three';
import TWEEN from '@tweenjs/tween.js'
export default {
  mounted() {
    const container = this.$refs.container;

    // 创建场景、相机和渲染器
    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(
      75,
      container.clientWidth / container.clientHeight,
      0.1,
      1000
    );
    const renderer = new THREE.WebGLRenderer();
    renderer.setSize(container.clientWidth, container.clientHeight);
    container.appendChild(renderer.domElement);

    // 创建头发模型
    const hairGeometry = new THREE.PlaneBufferGeometry(0.2, 2, 10, 20);
    const hairMaterial = new THREE.MeshBasicMaterial({ color: 0xdddddd });
    const hair = new THREE.Mesh(hairGeometry, hairMaterial);
    scene.add(hair);

    // 创建风力
    const wind = { x: 0.1, y: 0, z: 0 };

    // 创建头发顶点的动画
    const hairAnimation = hair.geometry;
    const hairVertices = hairAnimation.attributes.position.array;
    for (let i = 0; i < hairVertices.length; i += 3) {
      hairVertices[i] += Math.random() - 0.5;
      hairVertices[i + 1] += Math.random() - 0.5;
      hairVertices[i + 2] += Math.random() - 0.5;
      // // 判断头发是否超出边界，如果超出，则将其位置重置为初始位置
      // if (hairVertices[i] < -1) {
      //   hairVertices[index] = vertex.x;
      //   hairVertices[index + 1] = vertex.y + 2;
      //   hairVertices[index + 2] = vertex.z;
      // }
    }
    hairAnimation.attributes.position.needsUpdate = true;

    let flag = true;
    const hairTween = new TWEEN.Tween({ angle: 0 })
      .to({ angle: Math.PI * 2 }, 5000)
      .repeat(Infinity)
      .onUpdate(() => {
        if (hairVertices[0] < 10 && flag) {
          for (let i = 0; i < hairVertices.length; i += 3) {
            const windForce = new THREE.Vector3(wind.x, wind.y, wind.z)
              .normalize()
              .multiplyScalar(Math.random() * 0.1);
            hairVertices[i] += windForce.x;
            hairVertices[i + 1] += windForce.y;
            hairVertices[i + 2] += windForce.z;
          }
        } else {
          flag = false;
        }
        if (!flag && hairVertices[0] > -10) {
          for (let i = 0; i < hairVertices.length; i += 3) {
            const windForce = new THREE.Vector3(wind.x, wind.y, wind.z)
              .normalize()
              .multiplyScalar(Math.random() * 0.1);
            hairVertices[i] -= windForce.x;
            hairVertices[i + 1] -= windForce.y;
            hairVertices[i + 2] -= windForce.z;
          }
        } else {
          flag = true;
        }

        hairAnimation.attributes.position.needsUpdate = true;
      });

    // 设置相机位置和目标
    camera.position.z = 5;
    camera.lookAt(hair.position);

    // 渲染场景
    function animate(time) {
      requestAnimationFrame(animate);
      TWEEN.update(time);
      renderer.render(scene, camera);
    }
    animate();
    hairTween.start();
  },
}
</script>
