<template>
    <div class="viewport" id="canv" ref="canvas">
    </div>
</template>
    
<script setup lang='ts'>
import { onMounted, ref } from 'vue';
import * as THREE from 'three'
import * as TweakPane from 'tweakpane'
import { TrackballControls } from 'three/examples/jsm/controls/TrackballControls'
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader'
import { ARButton } from 'three/examples/jsm/webxr/ARButton'

const testData = [
    {
        modelURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-models/main/Coral.glb",
        audioURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-audio/main/Coral.mp3",
        position: {
            x: 0,
            y: 0,
            z: 0,
        },
        rotation: {
            x: 0,
            y: 0,
            z: 0,
        },
        scale: 0.1
    },
    {
        modelURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-models/main/Crab.glb",
        audioURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-audio/main/Crab.mp3",
        position: {
            x: 0,
            y: 0,
            z: 0,
        },
        rotation: {
            x: 0,
            y: 0,
            z: 0,
        },
        scale: 0.1
    },
    {
        modelURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-models/main/Dolphin.glb",
        audioURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-audio/main/Dolphin.mp3",
        position: {
            x: 0,
            y: 0,
            z: 0,
        },
        rotation: {
            x: 0,
            y: 0,
            z: 0,
        },
        scale: 0.1
    },
    {
        modelURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-models/main/Eel.glb",
        audioURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-audio/main/Eel.mp3",

        position: {
            x: 0,
            y: 0,
            z: 0,
        },
        rotation: {
            x: 0,
            y: 0,
            z: 0,
        },
        scale: 0.1
    },
    {
        modelURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-models/main/Fish.glb",
        audioURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-audio/main/Fish.mp3",

        position: {
            x: 0,
            y: 0,
            z: 0,
        },
        rotation: {
            x: 0,
            y: 0,
            z: 0,
        },
        scale: 0.1
    },
]

let scene = null as any;
let camera = null as any;
let renderer = null as any;
let light = null as any;   
let ambientLight = null as any;
let modelLoader = new GLTFLoader();
let modelArr = [] as any[];
let mixer = [] as any[];
let shadowPlane = null as any;
// let shadowCameraHelper = null as any;
let controls = null as any;
let arBtn = null as any;
let pane = null as any;
let hitTestSource = null as any;
let localSpace = null as any;
let listener = null as any;
let sound = null as any;
let sphere = null as any;
let hitTestSourceInitialized = false as boolean;
let audioIsInitialized = false as boolean;
let audioIsPlaying = false as boolean;
let planeCreated = false as boolean;

const clock = new THREE.Clock
const PARAMS = {
    x: 0,
    y: 0,
    z: 0,
}
const positionOfAudioAndSphere = {
    x: 0,
    y: 0,
    z: -0.5,
}
const canvas = ref<HTMLElement | undefined>(undefined)

// 1. Create Scene for everything to be loaded into
function setupScene() {
    scene = new THREE.Scene()
}

// 2. Create Camera to view Scene and objects within
function setupCamera() {
    camera = new THREE.PerspectiveCamera(
        75,
        window.innerWidth / window.innerHeight,
        0.1,
        1000
    )
    scene.add(camera)
    // Camera Options
    camera.position.z = 2
    camera.position.y = 1
}
// 3. Create renderer, which handles placing objects onto screen. NB: alpha is needed for camera, otherwise scene background is a solid color.
function setupRenderer() {
    renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true })
    renderer.setSize(window.innerWidth, window.innerHeight)
    renderer.xr.enabled = true
    renderer.shadowMap.enabled = true
    renderer.shadowMap.type = THREE.PCFSoftShadowMap
    arBtn = ARButton.createButton(renderer, {
        requiredFeatures: ["hit-test"],
        optionalFeatures: ["dom-overlay", "dom-overlay-for-handheld-ar"],
        domOverlay: {
            root: document.body
        }
    })
    arBtn.addEventListener('click', async () => {
        if(!audioIsInitialized){
            await setupAudio()
            audioIsInitialized = true
            startAudio()
        }
    })
}

