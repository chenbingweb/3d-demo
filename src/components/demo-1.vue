<template>
  <div>
    <canvas id="canvas" class="canvas"></canvas>
    <button class="btn" @click="onClick">动画修改摄像头位置</button>
    <div class="flex_start">
      <button v-for="(item, i) in list" :key="i" @click="play(item)">
        {{ item }}
      </button>
    </div>
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref } from "vue";
import { OrbitControl } from "oasis-engine-toolkit";
import {
  Camera,
  GLTFResource,
  WebGLEngine,
  Script,
  Entity,
  Vector3,
  Component,
  DirectLight,
  Animator,
  WrapMode,
  AnimatorStateMachine,
  AnimatorControllerLayer,
  AnimatorLayerBlendingMode,
  MeshRenderer,
  Color,
  PBRBaseMaterial,
} from "oasis-engine";
import TWEEN from "@tweenjs/tween.js";
let list = ref([]);
let changeCamera: any = null;
let animator: Animator;
const onClick = () => {
  console.log("click");
  console.log(changeCamera);
  if (changeCamera) {
    changeCamera.start();
  }
};
const play = (val) => {
  animator.play(val);

  const currentState = animator.getCurrentAnimatorState(0);
  // 播放一次
  currentState.wrapMode = WrapMode.Once;
};
let car = "./public/asset/gltf/0802/car.gltf";
// car = "./public/asset/gltf/audia/scene.glb";
// car = "./public/asset/gltf/audia01/audia.gltf";

// car = "./public/asset/gltf/ring/car.gltf"; //ok
let env = "./public/asset/gltf/ring/environment.gltf"; //ok
// car = "./public/asset/gltf/ferrari/ferrari.glb";

car = "./public/asset/gltf/0704/car.gltf";
// env = "./public/asset/gltf/0704/environment.gltf"; //ok

// car = "./public/asset/gltf/0704/cityshanghai.gltf";
// env = "./public/asset/gltf/0704/environment.gltf"; //ok
// car = "./public/asset/person/person2.glb";
onMounted(() => {
  const engine = new WebGLEngine("canvas");
  engine.canvas.resizeByClientSize();

  const rootEntity = engine.sceneManager.activeScene.createRootEntity();

  const cameraEntity = rootEntity.createChild("camera");
  cameraEntity.addComponent(Camera);
  cameraEntity.addComponent(OrbitControl);
  cameraEntity.transform.setPosition(10, 10, 10);
  engine.sceneManager.activeScene.ambientLight.diffuseSolidColor.set(
    1,
    1,
    1,
    1
  );
  const directLightNode = rootEntity.createChild("dir_light");
  const directLightNode2 = rootEntity.createChild("dir_light2");
  directLightNode.addComponent(DirectLight);
  directLightNode2.addComponent(DirectLight);
  directLightNode.transform.setRotation(30, 0, 0);
  directLightNode2.transform.setRotation(-30, 180, 0);

  engine.resourceManager.load<GLTFResource>(car).then((gltf) => {
    // gltf.defaultSceneRoot.transform.scale.set(1, 1, 1);
    console.log(gltf.animations);
    //@ts-ignore
    gltf.animations?.forEach((item) => {
      //@ts-ignore
      list.value.push(item.name);
    });
    animator = gltf.defaultSceneRoot.getComponent(Animator);
    const { animatorController } = animator;
    const animatorStateMachine = new AnimatorStateMachine();
    const additiveLayer = new AnimatorControllerLayer("additiveLayer");
    additiveLayer.stateMachine = animatorStateMachine;
    additiveLayer.blendingMode = AnimatorLayerBlendingMode.Additive;
    animatorController.addLayer(additiveLayer);

    // list.value.forEach((name) => {
    //   const clip = animator.findAnimatorState(name).clip;
    //   const newState = animatorStateMachine.addState(name);
    //   newState.clipStartTime = 1;
    //   newState.clip = clip;
    // });

    // animator.play("hot", 0);
    // animator.play("a_o", 1);

    rootEntity.addChild(gltf.defaultSceneRoot);
    let list2 = [
      "pSphere3 (1)", //
      "pSphere3 (2)",
      "pSphere3 (3)",
      "pSphere3 (4)",
      "pSphere3 (5)",
      "pSphere3",
      "pSphere4 (1)", //
      "pSphere4 (2)",
      "pSphere4 (3)",
      "pSphere4 (4)",
      "pSphere4 (5)",
      "pSphere4",
    ];
    list2.forEach((item) => {
      let obj = rootEntity.findByName(item);

      let mesh = obj.getComponent(MeshRenderer);
      let material = mesh.getMaterial() as PBRBaseMaterial;
      material.isTransparent = true;
      material.baseColor = new Color(0, 0, 0, 0);

      console.log(material);
    });

    changeCamera = rootEntity.addComponent(ChangeCamera);
  });
  engine.resourceManager.load<GLTFResource>(env).then((gltf) => {
    rootEntity.addChild(gltf.defaultSceneRoot);
    changeCamera = rootEntity.addComponent(ChangeCamera);
  });
  engine.run();
});

class ChangeCamera extends Script {
  cameraEntity: Entity | null = null;
  onAwake() {
    this.cameraEntity = this.entity.findByName("camera");
  }
  start() {
    if (this.cameraEntity) {
      let pos = this.cameraEntity.transform.position.clone();
      let tween = new TWEEN.Tween(pos); //创建一段tween动画
      tween.to(new Vector3(0, 0, 9), 1000); //4000：表示动画执行时间4000毫秒(ms)
      tween.onUpdate(() => {
        if (this.cameraEntity) {
          this.cameraEntity.transform.position = pos;
        }
      });
      tween.onComplete(() => {
        console.log("onComplete");
      });
      tween.start();
    }
  }
  onUpdate() {
    TWEEN.update();
  }
}
</script>

<style scoped>
.canvas {
  width: 100%;
  height: 100vh;
}
.btn {
  position: absolute;
  left: 0;
  top: 0;
}
.flex_start {
  width: 100%;
  position: absolute;
  right: 0;
  bottom: 30px;
}
</style>
