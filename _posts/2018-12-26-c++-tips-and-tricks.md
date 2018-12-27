C++ tips And Tricks
==================

Using POD class as a (private) predecessor of a class
-----------------------------------------------------

    struct XyzData {
        int x_, y_;
        void *p_;
    };

    class Xyz : private XyzData {
        //...
    };

Class `Xyz` can access `XyzData` members as its own data and it can set all members of its predecessor with functions like `bzero()`, `memset()`, or copy its values with `memcpy()`.
It depends on the use-case whether the predecessor should be private or protected.


Using ReadOnly class as a predecessor of a ReadWrite class
----------------------------------------------------------

It seems to be a nice trick to use a class with only get functions as a class with set functions:

    class Config {
    private:
        int x_;

    public:
        Config(Config const&) { … }
        int x() { return x_; }
    };

    class ConfigRW : public Config{
    public:
        ConfigRW(Config const& pred) : Config{pred} {}
        ConfigRW& x(int const _x) { x_ = _x; return *this}
    };

It is now possible to create `Config` from `ConfigRW`:

    Config conf{ConfigRW{}.x(25)};

`Config` can be used as a `constexpr` class.

Simplified definition of a C-string variable
--------------------------------------------

In C++11 it is possible to write:

    constexpr auto constString = "Hello World";

instead of:


    constexpr char constString[] = "Hello World";


Difference between `throw()` and `noexcept`
------------------------------------------

* `noexcept` calls `std::terminate()` while `throw()` calls `std::unexpected()`.
* There are `noexcept` operator and `noexcept` specifier.
* `noexcept` doesn't need to do a stack unwinding, it is implementation defined.

One can be attracted to use `noexcept` as much as possible but thanks to the fact that compiler doesn't do stack unwinding and that exceptions can occur on a lot of places that developer can easily overlook it is better not to use it too often.
`GCC` usually prints only short message and aborts in case of exception inside `nothrow` block and looking for the mistake can add a lot of needless work while performance benefits are negligible.


Useful GCC/Clang command-line arguments
---------------------------

* This command displays all predefined macros for `GCC` compiler:

    $ gcc -E -dM - </dev/null

* Search directories for system headers should be specified with `-system` command-line argument.
  * This argument is similar to `-I`.
  * Directories specified with `-isystem` are searched after directories specified with `-I`.
  * Files from these directories aren't checked with static analyser and warnings from such files doesn't cause errors.

Brief Tips and Tricks
---------------------

* When using SFINAE mark default function as `(...)`.
* `std::endl` does not only print `\n` it also flushes the stream. It can have serious performance impact.
* There is `std::ends` that inserts zero character to the stream.
* `const` means *can be initialised only once* not *cannot be changed*. It is also true in other languages.
* Conversion of `errno` to `error_code`:
  * Standard Library:
    * `ec = std::error_code(errno, std::generic_category())`
  * Boost:
    * `ec = boost::system::error_code(errno, boost::system::generic_category())`
