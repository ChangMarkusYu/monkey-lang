# monkey-lang

A compiler, a virtual machine and a repl for Monkey language, designed by Thorsten Ball. The code in this repo largely references the two books *Writing an Interpreter in Go* 
and *Writing an Interpreter in Go*, written by Mr. Ball himself.

To build and run the repl, simply make a directory `~/go/src/monkey-lang`, clone the files, and then enter 
the command:

`go build -o monkey-lang && ./monkey-lang`

The repl parses user's input, complies it into byte code and executes the byte code in a
virtual stack machine.


## Language Features

### Built-in Types
Monkey supports several built-in types: integer, string, array and hash map.
```
>> let a = 12 + 34 / 56 - 12 * 200
-2388
>> "hello" + " world!"
hello world!
>> let arr = ["hello", 23, "world", [1, 2, "stub"], [5, 6]]
[hello, 23, world, [1, 2, stub], [5, 6]]
>> arr[3][2]
stub
>> {"foo": "bar", "value": 125, 170: "store" }["value"]
125
```

### Functions
Functions are treated as first-class citizens. Closures are also supported. In fact, every function is a closure in Monkey:
```
>> let newAdder = fn(a) { let adder = fn(b) { a + b; }; return adder;}
Closure[0xc000520000]
>> newAdder(10)(12)
22
>> let f = fn(x) { x + 3; }
Closure[0xc00000e180]
>> let h = fn(x) { x * x; }
Closure[0xc00008e000]
>> let compose = fn(f, h) { return fn(x) { f(h(x)); } }
>> compose(f, h)(20)
403
```

### Built-in Functions
There are also several built-in functions available:`first`, `last`, `rest`, `push`and `len`. They are mainly for manipulating arrays and strings:
```
>> let list = [1, 2, 3, 4] 
[1, 2, 3, 4]
>> first(list)
1
>> last(list)
4
>> rest(list)
[2, 3, 4]
>> let anotherList = push(list, 5)
[1, 2, 3, 4, 5]
>> first(rest(rest(rest(anotherList))))
4
>> len("hello, world!")
13
>> len(anotherList)
5
```
To print values, use the `puts` function:
```
>> let a = 12
12
>> let msg = "hello, world!"
hello, world!
>> let map = {"foo": "bar", 123 : 456}
{123: 456, foo: bar}
>> puts(a, msg, map)
12
hello, world!
{foo: bar, 123: 456}
null
```
The trailing `null` is the return value of the `puts` function. Not exactly pleasant user experience. 
This is one of (among many) the limitations of this implementation.

### Demo Program
As a final demostration to showcase the language, below is a classic recursive function for computing a Fibonacci number written in Monkey:
```
>> let fib = fn(x){ if(x == 0){ return 0; } else { if(x == 1) { return 1; } else { fib(x - 1) + fib(x - 2); } } };
Closure[0xc00000e0e0]
>> fib(15)
610
```
