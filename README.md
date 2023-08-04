# dart_earcut

Ear-clipping (earcutting) triangulation algorithm, ported (with minor API differences) from [earcut4j/earcut4j](https://github.com/earcut4j/earcut4j) and [mapbox/earcut](https://github.com/mapbox/earcut).

## Usage

* 2D (x/y) coordinates, without holes  
  * `triangulateRaw`: expects points in the format `[x0, y0, x1, y1, x2, y2, ...]`
  * `triangulateFromPoints`: expects 'dart:math's `Point` objects

  ```dart
  final triangles = Earcut.triangulateRaw([10,0, 0,50, 60,60, 70,10]);
  final triangles = Earcut.triangulateFromPoints([Point(10, 0), Point(0,50), Point(60,60), Point(70,10)]);
  // Both return [1,0,3, 3,2,1]
  ```

* Holes  
  A list of hole indicies, if any. For example, `[5, 8]` for a 12-vertice input would mean one hole with vertices 5-7 and another with 8-11. If you pass a single vertice as a hole, Earcut treats it as a Steiner point.

  ```dart
  final List<int> triangles = Earcut.triangulateRaw([0, 0, 100, 0, 100, 100, 0, 100, 20, 20, 80, 20, 80, 80, 20, 80], holeIndices: [4]);
  // Returns [3,0,4, 5,4,0, 3,4,7, 5,0,1, 2,3,7, 6,5,1, 2,7,6, 6,1,2]
  ```

* More dimensions  
  Expected to be in the format `[x0, y0, z0, x1, y1, z1, x2, y2, z2, ...]`

  ```dart
  final List<int> triangles = Earcut.triangulateRaw([10, 0, 1, 0, 50, 2, 60, 60, 3, 70, 10, 4], dimensions: 3);
  // Returns [1,0,3, 3,2,1]
  ```

## The Algorithm

The library implements a modified ear slicing algorithm, optimized by [z-order curve](https://en.wikipedia.org/wiki/Z-order_curve) hashing and extended to handle holes, twisted polygons, degeneracies and self-intersections in a way that doesn't _guarantee_ correctness of triangulation, but attempts to always produce acceptable results for practical data.

It's based on ideas from [FIST: Fast Industrial-Strength Triangulation of Polygons](https://www.cosy.sbg.ac.at/~held/projects/triang/triang.html) by Martin Held and[Triangulation by Ear Clipping](https://www.geometrictools.com/Documentation/TriangulationByEarClipping.pdf) by David Eberly.
