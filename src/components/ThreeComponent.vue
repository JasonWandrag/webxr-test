<template>
    <div class="viewport" id="canv" ref="canvas">
        <!-- <ARButton /> -->
    </div>

</template>
    
<script setup lang="ts">
import { onMounted, ref } from 'vue';
import * as THREE from 'three'
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader'
import { TrackballControls } from 'three/examples/jsm/controls/TrackballControls'
// import ARButton from "./ARButton.vue"
// import ARButton from "./ARButton.vue"
import { ARButton } from 'three/examples/jsm/webxr/ARButton';

const testData = [
    // Crab
    {
        modelURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-models/main/Crab.glb",
        audioURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-audio/main/Crab.mp3",
        position: {
            x: getRandomNumberBetween(-3, 3),
            y: 0,
            z: getRandomNumberBetween(-3, 3),
        },
        rotation: {
            x: 0,
            y: Math.random() * 360,
            z: 0,
        },

        moveY: false,
        canMove: true,
        amt: 2,
    },
    // Dolphin
    {
        modelURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-models/main/Dolphin.glb",
        audioURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-audio/main/Dolphin.mp3",
        position: {
            x: getRandomNumberBetween(-3, 3),
            y: getRandomNumberBetween(3, 8),
            z: getRandomNumberBetween(-3, 3),
        },
        rotation: {
            x: 0,
            y: Math.random() * 360,
            z: 0,
        },

        moveY: true,
        canMove: true,
        amt: 2,
    },
    // Eel
    {
        modelURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-models/main/Eel.glb",
        audioURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-audio/main/Eel.mp3",

        position: {
            x: getRandomNumberBetween(-3, 3),
            y: getRandomNumberBetween(1, 3),
            z: getRandomNumberBetween(-3, 3),
        },
        rotation: {
            x: 0,
            y: Math.random() * 360,
            z: 0,
        },

        moveY: true,
        canMove: true,
        amt: 2,
    },
    // Fish
    {
        modelURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-models/main/Fish.glb",
        audioURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-audio/main/Fish.mp3",

        position: {
            x: getRandomNumberBetween(-3, 3),
            y: getRandomNumberBetween(1, 4),
            z: getRandomNumberBetween(-3, 3),
        },
        rotation: {
            x: 0,
            y: Math.random() * 360,
            z: 0,
        },

        moveY: true,
        canMove: true,
        amt: 2,
    },
    // Hammerhead
    {
        modelURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-models/main/Hammerhead.glb",
        audioURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-audio/main/Hammerhead.mp3",

        position: {
            x: getRandomNumberBetween(-3, 3),
            y: getRandomNumberBetween(2, 5),
            z: getRandomNumberBetween(-3, 3),
        },
        rotation: {
            x: 0,
            y: Math.random() * 360,
            z: 0,
        },

        moveY: true,
        canMove: true,
        amt: 2,
    },
    // Lobster
    {
        modelURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-models/main/Lobster.glb",
        audioURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-audio/main/Lobster.mp3",

        position: {
            x: getRandomNumberBetween(-3, 3),
            y: 0,
            z: getRandomNumberBetween(-3, 3),
        },
        rotation: {
            x: 0,
            y: Math.random() * 360,
            z: 0,
        },

        moveY: false,
        canMove: true,
        amt: 2,
    },
    // Octopus
    {
        modelURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-models/main/Octopus.glb",
        audioURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-audio/main/Octopus.mp3",

        position: {
            x: getRandomNumberBetween(-3, 3),
            y: getRandomNumberBetween(1, 4),
            z: getRandomNumberBetween(-3, 3),
        },
        rotation: {
            x: 0,
            y: Math.random() * 360,
            z: 0,
        },

        moveY: true,
        canMove: true,
        amt: 2,
    },
    // Penguin
    {
        modelURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-models/main/Penguin.glb",
        audioURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-audio/main/Penguin.mp3",

        position: {
            x: getRandomNumberBetween(-3, 3),
            y: getRandomNumberBetween(3, 7),
            z: getRandomNumberBetween(-3, 3),
        },
        rotation: {
            x: 0,
            y: Math.random() * 360,
            z: 0,
        },

        moveY: true,
        canMove: true,
        amt: 2,
    },
    // Seal
    {
        modelURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-models/main/Seal.glb",
        audioURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-audio/main/Seal.mp3",

        position: {
            x: getRandomNumberBetween(-3, 3),
            y: getRandomNumberBetween(3, 7),
            z: getRandomNumberBetween(-3, 3),
        },
        rotation: {
            x: 0,
            y: Math.random() * 360,
            z: 0,
        },

        moveY: true,
        canMove: true,
        amt: 2,
    },
    // Shark
    {
        modelURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-models/main/Shark.glb",
        audioURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-audio/main/Shark.mp3",

        position: {
            x: getRandomNumberBetween(-3, 3),
            y: getRandomNumberBetween(1, 7),
            z: getRandomNumberBetween(-3, 3),
        },
        rotation: {
            x: 0,
            y: Math.random() * 360,
            z: 0,
        },

        moveY: true,
        canMove: true,
        amt: 2,
    },
    // Squid
    {
        modelURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-models/main/Squid.glb",
        audioURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-audio/main/Squid.mp3",

        position: {
            x: getRandomNumberBetween(-3, 3),
            y: getRandomNumberBetween(3, 8),
            z: getRandomNumberBetween(-3, 3),
        },
        rotation: {
            x: 0,
            y: Math.random() * 360,
            z: 0,
        },
        scale: 0.5,
        moveY: true,
        canMove: true,
        amt: 2,
    },
    // Starfish
    {
        modelURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-models/main/StarFish.glb",
        audioURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-audio/main/StarFish.mp3",

        position: {
            x: getRandomNumberBetween(-3, 3),
            y: 0,
            z: getRandomNumberBetween(-3, 3),
        },
        rotation: {
            x: 0,
            y: Math.random() * 360,
            z: 0,
        },

        moveY: false,
        canMove: true,
        amt: 2,
    },
    // Stingray
    {
        modelURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-models/main/StingRay.glb",
        audioURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-audio/main/StingRay.mp3",

        position: {
            x: getRandomNumberBetween(-3, 3),
            y: getRandomNumberBetween(3, 6),
            z: getRandomNumberBetween(-3, 3),
        },
        rotation: {
            x: 0,
            y: Math.random() * 360,
            z: 0,
        },

        moveY: true,
        canMove: true,
        amt: 2,
    },
    // Turtle
    {
        modelURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-models/main/Turtle.glb",
        audioURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-audio/main/Turtle.mp3",

        position: {
            x: getRandomNumberBetween(-3, 3),
            y: getRandomNumberBetween(2, 5),
            z: getRandomNumberBetween(-3, 3),
        },
        rotation: {
            x: 0,
            y: Math.random() * 360,
            z: 0,
        },

        moveY: true,
        canMove: true,
        amt: 2,
    },
    // Whale
    {
        modelURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-models/main/Whale.glb",
        audioURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-audio/main/Whale.mp3",

        position: {
            x: getRandomNumberBetween(-3, 3),
            y: getRandomNumberBetween(3, 7),
            z: getRandomNumberBetween(-3, 3),
        },
        rotation: {
            x: 0,
            y: Math.random() * 360,
            z: 0,
        },
        scale: 1.5,
        moveY: false,
        canMove: true,
        amt: 1,
    },
    // ENVIRONMENT
    // Coral
    {
        modelURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-models/main/Coral.glb",
        audioURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-audio/main/Coral.mp3",
        position: {
            x: getRandomNumberBetween(-3, 3),
            y: 0,
            z: getRandomNumberBetween(-3, 3),
        },
        rotation: {
            x: 0,
            y: Math.random() * 360,
            z: 0,
        },

        moveY: false,
        canMove: false,
        amt: 2,
    },
    // Rocks
    {
        modelURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-models/main/Rocks.glb",
        audioURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-audio/main/Rocks.mp3",

        position: {
            x: getRandomNumberBetween(-3, 3),
            y: 0,
            z: getRandomNumberBetween(-3, 3),
        },
        rotation: {
            x: 0,
            y: Math.random() * 360,
            z: 0,
        },

        moveY: true,
        canMove: false,
        amt: 2,
    },
    // SeaWeed
    {
        modelURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-models/main/SeaWeed.glb",
        audioURL: "https://raw.githubusercontent.com/JasonWandrag/low-poly-aquatic-life-audio/main/SeaWeed.mp3",

        position: {
            x: getRandomNumberBetween(-3, 3),
            y: 0,
            z: getRandomNumberBetween(-3, 3),
        },
        rotation: {
            x: 0,
            y: Math.random() * 360,
            z: 0,
        },

        moveY: false,
        canMove: false,
        amt: 2,
    },
]

