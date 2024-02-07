# Materials

Materials are used to put a color on each visible pixel of the geometries.

The algorithms that decide on the color of each pixel are written in programs called shaders. Writing shaders is one of the most challenging parts of WebGL and Three.js. We will use materials with pre-made shaders for now.

## Initialize Material

```javascript

// Remember from Textures Lesson: We need to initialize a texture loader to use external textures

const textureLoader = new THREE.TextureLoader()

// Now we can use our texture loader to load external textures. Pay close attention to the names: alpha texture, matcap? 

const doorColorTexture = textureLoader.load('./textures/door/color.jpg')
const doorAlphaTexture = textureLoader.load('./textures/door/alpha.jpg')
const doorAmbientOcclusionTexture = textureLoader.load('./textures/door/ambientOcclusion.jpg')
const doorHeightTexture = textureLoader.load('./textures/door/height.jpg')
const doorNormalTexture = textureLoader.load('./textures/door/normal.jpg')
const doorMetalnessTexture = textureLoader.load('./textures/door/metalness.jpg')
const doorRoughnessTexture = textureLoader.load('./textures/door/roughness.jpg')
const matcapTexture = textureLoader.load('./textures/matcaps/1.png')
const gradientTexture = textureLoader.load('./textures/gradients/3.jpg')

//Finally we declare our material

const material = new THREE.MeshBasicMaterial({ map: doorColorTexture })


```

## Types of Materials

### MeshBasicMaterial

```javascript

const material = new THREE.MeshBasicMaterial

material.map = doorColorTexture //Map applies texture to the surface of the geometry

material.color = new THREE.color(0xff0000) //color applies color on the surface of the geometry. Color is a class of THREE.

material.wireframe = true //Thickness of 1 wireframe

material.transparent = true //Allows you to add opacity to material
material.opacity = //Opacity of material: 0-1

material.alphaMap = doorAlphaTexture //White will be visible and black won't be

material.side = THREE.DoubleSide //Make material double sided //FrontSide and BackSide are also options; however, DoubleSide requires more of the GPU
```

### MeshNormalMaterial

Mainly used for light

```javascript

//Initialize material:
//The color gradient in the material is the x,y,z depending on camera

const material = new THREE.MeshNormalMaterial

material.wireframe = true;

material.flatShading = true; //Like wireframe  but for flat faces

```

### MeshMatcapMaterial

Needs a texture and according to the face it is trying to draw, it will take the color of the pixel of the image in relation to the camera. Creates an illusion that there is a light behind the camera

```javascript

const material = new THREE.MeshMatcapMaterial()

material.matcap = matcapTexture //Add a texture

```

#### Sources for MatCaps

- [github repo](https://github.com/nidorx/matcaps)
- [Matcap Studio](https://www.kchapelier.com/matcap-studio/)
- Blender: will learn later

### MeshDepthMaterial

Mainly used by THREE.js to capture depth of material, but we will use it when we get into shadows.

```javascript
const material = new THREE.MeshDepthMaterial()
```

### MeshLambertMaterial

First material that truly requires light: here is a quick intro.

```javascript
//Create Light Before Sizes

const ambientLight = new THREE.AmbientLight(0xffffff, 1)
scene.add(ambientLight)

const pointLight = new THREE.PointLight(0xffffff, 30)
scene.add(pointLight)
pointLight.position.set(2,3,4)

// MeshLambertMaterial is the most performant material that uses light. We can also use all the params from MeshBasicMaterial but we also have access to now light params. We will go over these in later materials


//Create Material

const material = new THREE.MeshLambertMaterial()

```

### MeshPhongMaterial

```javascript

const material = new THREE.MeshPhongMaterial()
 
material.shininess = 100 //light reflection intensity
material.specular = new THREE.Color(0x1188ff) //light reflection color
```

### MeshToonMaterial

Not really realistic.

```javascript

const gradientTexture = textureLoader.load('./textures/gradients/3.jpg')


const material = new THREE.MeshToonMaterial()

gradientTexture.minFilter = THREE.NearestFilter //on our gradient and switch out minFilter and maxFilter to nearest filter
gradientTexture.magFilter = THREE.NearestFilter
gradientTexture.generateMipmaps = false // since the gradientTexture image is 3x1 pixels we need to disable mipmap
 
material.gradientMap = gradientTexture 

```

### MeshStandardMaterial

Uses physically based rendering principles (PBR) which is the standard in the industry. This has a better algorithm for metalness and roughness.



```javascript

const material = new THREE.MeshStandardMaterial()

material.metalness = 0.45
material.roughness = 0.65

//Add a Map
material.map = doorColorTexture

//Add Ambient Occlusion: Will add details in the crevices like a door hinge

material.aoMap = doorAmbientOcclusionTexture 
material.aoMapIntensity = 5

//Add displacement Map: Increases height; however, we need more subdivisions

material.displacementMap = doorHeightTexture
material.displacementScale = 0.2

//Add Metalness and Roughness off image map

material.metalnessMap = doorMetalnessTexture
material.roughnessMap = doorRoughnessTexture

//Normal Map

material.normalMap = doorNormalTexture
material.normalScale.set(0.5, 0.5)

//Alpha Map

material.transparent = true
material.alphaMap = doorAlphaTexture


```

## Loading Environment Map

[Polyhaven](https://polyhaven.com/) for environment maps

```javascript

import {RGBELoader} from 'three/examples/jsm/loaders/RGBELoader.js'

//Initialize Environment Map after creating scene

const rgbeLoader = new RGBELoader()

rgbeLoader.load('./textures/environmentMap/2k.hdr', (environmentMap) => {
    
    environmentMap.mapping = THREE.EquirectangularReflectionMapping

    scene.background = environmentMap
    scene.environment = environmentMap
})

```