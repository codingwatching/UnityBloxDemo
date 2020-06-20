# UnityBloxDemo
Unity Minecraft like clone

## Controls
* W,A,S,D - movement
* Space - jump
* Left mouse button - break blocks
* Right mouse button - place selected block
* Mouse wheel - switch block
* E - saves player position into file
* R - loads player position from file
* V - toggle fly/noclip

## Technical details
* Main script which loads terrain around specified gameobject is ChunkManager
  * Every frame either one chunk or one mesh is generated (no multithreading as of now)
  * Chunks are loaded from closest to furthest.
* The world is split into chunks of 16x16x16 blocks
  * Blocks are 1 unit wide
  * Each chunk contains 4096 bytes of block data (1 byte per block)
  * It is practically endless in any direction (but there is nothing generated above the ground)
  * Blocks have different hardness (snow having the lowest and stone the largest) so minining times are different.
* Terrain is generated by ChunkGenerator
  * Perlin noise is used to specify height of the terrain
  * Caves are generated if a NoiseGenerator's interpolated 3D noise returns value above a threadshold
  * Grass is on top of the terrain, followed by few layers of dirt and practically endless amount of stone
  * After certain height grass is replaced with snow
* Meshes are generated by ChunkMeshGenerator
  * Generator only provides mesh
  * Generator requires ChunkManager to determine block occlusion at the edges of chunk
  * Generator checks for any exposed block face. For each exposed face a square is placed (see optimizations below)
  * These meshes are used both for collision and rendering

## TODO Optimizations
* Use job system to make terrain generation and mesh creation multithreaded.
* Reduce mesh vertex count by grouping same block faces into convex polygons (https://github.com/nickgravelyn/Triangulator)
* Cull terrain by checking which edges of chunks have openings to neighbouring chunks.
* Fix raycast function

### Possible optimizations
* Use RLE compression on chunks (maybe only for unloaded chunks)
* Split meshes by faces. Only 3 faces of a cube are visible at any time (but it increases draw calls).