// 4. Load 3D model to scene
function getRandomNumberBetween(min: number,max: number){
    return Math.floor(Math.random()*(max-min+1)+min);
}
async function setupModel(modelURL: string, yPos: number, scale: number) {
    // const textureLoader = new THREE.TextureLoader()
    // const textureURL = 'https://raw.githubusercontent.com/webxr-academy/models/main/jelly/Spotted-Jelly.png'
    // const texture = textureLoader.load(textureURL)
    // texture.encoding = THREE.sRGBEncoding
    // texture.flipY = false

    // const modelURL = 'src/assets/dinomei.glb'
    // const modelURL = 'https://raw.githubusercontent.com/webxr-academy/models/main/jelly/Spotted-Jelly.gltf'
    
    const gltf = await modelLoader.loadAsync(modelURL)
    const model = gltf.scene
    modelArr.push(model)
    model.traverse((child: any) => {
        if (child instanceof THREE.Mesh) {
            // child.material.map = texture
            child.castShadow = true
            child.receiveShadow = true
        }
    })
    model.position.x = getRandomNumberBetween(-3, 3)
    model.position.y = yPos ? yPos : 0
    model.position.z = getRandomNumberBetween(-3, 3)
    model.rotateY(Math.floor(Math.random() * 360))
    // Animations HERE
    const mixerIn = new THREE.AnimationMixer(model)
    mixer.push(mixerIn)
    gltf.animations.forEach((clip: any) => {
        const action = mixerIn.clipAction(clip)
        action.play()
    })
    // Hook up positioning with GUI Panel
    // pane.on('change', (ev: any) => {
    //     // Which input was changed
    //     const type = ev.presetKey
    //     const { value } = ev
    //     switch (type) {
    //         case 'x':
    //             model.position.x = value
    //             break;
    //         case 'y':
    //             model.position.y = value
    //             break;
    //         case 'z':
    //             model.position.z = value
    //             break;
    //         default:
    //             break;
    //     }
    // })
    model.scale.set(scale, scale, scale)
    scene.add(model)
}

// 5. Set up Ambient Light for Illumination, and Directional Light for Shadows
function setupLighting() {
    light = new THREE.DirectionalLight('hsl(0, 100%, 100%)')
    const light2 = new THREE.DirectionalLight('hsl(0, 100%, 100%)')
    const light3 = new THREE.DirectionalLight('hsl(0, 100%, 100%)')
    const light4 = new THREE.DirectionalLight('hsl(0, 100%, 100%)')
    const light5 = new THREE.DirectionalLight('hsl(0, 100%, 100%)')
    // const light6 = new THREE.DirectionalLight('hsl(0, 100%, 100%)')
    ambientLight = new THREE.AmbientLight(0xFFFFFF, 1);
    scene.add(light)
    scene.add(light2)
    scene.add(light3)
    scene.add(light4)
    scene.add(light5)
    scene.add(ambientLight)  
    light.position.set(0, 10, 0)
    light2.position.set(1, 0, 0)
    light3.position.set(-1, 0, 0)
    light4.position.set(0, 0, 1)
    light5.position.set(0, 0, -1)
    // light6.position.set(0, 0, 0)
    light.castShadow = true
    light.shadow.mapSize.width = 2048
    light.shadow.mapSize.height = 2048
    // NEEDED WHEN SETTING DIRECTIONAL LIGHT 
    light.shadow.camera.near = -10
    light.shadow.camera.far = 1000
    light.shadow.camera.left = 10
    light.shadow.camera.right = -10
    light.shadow.camera.top = 10
    light.shadow.camera.bottom = -10
    // shadowCameraHelper = new THREE.CameraHelper(light.shadow.camera)
    // scene.add(shadowCameraHelper)
}

// 6. Create a plane to cast the shadow onto
function setupShadow() {
    const planeGeometry = new THREE.PlaneGeometry(40, 40)
    planeGeometry.rotateX(-Math.PI / 2)
    const planeMaterial = new THREE.ShadowMaterial()
    planeMaterial.opacity = 0.5
    shadowPlane = new THREE.Mesh(planeGeometry, planeMaterial)
    // shadowPlane.visible = false
    shadowPlane.receiveShadow = true // Needed for shadows
    shadowPlane.matrixAutoUpdate = false
    // shadowPlane.position.set(0, -2, 0)
    scene.add(shadowPlane)
}

// 7. Add GUI to manipulate some parameters
function setupGUI() {
    pane = new TweakPane.Pane()
    pane.containerElem_.style.zIndex = "10"
    pane.addInput(PARAMS, 'x', { min: -5, max: 5, step: 0.1 })
    pane.addInput(PARAMS, 'y', { min: -5, max: 5, step: 0.1 })
    pane.addInput(PARAMS, 'z', { min: -5, max: 5, step: 0.1 })

}

// 8. Add controls
function setupControls() {
    controls = new TrackballControls(camera, canvas.value)
    controls.rotateSpeed = 1.0
    controls.zoomSpeed = 5
    controls.panSpeed = 0.8
    controls.noZoom = false
    controls.noPan = false
    controls.staticMoving = true
    controls.dynamicDampingFactor = 0.3;
}

