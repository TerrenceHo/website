---
title: From Object Oriented To Functional
date: 2017-10-21
tags: ["python", "functional programming"]
draft: false
markup: mmark
math: true
---

Many coders have heard of the term "functional programming", but have often never really used it or its properties.  Especially if they came from Java, where there are no real functions, only methods each function has to be attached to an object, and cannot stand by itself. Functions are commomly thought of as defining behavior. However, functions can also be thought of as data, instead of just instructions.  They are data that define your instructions.

You can manipulate functions like other types of data.  You can pass it around, alter functions, and use functions to manipulate other data.  If you were to think of functions as data, limitations like having to attach a method to an object to define behavior goes away.  

In this first blog post, we shall experiment with treating functions as data that can define behavior.  To give an example of this, we will implement a calculator.  We will start with a more object oriented version to contrast object oriented vs functional, and then progressively replace functionality of the calculator with functional parts, while maintaining the same overall goal of having a calculator.

We want it to be able to Add, Subtract, and Multiply.  Let's define a _Calculator_ object to hold our accumulated value during calculations.  An accumulated value is simply so we can remember the previous value for successive operations.  $1+2=3$, then three is the accumulated value.  Our next operation will use $3$ and a another number.  We'll also define a _Do_ method to compute an action.


```python
import math

class Calculator(object):
    def __init__(self):
        self.acc = 0.0
    
    def Do(self, opt, v):
        if opt == 'Add':
            self.acc += v
        elif opt == 'Sub':
            self.acc -= v
        elif opt == 'Mul':
            self.acc *= v
        else:
            print("Undefined Operation")
        
        return self.acc
```

Above we have defined a _Calculator_ object.  It has a _Do_ method takes in a string _opt_ to determine which operation to conduct, and a value  _v_ float or int.  It currently has three actions, and returns the accumulated value once it is done computing the value. Below is an example of usage.


```python
c = Calculator()
print(c.Do("Add", 100))
print(c.Do("Sub", 50))
print(c.Do("Mul", 2))
```

    100.0
    50.0
    100.0


Our _Calculator_ is quite limited as of right now.  It can't really do much.  If we wanted to add some other functionality to the calculator, such as calculating the square root of the accumulated value, it would require adding another ```elif``` to the _Do_ method.

We would have to add another conditional for _every_ operation we wanted to do.  This is not maintainable in the long run, and not user friendly.  If the user wanted to define his or her own operation to use with the calculator, they would have to edit the source code itself.  

Let's see if we can move functionality outside of the Do method.  Let's rewrite our calculator a little bit, but this time using a slightly more functional style.  


```python
class Calculator(object):
    def __init__(self):
        self.acc = 0.0
    
    def Do(self, opfunc, v):
        
        self.acc = opfunc(self.acc, v)
        return self.acc
```

Now we have changed our calculator a bit.  _Do_ is much simpler now, and takes in two standard parameters, _opfunc_ and _v_.  What this calculator does is to take in an operation function _opfunc_, as well as the previous value _v_.  Whenever Do is called, it calls the operation we pass in, using the accumulated value the Calculator object holds and and value _v_ passed in to compute a value.  For example, an adding function would take 
```
self.acc + v
``` 
and return the value.

Taking a moment to digest this, the _Do_ method simply takes a function and executes it.  It doesn't take in a number value, but takes a function, evaulates that function using _v_, and returns the computed value.  The advantage of this is that functionality has moved outside the _Do_ method inside the calculator.  Now the _Do_ method only "does" a function that we supply it.   

We can now redefine our previous _Add_, _Sub_, and _Mul_ functions.  These are the functions that we pass into _Do_.  Examples are given below.  

New operation functions must follow the schema below.  It requires two numerical inputs and returns one computed value.
```
def Opt(value_a, value_b):
    return operation
```


```python
def Add(a, b):
    return a + b

def Sub(a, b):
    return a - b

def Mul(a, b):
    return a * b
```

Our _Add_, _Sub_, and _Mul_ function all follow the above schema.  Take a moment to verify this.

To use this calculator, all we have to do is pass in the function to the _Do_ method.  This makes it very simple to define new operation functions and utilize them.  Below is an example.  The numerical outputs are still the same as before with the pre-defined if-statements.


```python
c = Calculator()
print(c.Do(Add, 5))
print(c.Do(Sub, 3))
print(c.Do(Mul, 8))
```

    5.0
    2.0
    16.0


# Extending the Calculator

Let's say some user wanted a square root function.  Easy, we just define another function!   


```python
def Sqrt(a, _):
    return math.sqrt(a)
```

Here is where our code got a little ugly.  _Sqrt_ doesn't need an extra value _v_ because it only acts on the accumulated value.  Previously, all of our operations required two numbers to use, _v_ and the accumulated value.  This is why we have an underscore variable _ in place of v for the second parameter, because we ignore the input value _v_ anyway.  An example of using _Sqrt_ is below.


```python
print(c.Do(Sqrt, 0)) # Last operand ignored
```

    4.0


The _Sqrt_ function currently requires the user to ignore the second operand, which is a bit clunky.  We should clean this up.

