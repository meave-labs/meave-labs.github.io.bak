Putting old code into new blocks
--------------------------------

During changing of older source code it is quite common to take parts of the old code and put them into new blocks, for example:

    void func() {
        command0;
        command1;
        // ...
        commandN();
    }

Needs to be changed to the following code, maybe because there is a test that needs to be run over several possible values now:

    void func() {
        for (auto const &x: {val0, val1, ..., valM}) {
            command0;
            command1;
            // ...
            commandN();
        }
    }

Diff of such change can be quite confusing: It would contain two useful lines with the addition of the beginning and end of the `for` statement and two lines for every `command`. A person responsible for code review would have a hard time then to filter that few useful changes from the waste amount of mechanical non-important changes:

     void func() {
    -    //command0();
    -    //command1();
    -    //...
    -    //commandN();
    +    for (auto const &x: {val0, val1, ..., valM}) {
    +        //command0();
    +        //command1();
    +        //...
    +        //commandN();
    +    }
     }

A possible solution is to use half-indentation in such case: Code above uses four spaces for indentation so we can indent the first and the last line of `for` by two spaces and remain lines with commands unchanged.
Resulting diff is much easier to understand and to verify:

     void func() {
    +  for (auto const &x: {val0, val1, ..., valM}) {
         //command0();
         //command1();
         //...
         //commandN();
    +  }
     }