// Initialize ThreeJS Scene and assign values AFTER html has been rendered to properly run scene. Also start running animation loop
function init(): void {
    setupScene()
    setupCamera()
    setupRenderer()
    // setupGUI()
    // setupModel('src/assets/Coral.glb', 0, 0.2)
    setupModel('src/assets/Crab.glb', 0.2, 0.2)
    setupModel('src/assets/Dolphin.glb', 0.2, 0.2)
    setupModel('src/assets/Eel.glb', 0.2, 0.2)
    setupModel('src/assets/Fish.glb', 0.2, 0.2)
    setupModel('src/assets/Hammerhead.glb', 0.2, 0.2)
    setupModel('src/assets/Lobster.glb', 0.2, 0.2)
    setupModel('src/assets/Octopus.glb', 0.2, 0.3)
    setupModel('src/assets/Penguin.glb', 0.2, 0.1)
    setupModel('src/assets/Rocks.glb', 0, 0.05)
    setupModel('src/assets/Rocks.glb', 0, 0.05)
    setupModel('src/assets/Rocks.glb', 0, 0.05)
    setupModel('src/assets/Rocks.glb', 0, 0.05)
    setupModel('src/assets/Rocks.glb', 0, 0.05)
    setupModel('src/assets/Rocks.glb', 0, 0.05)
    setupModel('src/assets/Seal.glb', 0.2, 0.2)
    setupModel('src/assets/SeaWeed.glb', 0, 0.1)
    setupModel('src/assets/SeaWeed.glb', 0, 0.1)
    setupModel('src/assets/SeaWeed.glb', 0, 0.1)
    setupModel('src/assets/SeaWeed.glb', 0, 0.1)
    setupModel('src/assets/SeaWeed.glb', 0, 0.1)
    setupModel('src/assets/Shark.glb', 0.2, 0.2)
    setupModel('src/assets/Squid.glb', 0.2, 0.2)
    setupModel('src/assets/StarFish.glb', 0.2, 0.2)
    setupModel('src/assets/StingRay.glb', 0.2, 0.2)
    setupModel('src/assets/Turtle.glb', 0.2, 0.2)
    setupModel('src/assets/Whale.glb', 3, 3)
    setupLighting() 
    setupShadow()
    setupControls()

    canvas.value?.appendChild(renderer.domElement)
    canvas.value?.appendChild(arBtn)
    // Change on window resize
    window.addEventListener('resize', onWindowResize, false)
    renderer.setAnimationLoop((timestamp: any, frame: any) => {
        
        if (frame) {
            if (!hitTestSourceInitialized) { initializeHitTestSource() }
            if (hitTestSourceInitialized) {
                const hitTestResults = frame.getHitTestResults(hitTestSource)
                if (hitTestResults.length > 0) {
                    const hit = hitTestResults[0]
                    const pose = hit.getPose(localSpace)
                    shadowPlane.visible = true
                    if(!planeCreated){
                        shadowPlane.matrix.fromArray(pose.transform.matrix)
                        planeCreated = true
                    }
                }
            }
        }
        const delta = clock.getDelta()
        if (mixer.length) {
            mixer.forEach((anim: any) => anim.update(delta))
            // mixer.update(delta)
        }
        // let switched = false
        // let switchedZ = false
        // modelArr.forEach((model:any) => {
            
        //     if(Math.abs(model.rotation.y) == 360){
                
        //         switched = !switched;
        //         console.log("Rotation Y",model.rotation.y);
        //         console.log("switched",switched);
        //     }
        //     if(Math.abs(model.position.z) == 2){
                
        //         switchedZ = !switchedZ;
        //         console.log("Position Z",model.position.z);
        //         console.log("Switched Z",switchedZ);
        //     }
    
        //     model.rotation.y =  switchDirection(switched, model.rotation.y, 0.001);
        //     // model.position.x += neg ? Math.sin(DegToRad(model.position.x)) : -Math.sin(DegToRad(model.position.x));
        //     model.position.z = switchDirection(switchedZ, model.position.z, 0.01);
        //     // if(model.rotation.y >= 360){
                
        //     // }
        // })
        renderer.render(scene, camera)
        controls.update()
    })
}
function onWindowResize() {
    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix()
    renderer.setSize(window.innerWidth, window.innerHeight)
}
async function initializeHitTestSource() {
    const session = renderer.xr.getSession()
    const viewerSpace = await session.requestReferenceSpace("viewer")
    hitTestSource = await session.requestHitTestSource({ space: viewerSpace })
    localSpace = await session.requestReferenceSpace("local")
    hitTestSourceInitialized = true

    session.addEventListener("end", () => {
        hitTestSourceInitialized = false
        hitTestSource = null
    })
}
async function setupAudio(){
    listener = new THREE.AudioListener()
    camera.add(listener)
    createSphere()
    await createPositionalAudio()
    sphere.add(sound)
}
function createSphere(){
    const sphereShape = new THREE.SphereBufferGeometry(0.2, 32, 16);
    const sphereMaterial = new THREE.MeshPhongMaterial({ color: 0xff2200 })
    sphere = new THREE.Mesh(sphereShape, sphereMaterial)
    sphere.position.set(positionOfAudioAndSphere.x, positionOfAudioAndSphere.y, positionOfAudioAndSphere.z)
    // scene.add(sphere)
}
async function createPositionalAudio(){
    sound = new THREE.PositionalAudio(listener)
    sound.setRefDistance(0.1)
    sound.setDistanceModel('linear')
    sound.setMaxDistance(5)
    sound.setLoop(true)
    sound.setDirectionalCone(180, 230, 0)

    const audioLoader = new THREE.AudioLoader()
    const audioURL = 'src/assets/dinomei.mp3'
    const buffer = await audioLoader.loadAsync(audioURL)
    sound.setBuffer(buffer)
}
function startAudio(){
    sound.play()
    audioIsPlaying = true
}
onMounted(() => {
    init()
})

</script>
    
<style>
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}
</style>