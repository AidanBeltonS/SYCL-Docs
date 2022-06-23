| Proposal ID      |                                                                                  |
| ---------------- | -------------------------------------------------------------------------------- |
| Name             | SYCL Complex Library                                                             |
| Date of Creation | 6 May 2022                                                                       |
| Target           | SYCL Next                                                                        |
| Current Status   | _Draft_                                                                          |
| Reply-to         | Aidan Belton <belton.aidan@codeplay.com>                                         |
| Original author  | Aidan Belton <belton.aidan@codeplay.com>                                         |
| Contributors     | Aidan Belton <belton.aidan@codeplay.com>                                         |

# SYCL Complex

Currently, SYCL does not have support for complex numbers or
mathematical operations. Some implementations provide complex number
support through extensions. However, the user interface can vary. A
single SYCL complex interface would improve code portability.

The proposed interface follows the existing C++ `std::complex` standard.

The complex type is trivially copyable and type trait `is_device_copyable`
should resolve to `std::true_type`.

## `sycl::complex` interface

This proposal adds the `sycl::complex` class, with the specializations of
`T`; `float`, `double`, and `sycl::half` being defined. The class have
operators `+`,`-`,`*`,`/`,`+=`,`-=`,`*=`,`/=`,`==`,`!=`, `<<`.
In addition the classes have member functions `real()` and `imag()` to allow
the user to access the real and imaginary values of the complex library.


### Template
```C++
namespace sycl {
    template <typename T>
    class complex {
        public:
            complex(const T&, const T&);
            complex(const complex&)
            complex(const std::complex<T>&)

            operator std::complex();

            T real();
            T imag();

            void real(T _re);
            void imag(T _im);

            /*Complex operators*/

            // operator=
            template<typename X>
            complex& operator+=(const complex<X>&);

            // operator+=
            template<typename X>
            complex& operator+=(const complex<X>&);

            // operator-=
            template<typename X>
            complex& operator-=(const complex<X>&);

            // operator*=
            template<typename X>
            complex& operator*=(const complex<X>&);

            // operator/=
            template<typename X>
            complex& operator/=(const complex<X>&);

            /*Real operators*/

            // operator=
            complex& operator=(const T&);

            // operator+=
            complex& operator+=(const T&);

            // operator-=
            complex& operator-=(const T&);

            // operator*=
            complex& operator*=(const T&);

            // operator/=
            complex& operator/=(const T &);
    }

    /*Complex operators*/

    // operator+
    template<typename T>
    complex<T> operator+(const complex<T>&, const complex<T>&);

    // operator-
    template<typename T>
    complex<T> operator-(const complex<T>&, const complex<T>&);

    // operator*
    template<typename T>
    complex<T> operator*(const complex<T>&, const complex<T>&);

    // operator/
    template<typename T>
    complex<T> operator/(const complex<T>&, const complex<T>&);

    // operator==
    template<typename T>
    bool operator==(const complex<T>&, const complex<T>&);

    // operator!=
    template<typename T>
    bool operator!=(const complex<T>&, const complex<T>&);

    /*Real operators*/

    // operator+
    template<typename T>
    complex<T> operator+(const complex<T>&, const T&);
    template<typename T>
    complex<T> operator+(const T&, const complex<T>&);

    // operator-
    template<typename T>
    complex<T> operator/(const complex<T>&, const T&);
    template<typename T>
    complex<T> operator/(const T>&, const complex<T>&);

    // operator==
    template<typename T>
    bool operator==(const complex<T>&, const T&);
    template<typename T>
    bool operator==(const T&, const complex<T>&);

    // operator!=
    template<typename T>
    bool operator!=(const complex<T>&, const T&);
    template<typename T>
    bool operator!=(const T&, const complex<T>&);

    /*Stream operator*/

    // operator<<
    template<typename T>
    const sycl::stream& operator<<(sycl::stream&, const complex<T>&);

} // namespace sycl
```

### Specializations
```C++
namespace sycl {
    template<> class complex<double>;
    template<> class complex<float>;
    template<> class complex<sycl::half>;
} // namespace sycl
```

### Generic typename
The generic type `gencomplex` is defined as `complex<double>`,
`complex<float>`, `complex<sycl::half>`.

### Operator description table

The table belows shows the operators defined by the SYCL complex interface
along with a description of its operation.

