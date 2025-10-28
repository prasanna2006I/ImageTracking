# Marker based WebAR

## AIM:
To create a marker-based WebAR experience using MindAR and Three.js, which detects an image marker and overlays an animated drummer raccoon model that plays background music when the marker is visible.


## ALGORITHM:
## STEP 1-
Initialize AR Environment – Set up camera, renderer, and container using MindARThree.
## STEP 2-
Load the Image Marker – Import the .mind file (musicband.mind) for AR detection.
## STEP 3-
Set Up Lighting – Add a HemisphereLight to illuminate the 3D model.
## STEP 4-
Load and Configure the 3D Model – Import the GLTF drummer raccoon, adjust scale and position.
## STEP 5-
Attach Model to Marker Anchor – Link the model to the first target’s anchor in the .mind file.
## STEP 6-
Add Audio and Marker Events – Load background audio and play/pause it on markerFound/markerLost.
## STEP 7-
Animate and Render the Scene – Use AnimationMixer to animate the model and render continuously with MindAR.
## PROGRAM:
## index.html
```
<html>
    <head>
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <script src="./libs/mindar/mindar-image-three.prod.js"></script>
        <script src="./main.js" type="module"></script>
        <style>
            html,body {
                position: relative;
                margin: 0;
                width: 100%;
                height: 100%;
                overflow: hidden;
            }
        </style>
        <link rel="icon" href="data:,">
    </head>
    <body>
    </body>
</html>
```
## main.js
```
import {loadGLTF, loadAudio} from "./libs/loader.js";

const THREE = window.MINDAR.IMAGE.THREE;

document.addEventListener('DOMContentLoaded', () => {
  const start = async() => {
    const mindarThree = new window.MINDAR.IMAGE.MindARThree({
      container: document.body,
      imageTargetSrc: './asserts/musicband.mind',
    });
    const {renderer, scene, camera} = mindarThree;

    const light = new THREE.HemisphereLight( 0xffffff, 0xbbbbff, 1 );
    scene.add(light);

    const gltf = await loadGLTF('./asserts/models/musicband-raccoon/scene.gltf');
    gltf.scene.scale.set(0.1, 0.1, 0.1);
    gltf.scene.position.set(0, -0.4, 0);

    const anchor = mindarThree.addAnchor(0);
    anchor.group.add(gltf.scene);

const audioClip = await loadAudio("./asserts/sounds/musicband-background.mp3");
const listener = new THREE.AudioListener();
const audio = new THREE.PositionalAudio(listener);

camera.add(listener);
anchor.group.add(audio);

audio.setRefDistance(100);
audio.setBuffer(audioClip);
audio.setLoop(true);
anchor.onTargetFound = () =>
{
audio.play();
}
anchor.onTargetLost = () => {
audio.pause();
}

    const mixer = new THREE.AnimationMixer(gltf.scene);
    const action = mixer.clipAction(gltf.animations[0]);
    action.play();

    const clock = new THREE.Clock();

    await mindarThree.start();
    renderer.setAnimationLoop(() => {
      const delta = clock.getDelta();
      gltf.scene.rotation.set(0, gltf.scene.rotation.y+delta, 0);
      mixer.update(delta);
      renderer.render(scene, camera);
    });
  }
  start();
});
```
## File Structure


├── index.html

├── main.js

├── assets/

│   └── models/

│       └── musicband-raccoon/

│           ├── scene.gltf

│           ├── scene.bin

│           └── textures/

## OUTPUT:



![WhatsApp Image 2025-10-28 at 22 03 14_e6f30351](https://github.com/user-attachments/assets/5f2027a9-f952-4f2a-b0d6-e941d1189d93)

## RESULT:
The experiment successfully demonstrates marker-based AR using MindAR and Three.js. The drummer raccoon model was successfully rendered and animated on the detected marker, and synchronized background audio was played, validating effective marker detection and real-time 3D rendering in a web-based AR environment.