let scene = null as any;
let camera = null as any;
let renderer = null as any;
// Extra options for AR Environment
let light = null as any;
let ambientLight = null as any;
let shadowPlane = null as any;
let planeCreated = false as any;
// ThreeJS 3D Models
let modelLoader = new GLTFLoader();
let objectArr = [] as any[];
// Audio
let audioLoader = new THREE.AudioLoader();
let listener = new THREE.AudioListener();
let audioIsInitialized = false;
// Controls
let controls = null as any;
// Hit testing
let hitTestSource = null as any;
let localSpace = null as any;
let hitTestSourceInitialized = false;
// UI
let arBtn = null as any;
const canvas = ref<HTMLElement>()
// Rate of animations
const clock = new THREE.Clock
// RayCasting
let raycaster = null as any;
let selectedObject = null as any;
let line = null as any;
// 1. Create Scene for everything to be loaded into
function createScene() {
    scene = new THREE.Scene()
}

// 2. Create Camera to view Scene and objects within
function createCamera() {
    camera = new THREE.PerspectiveCamera(
        75,
        window.innerWidth / window.innerHeight,
        0.1,
        1000
    )
    scene.add(camera)
    camera.add(listener)
    camera.position.z = 2
    camera.position.y = 1
}
function getCameraPosition() {
    return camera.position
}
function getCameraRotation() {
    const rotation = new THREE.Quaternion()
    rotation.setFromRotationMatrix(camera.matrixWorld)
    return rotation
}
function getCameraDirectionNormalized() {
    const quat = getCameraRotation()
    const cameraDirection = new THREE.Vector3(0, 0, -1)
    cameraDirection.applyQuaternion(quat)
    cameraDirection.normalize()
    return cameraDirection
}