| Function                       | Description    |
|--------------------------------|----------------|
| `gencomplex& operator+=(const gencomplex& z);` | Adds and assigns complex number z. |
| `gencomplex& operator+=(const genfloat& x);`   | Adds and assigns real number x. |
| `gencomplex& operator-=(const gencomplex& z);` | Subtracts and assigns complex number z. |
| `gencomplex& operator-=(const genfloat& x);`   | Subtracts and assigns real number x. |
| `gencomplex& operator*=(const gencomplex& z);` | Multiplies and assigns complex number z. |
| `gencomplex& operator*=(const genfloat& x);`   | Multiplies and assigns real number x. |
| `gencomplex& operator/=(const gencomplex& z);` | Divides and assigns complex number z. |
| `gencomplex& operator/=(const genfloat& x);`   | Divides and assigns real number x. |
| `gencomplex operator+(const gencomplex& z, const gencomplex& w);`  | Adds complex numbers z and w and returns the value. |
| `gencomplex operator+(const gencomplex& z, const genfloat& y);`    | Adds complex number z and real w and returns the value. |
| `gencomplex operator+(const genfloat& x, const gencomplex& w);`    | Adds real x and complex number w and returns the value. |
| `gencomplex operator-(const gencomplex& z, const gencomplex& w);`  | Subtracts complex numbers z and w and returns the value. |
| `gencomplex operator-(const gencomplex& z, const genfloat& y);`    | Subtracts complex number z and real y and returns the value. |
| `gencomplex operator-(const genfloat& x, const gencomplex& w);`    | Subtracts real x and complex number w and returns the value. |
| `gencomplex operator*(const gencomplex& z, const gencomplex& w);`  | Multiplies complex numbers z and w and returns the value. |
| `gencomplex operator*(const gencomplex& z, const genfloat& y);`    | Multiplies complex number z and real y and returns the value. |
| `gencomplex operator*(const genfloat& x, const gencomplex& w);`    | Multiplies real x and complex number w and returns the value. |
| `gencomplex operator/(const gencomplex& z, const gencomplex& w);`  | Divides complex numbers z and w and returns the value. |
| `gencomplex operator/(const gencomplex& z, const genfloat& y);`    | Divides complex number z and real y and returns the value. |
| `gencomplex operator/(const genfloat& x, const gencomplex& w);`    | Divides real x and complex number w and returns the value. |
| `gencomplex operator==(const gencomplex& z, const gencomplex& w);` | Compares complex numbers z and w and returns true if they are the same, otherwise false. |
| `gencomplex operator==(const gencomplex& z, const genfloat& y);`   | Compares complex number z and real y and returns true if they are the same, otherwise false. |
| `gencomplex operator==(const genfloat& x, const gencomplex& w);`   | Compares real x and complex number w and returns true if they are the same, otherwise false. |
| `gencomplex operator!=(const gencomplex& z, const gencomplex& w);` | Compares complex numbers z and w and returns true if they are different, otherwise false. |
| `gencomplex operator!=(const gencomplex& z, const genfloat& y);`   | Compares complex number z and real y and returns true if they are different, otherwise false. |
| `gencomplex operator!=(const genfloat& x, const gencomplex& w);`   | Compares real x and complex number w and returns true if they are different, otherwise false. |
| `const sycl::stream& operator<<(sycl::stream& x, const gencomplex& z);`   | Streams the complex number z in the format "(real,imaginary)" into `sycl::stream` x and return the result. |


### SYCL math functions
This proposal adds overloads accepting `gencomplex` for the SYCL math
functions, `abs`, `acos`, `asin`, `atan`, `acosh`, `asinh`, `atanh`,
`cos`, `cosh`, `exp`, `log`, `log10`, `pow`, `sin`, `sinh`, `sqrt`,
`tan`, and `tanh`. Each math function should follow the C++ standard for
handling NaN's and Inf values.

New math functions `arg`, `conj`, `norm`, `polar`, and `proj` are added.

