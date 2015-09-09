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

## Learned Python knowledge

Programmatically import a module: importlib.import_module()

## Blogs

- http://hydraraptor.blogspot.com about make 3d printer