function createVectorXDistanceAwayFromCamera(distanceFromCamera: number) {
    const vector = new THREE.Vector3()
    camera.getWorldDirection(vector)
    vector.multiplyScalar(distanceFromCamera)
    vector.add(camera.position)
    return vector
}

// RayCasting Functions
function createRaycastLine() {
    const lineMaterial = new THREE.LineBasicMaterial({ color: 0xffffff })
    const lineGeometry = new THREE.BufferGeometry()
    const positions = new Float32Array(2 * 3)

    lineGeometry.setAttribute("position", new THREE.BufferAttribute(positions, 3))

    line = new THREE.Line(lineGeometry, lineMaterial)
    scene.add(line)
}
function updateRaycasterHelperLine() {
    const positionStart = createVectorXDistanceAwayFromCamera(0)
    const positionEnd = createVectorXDistanceAwayFromCamera(200)
    const positions = line.geometry.attributes.position.array

    positions[0] = positionEnd.x
    positions[1] = positionEnd.y
    positions[2] = positionEnd.z
    positions[3] = positionStart.x
    positions[4] = positionStart.y - 0.2
    positions[5] = positionStart.z

    line.geometry.attributes.position.needsUpdate = true
}

function setupRaycaster() {
    raycaster = new THREE.Raycaster
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
        if (!audioIsInitialized) {
            await setupAudio()
            audioIsInitialized = true
            playAllAudio()
        }
    })
    // setupAudio()

}
function setupAudio() {
    listener = new THREE.AudioListener()
    camera.add(listener)
    // sphere.add(sound)
}