```C++
namespace sycl {
    genfloat abs(const gencomplex& z);

    gencomplex acos(const gencomplex& z);

    gencomplex asin(const gencomplex& z);

    gencomplex atan(const gencomplex& z);

    gencomplex acosh(const gencomplex& z);

    gencomplex asinh(const gencomplex& z);

    gencomplex atanh(const gencomplex& z);

    genfloat arg(const gencomplex& z);

    gencomplex conj(const gencomplex& z);

    gencomplex cos(const gencomplex& z);

    gencomplex cosh(const gencomplex& z);

    gencomplex exp(const gencomplex& z);

    gencomplex log(const gencomplex& z);

    gencomplex log10(const gencomplex& z);

    genfloat norm(const gencomplex& z);

    gencomplex polar(const genfloat& rho, const genfloat& theta = 0);

    gencomplex pow(const gencomplex& z, const genfloat& y);

    gencomplex pow(const gencomplex& z, const gencomplex& w);

    gencomplex pow(const genfloat& x, const gencomplex& w);

    gencomplex proj(const gencomplex& z);

    gencomplex sin(const gencomplex& z);

    gencomplex sinh(const gencomplex& z);

    gencomplex sqrt(const gencomplex& z);

    gencomplex tan(const gencomplex& z);

    gencomplex tanh(const gencomplex& z);
} // namespace sycl
```

### Math function description table

The table below shows each function along with a description of its
mathematical operation.

| Function                                                           | Description                                                                           |
|--------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| `genfloat abs(const gencomplex& z)`                                | Compute the magnitude of complex number x.                                            |
| `gencomplex acos(const gencomplex& z)`                             | Compute the inverse cosine of complex number x.                                       |
| `gencomplex asin(const gencomplex& z)`                             | Compute the inverse sine of complex number x.                                         |
| `gencomplex atan(const gencomplex& z)`                             | Compute the inverse tangent of complex number x.                                      |
| `gencomplex acosh(const gencomplex& z)`                            | Compute the inverse hyperbolic cosine of complex number x.                            |
| `gencomplex asinh(const gencomplex& z)`                            | Compute the inverse hyperbolic sine of complex number x.                              |
| `gencomplex atanh(const gencomplex& z)`                            | Compute the inverse hyperbolic tangent of complex number x.                           |
| `genfloat arg(const gencomplex& z);`                               | Compute phase angle in radians of complex number x.                                   |
| `gencomplex conj(const gencomplex& z)`                             | Compute the conjugate of complex number x.                                            |
| `gencomplex cos(const gencomplex& z)`                              | Compute the cosine of complex number x.                                               |
| `gencomplex cosh(const gencomplex& z)`                             | Compute the hyperbolic cosine of complex number x.                                    |
| `gencomplex exp(const gencomplex& z)`                              | Compute the base-e exponent of complex number x.                                      |
| `gencomplex log(const gencomplex& z)`                              | Compute the natural log of complex number x.                                          |
| `gencomplex log10(const gencomplex& z)`                            | Compute the base-10 log of complex number x.                                          |
| `genfloat norm(const gencomplex& z)`                               | Compute the squared magnitude of complex number x.                                    |
| `gencomplex polar(const genfloat& rho, const genfloat& theta = 0)` | Construct a complex number from polar coordinates with mangitude rho and angle theta. |
| `gencomplex pow(const gencomplex& z, const genfloat& y)`           | Compute complex number z raised to the power of real number y.                     |
| `gencomplex pow(const gencomplex& z, const gencomplex& w)`         | Compute complex number z raised to the power of complex number y.                     |
| `gencomplex pow(const genfloat& x, const gencomplex& w)`           | Compute real number x raised to the power of complex number y.                     |
| `gencomplex proj(const gencomplex& z)`                             | Compute the projection of complex number x.                                           |
| `gencomplex sin(const gencomplex& z)`                              | Compute the sine of complex number x.                                                 |
| `gencomplex sinh(const gencomplex& z)`                             | Compute the hyperbolic sine of complex number x.                                      |
| `gencomplex sqrt(const gencomplex& z)`                             | Compute the square root of complex number x.                                          |
| `gencomplex tan(const gencomplex& z)`                              | Compute the tangent of complex number x.                                              |
| `gencomplex tanh(const gencomplex& z)`                             | Compute the hyperbolic tangent of complex number x.                                   |

### Vector types
There is the possibility to add complex vector types such as `vec` and
`marray`. This would provide users a convenient method to perform vector
math with complex numbers. This is not being proposed currently. However,
it is a consideration for future development.

### Implementation Notes
As the interface follows `std::complex`, it would be possible for
implementations which already implement `std::complex` to implement
`sycl::complex` with aliasing. The only additional function that is added
is the operator overload `<<` for `sycl::stream`, which is a free function,
so this can be added to `std::complex` without changing the class
interface. Implementing `sycl::complex` with `std::complex` could make
adding the new interface easy at present. However, it may restrict
extending and adding to the interface in the future.
