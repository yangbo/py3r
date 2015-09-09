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