// 4. Create Model
async function createModel(modelURL: string, audioURL: string, position: any, rotation: any, moveY: boolean, canMove: boolean, amt: number) {
    for (let i = 0; i < amt; i++) {
        const gltObject = await modelLoader.loadAsync(modelURL)
        const glbModel = await loadModel(gltObject, position, rotation, canMove)
        // Load Animations
        const animationMixer = await loadAnimationMixer(gltObject)
        // Load Audio
        const spacialAudio = await loadSpacialAudio(gltObject, audioURL)

        objectArr.push({
            glbModel,
            animationMixer,
            spacialAudio,
            position,
            rotation,
            moveY,
            canMove,
            movementSpeed: Math.random() / 50,
            facing: 0,
            soundPlaying: false,
            posDirection: true,
            targetPosition: {
                x: getRandomNumberBetween(-6, 6),
                y: getRandomNumberBetween(0, 6),
                z: getRandomNumberBetween(-6, 6),
            }
        })
    }
}
async function loadModel(gltObject: any, position: any, rotation: any, canMove: boolean) {

    const model = gltObject.scene

    model.traverse((child: any) => {
        if (child instanceof THREE.Mesh) {
            child.castShadow = true
            child.receiveShadow = true
        }
    })
    model.position.x = getRandomNumberBetween(-3, 3)
    model.position.z = getRandomNumberBetween(-3, 3)
    model.rotation.x = rotation.x
    model.rotation.y = Math.random() * 360
    model.rotation.z = rotation.z
    let scale = Math.random() / 5
    model.scale.set(scale, scale, scale)
    if (!canMove) {
        model.position.y = 0
    } else {
        model.position.y = position.y
    }
    scene.add(model)
    return model
}
async function loadAnimationMixer(modelObj: any) {
    const mixer = new THREE.AnimationMixer(modelObj.scene)
    modelObj.animations.forEach((clip: any) => {
        const action = mixer.clipAction(clip)
        action.play()
    })
    return mixer
}
async function loadSpacialAudio(modelObj: any, audioURL: string) {
    const sound = new THREE.PositionalAudio(listener)
    sound.setRefDistance(0.1)
    sound.setMaxDistance(2)
    sound.setDistanceModel('linear')
    sound.setLoop(true)
    sound.setDirectionalCone(180, 230, 0)
    sound.autoplay = true
    const buffer = await audioLoader.loadAsync(audioURL)
    sound.setBuffer(buffer)
    modelObj.scene.add(sound)
    return sound
}

// 5.  Set up Ambient Light for Illumination, and Directional Light for Shadows
function setupLighting() {
    light = new THREE.DirectionalLight('hsl(0, 100%, 100%)')
    const light2 = new THREE.DirectionalLight('hsl(0, 100%, 100%)')
    const light3 = new THREE.DirectionalLight('hsl(0, 100%, 100%)')
    const light4 = new THREE.DirectionalLight('hsl(0, 100%, 100%)')
    const light5 = new THREE.DirectionalLight('hsl(0, 100%, 100%)')
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
}

