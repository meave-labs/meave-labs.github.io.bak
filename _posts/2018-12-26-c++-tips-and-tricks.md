C++ tips And Tricks
==================

Using POD class as a (private) predecessor of a class
-----------------------------------------------------

    ```c++
    struct XyzData {
        int x_, y_;
        void *p_;
    };

    class Xyz : private XyzData {
        //...
    };
    ```

Class `Xyz` can access `XyzData` members as its own data and it can set its predecessor with functions like `bzero()`, `memset()`, or copy its values with `memcpy()`.
It depends on the use-case whether the predecessor should be private or protected.
