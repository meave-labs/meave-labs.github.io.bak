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