// 6. Create a plane to cast the shadow onto
function setupShadow() {
    const planeGeometry = new THREE.PlaneGeometry(40, 40)
    planeGeometry.rotateX(-Math.PI / 2)
    const planeMaterial = new THREE.ShadowMaterial()
    planeMaterial.opacity = 0.5
    shadowPlane = new THREE.Mesh(planeGeometry, planeMaterial)
    shadowPlane.receiveShadow = true // Needed for shadows
    shadowPlane.matrixAutoUpdate = false
    scene.add(shadowPlane)
}


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
function init() {
    createScene()
    createCamera()
    setupRenderer()
    setupAudio()
    testData.forEach(({ modelURL, audioURL, position, rotation, moveY, canMove, amt }) => {
        createModel(modelURL, audioURL, position, rotation, moveY, canMove, amt)
    })
    setupRaycaster()
    createRaycastLine()
    setupLighting()
    setupShadow()
    setupControls()
    canvas.value?.appendChild(renderer.domElement)
    canvas.value?.appendChild(arBtn)
    // Change on window resize
    window.addEventListener('resize', onWindowResize, false)
    renderer.setAnimationLoop((timestamp: any, frame: any) => {
        updateRaycasterHelperLine()
        const cameraDirection = getCameraDirectionNormalized()
        const cameraPosition = getCameraPosition()

        raycaster.set(cameraPosition, cameraDirection)
        const intersectingObjs = raycaster.intersectObjects(scene.children)

        
        if (intersectingObjs.length > 0) {
            for (const intersectingObject of intersectingObjs) {
                if (intersectingObject.object !== selectedObject && selectedObject !== null) {
                    selectedObject.scale.x -= 0.03
                    selectedObject.scale.y -= 0.03
                    selectedObject.scale.z -= 0.03
                    selectedObject = null
                }
                if (intersectingObject.object instanceof THREE.Mesh) {
                    intersectingObject.object.scale.x += 0.03
                    intersectingObject.object.scale.y += 0.03
                    intersectingObject.object.scale.z += 0.03
                    selectedObject = intersectingObject.object
                }
                // console.log(selectedObject);
            }
        } else {
            if (selectedObject !== null) {
                selectedObject.scale.x -= 0.03
                selectedObject.scale.y -= 0.03
                selectedObject.scale.z -= 0.03
                selectedObject = null
            }
        }

        const delta = clock.getDelta()
        if (frame) {
            if (!hitTestSourceInitialized) { initializeHitTestSource() }
            if (hitTestSourceInitialized) {
                const hitTestResults = frame.getHitTestResults(hitTestSource)
                if (hitTestResults.length > 0) {
                    const hit = hitTestResults[0]
                    const pose = hit.getPose(localSpace)
                    shadowPlane.visible = true
                    if (!planeCreated) {
                        shadowPlane.matrix.fromArray(pose.transform.matrix)
                        planeCreated = true
                    }
                }
            }
        }
        objectArr.forEach((obj, i) => {
            if (obj.animationMixer) {
                obj.animationMixer.update(delta)

            }
            if (obj.canMove) {
                const positiveX = obj.glbModel.position.x < obj.targetPosition.x ? true : false
                const positiveY = obj.glbModel.position.y < obj.targetPosition.y ? true : false
                const positiveZ = obj.glbModel.position.z < obj.targetPosition.z ? true : false

                obj.glbModel.position.x += positiveX ? obj.movementSpeed : -obj.movementSpeed
                if (obj.moveY) {
                    obj.glbModel.position.y += positiveY ? obj.movementSpeed : -obj.movementSpeed
                }
                obj.glbModel.position.z += positiveZ ? obj.movementSpeed : -obj.movementSpeed


                // obj.glbModel.scene.rotation.x += positiveX ? speed / 2 : -speed / 2
                obj.glbModel.rotation.y += positiveY ? obj.movementSpeed / 2 : -obj.movementSpeed / 2
                // obj.glbModel.scene.rotation.z += positiveZ ? speed / 2 : -speed / 2


                if (obj.glbModel.position.x > obj.targetPosition.x && positiveX || obj.glbModel.position.x < obj.targetPosition.x && !positiveX) {
                
                    obj.targetPosition.x = getRandomNumberBetween(-3, 3)
                }
                if (obj.glbModel.position.y > obj.targetPosition.y && positiveY || obj.glbModel.position.y < obj.targetPosition.y && !positiveY) {
                
                    obj.targetPosition.y = getRandomNumberBetween(1, 6)
                }
                if (obj.glbModel.position.z > obj.targetPosition.z && positiveZ || obj.glbModel.position.z < obj.targetPosition.z && !positiveZ) {
            
                    obj.targetPosition.z = getRandomNumberBetween(-3, 3)
                }
            } 
        })
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

function playAllAudio() {
    if (objectArr.length) {
        objectArr.forEach(async (obj) => {
            if (obj.spacialAudio && !obj.soundPlaying) {
                await obj.spacialAudio.play()
                obj.soundPlaying = true
            }
        })
    }
}


function getRandomNumberBetween(min: number, max: number) {
    return Math.random() * (max - min + 1) + min;
}
function waitForMs(ms: number) {
    return new Promise((resolve) => setTimeout(resolve, ms));
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