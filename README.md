# py3r ([Chinese version](README_zh.md))

Py3r should have features like:

  - GUI with 3D view
  - Manually add support like Simplify3D
  - Decent slicing algorithm inspired by Simplify3D
  - Written in Python and C++ or Java (because I like java too)

Some research:

  - [Skeinforge](http://fabmetheus.crsndoo.com/wiki/index.php/Skeinforge) is a slicer wirtten in Python.
  - [Sfact](http://reprap.org/wiki/Sfact) is an improved version.
  - [Simple G-Code introduction](http://fabmetheus.crsndoo.com/wiki/index.php/G-Code)
  - This is maybe the latest [source code of Skeinforge](https://github.com/amsler/skeinforge)
  - This is the new Skeinforge [SFact's source code](https://github.com/ahmetcemturan/SFACT)

## Notes about Skeinforge

When I first use Skeinforge with the command line 'python skeinforge.py test.stl' it pops up 2 horrible windows and I lost...

To understand SkeinForge you need to read `documentation/skeinforge_application.skeinforge.html` first.
Skeinforge process g-code with a tool chain, every tool in the chain just do one thing such as carve (slicing) and it's input comes from the output of the previous tool and it's output will be the input of next tool and so on.

I have to say the carved svg file ([a sample](minionfront_carve.svg)) is wonderful when open with a browser and switch to "Iso View".
I never know that svg file can be used to build interactive GUI! Good job Enrique!

## Source code reading notes

Slicing code is in 'skeinforge_plugin/craft_plugin/carve.py', the doc is [Skeinforge Carve](http://fabmetheus.crsndoo.com/wiki/index.php/Skeinforge_Carve).

On the Arcol Blog a method of deriving the layer thickness is posted.  That article "Machine Calibrating" is at:
http://blog.arcol.hu/?p=157

stl.py is the core code to implement function of slicing STL file.

```
carving = svg_writer.getCarving(fileName)
```
The returned object carving is an instance of TriangleMesh, so the svg_writer.getCarving() method is used to translate STL file to GNU TriangleMesh format.

Then program go to ```return CarveSkein().getCarvedSVG( carving, fileName, repository )```.

The slicing code is in ```rotatedLoopLayers = carving.getCarveRotatedBoundaryLayers()```.
That is TriangleMesh.getCarveRotatedBoundaryLayers() method.

In file triangle_mesh.py, method getZAddExtruderPaths is the slicing implementation.
```
def getZAddExtruderPaths(self, z):
	'Get next z and add extruder loops.'
	settings.printProgress(len(self.rotatedLoopLayers), 'slice')
	rotatedLoopLayer = euclidean.RotatedLoopLayer(z)
	rotatedLoopLayer.loops = self.getLoopsFromMesh(self.zoneArrangement.getEmptyZ(z))
	return getZAddExtruderPathsBySolidCarving(rotatedLoopLayer, self, z)

def getLoopsFromMesh( self, z ):
	'Get loops from a carve of a mesh.'
	originalLoops = []
	self.setEdgesForAllFaces()
	if self.isCorrectMesh:
		originalLoops = getLoopsFromCorrectMesh( self.edges, self.faces, self.getTransformedVertexes(), z )
	if len( originalLoops ) < 1:
		originalLoops = getLoopsFromUnprovenMesh( self.edges, self.faces, self.importRadius, self.getTransformedVertexes(), z )
	loops = euclidean.getSimplifiedLoops(originalLoops, self.importRadius)
	sortLoopsInOrderOfArea(True, loops)
	return getOrientedLoops(loops)

def getLoopsFromCorrectMesh( edges, faces, vertexes, z ):
	'Get loops from a carve of a correct mesh.'
	remainingEdgeTable = getRemainingEdgeTable(edges, vertexes, z)
	remainingValues = remainingEdgeTable.values()
	for edge in remainingValues:
		if len( edge.faceIndexes ) < 2:
			print('This should never happen, there is a hole in the triangle mesh, each edge should have two faces.')
			print(edge)
			print('Something will still be printed, but there is no guarantee that it will be the correct shape.' )
			print('Once the gcode is saved, you should check over the layer with a z of:')
			print(z)
			return []
	loops = []
	while isPathAdded( edges, faces, loops, remainingEdgeTable, vertexes, z ):
		pass
	if euclidean.isLoopListIntersecting(loops):
		print('Warning, the triangle mesh slice intersects itself in getLoopsFromCorrectMesh in triangle_mesh.')
		print('Something will still be printed, but there is no guarantee that it will be the correct shape.')
		print('Once the gcode is saved, you should check over the layer with a z of:')
		print(z)
		return []
	return loops

def getRemainingEdgeTable(edges, vertexes, z):
	'Get the remaining edge hashtable.'
	remainingEdgeTable = {}
	if len(edges) > 0:
		if edges[0].zMinimum == None:
			for edge in edges:
				setEdgeMaximumMinimum(edge, vertexes)
	for edgeIndex in xrange(len(edges)):
		edge = edges[edgeIndex]
		if (edge.zMinimum < z) and (edge.zMaximum > z):
			remainingEdgeTable[edgeIndex] = edge
	return remainingEdgeTable

```

## Learned Python knowledge

Programmatically import a module: importlib.import_module()

## Blogs

- http://hydraraptor.blogspot.com about make 3d printer