This problem occurs because our defined schema for the _Do_ method is rather restricting. It requires that we have two numerical inputs. What if an operation only needs one parameter like _Sqrt_?  How about 3 parameters?  With the _Sqrt_ we could just ignore a parameter, but that's more of a hack than a real solution.  And notice that our _Sqrt_ function was really just a wrapper for the ```math.sqrt(a)``` from the standard library.  It would be more efficient and cleaner if we could just pass in the ```math.sqrt(a)``` function to the _Do_ method instead.

Things get a bit more complicated now.  Let's rewrite our operation functions from a function that takes two parameters and returns a value to a function that returns a function.  This returned function will take a value and return a value.  An example of an _Add_ function is defined below.


```python
def Add(v):
    def operation(acc):
        return acc + v
    return operation
```

We also need to update our _Do_ method.  _Do_ now takes in a function that takes in one value and returns a value.


```python
class Calculator(object):
    def __init__(self):
        self.acc = 0.0
    
    def Do(self, opfunc): 
        self.acc = opfunc(self.acc)
        return self.acc
```

Why does our _Add_ function return a function?  Here's the cool part about passing around functions as data.  We can modify a function before it is executed.  Let's give an example about how the add function would play out.  

Let's try ```Add(10)```.  _Add_ sets _v_ equal to $10$.  It defines a function called _operation_ that takes in a second value _acc_ and adds it to _v_.  Then add returns _operation_.

What specifically is returned when we call ```Add(10)```?  The result of the function looks like this
```
def operation(acc):
    return acc + 10
```

```Add(10)``` returns a function that adds $10$ to _acc_.  ```Add(9)``` would return a function that adds $9$ to _acc_.  

```
def operation(acc):
    return acc + 9
```
Let's see how adding works with _Do_ now.


```python
c = Calculator()
print(c.Do(Add(10)))
print(c.Do(Add(20)))
```

    10.0
    30.0


When calling _Do_, we don't call it with the ```Add``` function itself, but the result of evaluating ```Add(10)```.  The type of evaluating ```Add(10)``` is a function that takes in a value and returning a value. We are calling the operation function that is returned by ```Add(10)```.  
_Do_ then passes in ```self.acc``` into the operation function to add $10$ to ```self.acc```.

We can define other functions in a similiar style.  Thus we can easily define other functions like this as well.


```python
def Sub(v):
    def operation(acc):
        return acc - v
    return operation

def Mul(v):
    def operation(acc):
        return acc * v
    return operation
```


```python
c = Calculator()
print(c.Do(Add(5)))
print(c.Do(Sub(3)))
print(c.Do(Mul(8)))
```

    5.0
    2.0
    16.0


Now how would we implement _Sqrt_?  It's quite simple now.


```python
def Sqrt():
    def operation(acc):
        return math.sqrt(acc)
    return operation

c = Calculator()
print(c.Do(Add(5)))
print(c.Do(Sub(3)))
print(c.Do(Mul(8)))
print(c.Do(Sqrt()))
```

    5.0
    2.0
    16.0
    4.0


Our implementation avoids the awkardness of having to ignore an input variable.  But again _Sqrt_ is just a wrapper of ```math.sqrt(acc)```, so we can simply put that into the calculator instead.  

We can get real crazy and also implement different operations with any number of inputs.  Examples below.


```python
def Crazy(a, b, c, d, e):
    def operation(acc):
        return (((a + b) * d) ** e) + (c * acc )
    return operation

c = Calculator()
print(c.Do(Add(5)))
print(c.Do(Sub(3)))
print(c.Do(Mul(8)))
print(c.Do(math.sqrt))
print(c.Do(math.cos))
print(c.Do(Crazy(1,2,3,4,5)))
```

    5.0
    2.0
    16.0
    4.0
    -0.6536436208636119
    248830.03906913742


# Recap
We removed if statements from our _Calculator_ object and went to an object that took in a function to evaluate.  But while this model was more flexible in that functions could be easily defined outside the _Calculator_ and passed in, it could only take in two parameters.  

To fix that problem, we adjusted our functions a little bit.  Instead, we created functions that return functions that compute an operation.  This allowed us to

# Bonus
If we wanted to, we could get rid of the object _Calculator_ and simply have a closure function instead.  This makes our implementation nearly entirely purely functional.  We need the ```nonlocal``` statement to indicate that ```acc``` is not within the score of the inner function.

Note that because Calculator actually makes sense as an object since it's a noun, it would be better to leave it as an object.  However, this is just to show that closures can take the place of objects and can even be better.


```python
def Calculate():
    acc = 0.0
    def Do(opfunc): 
        nonlocal acc
        acc = opfunc(acc)
        return acc
    return Do
    
c = Calculate()
print(c(Add(5)))
print(c(Sub(3)))
print(c(Mul(8)))
print(c(math.cos))
print(c(Crazy(1,2,3,4,5)))
```

    5.0
    2.0
    16.0
    -0.9576594803233847
    248829.12702155902


# Conclusion

We went from a hard coded layered if statement to a function model, wherein we can pass in any function with any number of parameters to computer an accumulated value.  This kind of abstraction is the kind that can only be offered by functional programming.   

Why is this useful?  Well, come see next the next blog post, where we'll go over using functions to define API configurations.
