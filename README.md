# Code Style


## WRS Thoughts

### Libraries we interface with

* Nektar++ `ClassName.MethodName()`, rare exceptions, e.g `PointGeom::dist()`.
* Derived solvers are forced to follow `*.MethodName()` due to how overloading works.
* STD library `class_name.method_name()`.
* SYCL `class_name.method_name()`.

### General considerations
* Prefer const to non-const
* Prefer refs to pointers
* Prefer smart pointers over raw
* Prefer stl containers over e.g. c-style arrays.
* Headers in alphabetical order within groups: stl; nektar++; NESO-Particles etc.
* Indentation is 2 spaces
* Opening brace doesn't get its own line, closing brace does.

### WRS Philosophy/Position on class and method names
* Python has PEP8 and this works well for the Python community.
* C++ does not have a style with such strong adherence to as PEP8 in Python.
* No one style will be liked by everyone.
* The following snippet matches NESO (include) and NESO::Particles (which is consistent).

```
// File names as MyAmazingClass.hpp/cpp or MeaningfulName.hpp for files containing groups of related standalone functions

// PascalCase class name.
class MyAmazingClass {
public:
  // Snake Case method name.
  double get_some_value();
}

// Shared pointers to types should be named as follows
typedef std::shared_ptr<MyAmazingClass> MyAmazingClassSharedPtr;
```

* NESO (solvers) is varied and inconsistent.
* For any non-trival code base with dependencies there will always be a miss-match of styles between code you write and libraries you use.
* `ClassName.method_name()` Is a valid C++ style and conforms to PEP8 - Enables single consistent style across our C++ and any Python written (modulo derived solvers).
* How to stylise `solvers`?


### Other style thoughts

* Prefer class variable prefix to give information an IDE might not be able to deduce or the programmer read from the header file, e.g. is `TypeName m_foo;` a host type or a device type? Programmers should know what is in their scope and the API docs provide this information to users.
* Public vs protected with getter/setter is a separate discussion to naming style - see later section.

## General Specification Through Example

Prototype code style to match Python PEP8.

```

// Capitialised macro names preferred
#define ABS(x) ((x) < 0 ? (-(x)) : (x))

// "g_" If a global really must be used - **must** to const.
extern const int g_please_avoid;

// PascalCase class name.
class MyAmazingClass {

public:
    
  // Constructor, and other special methods, has to match class name.
  // Constructor and deferred constructors come first
  MyAmazingClass();

  // Destructor
  ~MyAmazingClass();

  // friend functions come next and in general methods have docstrings
  /**
   * Doxygen style docstrings
   *
   * @param arg Meaningful destription.
   */
  friend Foo friend_fun(Arg arg);
  
  // Snake Case method name in alphabetical order
  double get_some_value();
    
  // Snake Case attribute (preferred option) where a descriptive name is appropriate.
  // Public member variables are assumed to be accessed as part of the API without
  // getters / setters
  // Member variables in alphabetical order (helps group like-malloced variables):
  //  - initilizer list member variables come first alphabetically
  //  - then the rest
  int an_int_value;

  // Attribute where the name mirroring the maths with a capital may
  // significantly help connecting maths to code.
  double B0;

  // "d_" for device allocated memory (CUDA inspired).
  double * d_x;

  // "h_" for host allocated memory (i.e. for when there is a h_foo and a d_foo).
  double * h_x;
    
  // "s_" for allocated memory that is shared between host and device.
  double * s_x;

  // To discuss virtual method prefix (copies Nektar++)
  // This is relevant in the static-polymorhism case where the API points to an
  // "AbstractBaseClass::method_name" which is a wrapper around an implementation
  // named like "Specialisation::v_method_name".
  virtual void v_metaverse_method();

  // --- Below here are suggestions I find useful ---

  // "b_": SYCL buffers
  sycl::buffer b_x;
    
  // "k_" for local temporaries used in kernels.
  // Note the "this->" (Up for discussion)
  inline void foo(){

    const int k_scalar_int_value = this->scalar_int_value;
    const double k_some_value = this->get_some_value();

    q.submit([&](auto &h) {
      h.parallel_for(range<1>(size), [=](id<1> idx) {
        // use k_scalar_int_value, k_some_value
    });
   }).wait();

  }

protected:

  // To discuss - prefix for protected attributes (copies Nektar++).
  int m_protected_int;
  // See below for alternate use of prefix to denote where the data is on hetrogenous compute.

private:
    
  // To discuss - prefix for private attributes (copies Nektar++).
  int m_private_int;
  // See below for alternate use of prefix to denote where the data is on hetrogenous compute.
}

// Shared pointers to types should be named as follows
typedef std::shared_ptr<MyAmazingClass> MyAmazingClassSharedPtr;


```

## Public vs Getter/Setter

* Public attributes are acceptable - if you alter an attribute of a class in an undocumented manner no guarantees are given (Python style).
* Getters/setters can solve the issue of correctness but add significant overhead if copies are made. If pointers/references are returned to avoid copies it is now the responsibility of the user to manage the lifespan of the pointer w.r.t the lifespan of the original object. Solution(?) make public attributes `const` where possible.


