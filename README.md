# Code Style


## Imminent Decision

These conventions are "public" facing and hence changing them:
1. Breaks user-space.
2. Has to be done "all at once" to be remotely clean.

The following snippet matches NESO and NESO::Particles.

```
Filename conventions?

// PascalCase class name.
class MyAmazingClass {
public:
  // Snake Case method name.
  double get_some_value();
}

// Shared pointers to types should be named as follows
typedef std::shared_ptr<MyAmazingClass> MyAmazingClassSharedPtr;
```


## General Specification Through Example

Prototype code style to match Python PEP8.

```

// Capitialised macro names preferred
#define ABS(x) ((x) < 0 ? (-(x)) : (x))

// "g_" If a global really must be used - restrict to const?.
extern const int g_please_avoid;

// PascalCase class name.
class MyAmazingClass {

private:
    
  // To discuss - prefix for private attributes (copies Nektar++).
  int m_private_int;

protected:

  // To discuss - prefix for protected attributes (copies Nektar++).
  int m_protected_int;

public:
    
  // Constructor, and other special methods, has to match class name.
  MyAmazingClass();
  
  // Snake Case method name.
  double get_some_value();
    
  // Snake Case attribute (preferred option) where a descriptive name is appropriate.
  int scalar_int_value;

  // Attribute where the name mirroring the maths with a capital may
  // significantly help connecting maths to code.
  double kB;

  // "d_" for device allocated memory (CUDA inspired).
  double * d_x;

  // "h_" for host allocated memory (i.e. for when there is a h_foo and a d_foo).
  double * h_x;
    
  // "s_" for allocated memory that is shared between host and device.
  double * s_x;

  // To discuss virtual method prefix (copies Nektar++)
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

}

// Shared pointers to types should be named as follows
typedef std::shared_ptr<MyAmazingClass> MyAmazingClassSharedPtr;


```





