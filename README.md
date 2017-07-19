# hello-world
sup
<p> stuff </p>

[links](https://google.com)

<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/86/three.js"></script>

<script type="x-shader/x-vertex" id="vertexShader">

attribute vec3 center;
varying vec3 vCenter;
void main() {
vCenter = center;
gl_Position = projectionMatrix * modelViewMatrix * vec4( position, 1.0 );
}
</script>

<script type="x-shader/x-fragment" id="fragmentShader">

varying vec3 vCenter;
float edgeFactorTri() {
vec3 d = fwidth( vCenter.xyz );
vec3 a3 = smoothstep( vec3( 0.0 ), d * 1.5, vCenter.xyz );
return min( min( a3.x, a3.y ), a3.z );
}
void main() {
gl_FragColor.rgb = mix( vec3( 1.0 ), vec3( 0.2 ), edgeFactorTri() );
gl_FragColor.a = 1.0;
}
</script>

<script>

var camera, scene, renderer;
init();
animate();
function init() {
var geometry, material, mesh;
var size = 150;
camera = new THREE.PerspectiveCamera( 40, window.innerWidth / window.innerHeight, 1, 2000 );
camera.position.z = 800;
scene = new THREE.Scene();
//
geometry = new THREE.BoxGeometry( size, size, size );
material = new THREE.MeshBasicMaterial( { wireframe: true } );
mesh = new THREE.Mesh( geometry, material );
mesh.position.x = -150;
scene.add( mesh );
//
geometry = new THREE.BufferGeometry().fromGeometry( new THREE.BoxGeometry( size, size, size ) );
setupAttributes( geometry );
material = new THREE.ShaderMaterial( {
uniforms: {},
vertexShader: document.getElementById( 'vertexShader' ).textContent,
fragmentShader: document.getElementById( 'fragmentShader' ).textContent
} );
material.extensions.derivatives = true;
mesh = new THREE.Mesh( geometry, material );
mesh.position.x = 150;
scene.add( mesh );
//
geometry = new THREE.BufferGeometry().fromGeometry( new THREE.SphereGeometry( size / 2, 32, 16 ) );
setupAttributes( geometry );
material = new THREE.ShaderMaterial( {
uniforms: {},
vertexShader: document.getElementById( 'vertexShader' ).textContent,
fragmentShader: document.getElementById( 'fragmentShader' ).textContent
} );
material.extensions.derivatives = true;
mesh = new THREE.Mesh( geometry, material );
mesh.position.x = -150;
scene.add( mesh );
// renderer
renderer = new THREE.WebGLRenderer( { antialias: true } );
renderer.setPixelRatio( window.devicePixelRatio );
renderer.setSize( window.innerWidth, window.innerHeight );
document.body.appendChild( renderer.domElement );
// events
window.addEventListener( 'resize', onWindowResize, false );
}
function setupAttributes( geometry ) {
// TODO: Bring back quads
var vectors = [
new THREE.Vector3( 1, 0, 0 ),
new THREE.Vector3( 0, 1, 0 ),
new THREE.Vector3( 0, 0, 1 )
];
var position = geometry.attributes.position;
var centers = new Float32Array( position.count * 3 );
for ( var i = 0, l = position.count; i < l; i ++ ) {
vectors[ i % 3 ].toArray( centers, i * 3 );
}
geometry.addAttribute( 'center', new THREE.BufferAttribute( centers, 3 ) );
}
function onWindowResize() {
camera.aspect = window.innerWidth / window.innerHeight;
camera.updateProjectionMatrix();
renderer.setSize( window.innerWidth, window.innerHeight );
}
function animate() {
requestAnimationFrame( animate );
for ( var i = 0; i < scene.children.length; i ++ ) {
var object = scene.children[ i ];
object.rotation.x += 0.005;
object.rotation.y += 0.01;
}
renderer.render( scene, camera );
}
</script>
