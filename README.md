# Simple-Shader-Setup
Threejs shader boilerplate

Package Installs
```
npm create vite@latest ./ (Choose Vanilla)
npm i three
npm i vite-plugin-glsl --force

```

index.html
```
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/vite.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Vite App</title>
    <link rel="stylesheet" href="/src/style.css">
  </head>
  <body>
    <div id="container"></div>
    <script type="module" src="/src/app.js"></script>
  </body>
</html>
```

style.css
```
*{
    margin: 0;
}

#container{
    width: 100%;
    height: 100vh;
    background-color: #2c2929;
}
```

App.js
```
import * as THREE from 'three'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js'

import vertexShader from './shaders/vertexShader.glsl'
import fragmentShader from './shaders/fragmentShader.glsl'

export default class Sketch{
    constructor(options)
    {
        this.container = options.dom
        this.scene = new THREE.Scene()

        this.width = this.container.offsetWidth
        this.height = this.container.offsetHeight

        this.renderer = new THREE.WebGLRenderer({
            alpha: true,
        })
        this.renderer.setSize(this.width, this.height)
        this.container.appendChild(this.renderer.domElement)

        this.camera = new THREE.PerspectiveCamera(70, this.width / this.height, 0.01, 10)
        this.camera.position.z = 1

        this.controls = new OrbitControls(this.camera, this.renderer.domElement)

        this.time = 0

        this.setupResize()
        this.addObjects()
        this.render()
    }

    setupResize()
    {
        window.addEventListener('resize', this.resize.bind(this))
    }

    resize()
    {
        this.width = this.container.offsetWidth
        this.height = this.container.offsetHeight

        this.renderer.setSize(this.width, this.height)
        this.camera.aspect = this.width / this.height
        this.camera.updateProjectionMatrix()
    }

    addObjects()
    {
        this.geometry = new THREE.PlaneGeometry( 1, 1 )
        this.material = new THREE.ShaderMaterial({
            uniforms: {
                time: { value: 1.0 }
            },
            vertexShader: vertexShader,
            fragmentShader: fragmentShader,
        })

        this.mesh = new THREE.Mesh(this.geometry, this.material)
        this.scene.add(this.mesh)
    }

    render()
    {
        this.time += 0.05

        this.renderer.render(this.scene, this.camera)

        window.requestAnimationFrame(this.render.bind(this))
    }
}

new Sketch({
    dom: document.getElementById('container')
})
```

vertexShader.glsl
```
varying vec2 vUv;

void main() {
    vUv = uv;

    gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
}
```

fragmentShader.glsl
```
varying vec2 vUv;

void main() {
    gl_FragColor = vec4(vUv, 0.0, 1.0);
}
```

vite.config.js
```
import glsl from 'vite-plugin-glsl'
import { defineConfig } from 'vite'

export default defineConfig({
    plugins: [glsl()],
})
```
