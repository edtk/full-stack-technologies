# Python

## Переменные

## Строки

## Структуры данных

## Условные выражения

## Циклы

### Цикл for

```python
for i in range(5):    # for each number i in range 0-4. range(5) function returns list [0, 1, 2, 3, 4]
    print(i)          # this line is executed 5 times. First time i equals 0, then 1, ...


primes = [2, 3, 5, 7]   # create new list

for prime in primes:
    print(prime)
```

### Цикл for для строк

```python
hello_world = "Hello, World!"

for ch in hello_world:    # print each character from hello_world
    print(ch)

length = 0      # initialize length variable

for ch in hello_world:
    length += 1     # add 1 to the length on each iteration

print(len(hello_world) == length)
```

### Цикл while

```python
square = 1

while square <= 10:
    print(square)    # This code is executed 10 times
    square += 1      # This code is executed 10 times

print("Finished")  # This code is executed once

square = 0
number = 1

while square < 81:
    square = number ** 2
    print(square)
    number += 1
```

### break

```python
count = 0

while True:  # this condition cannot possibly be false
    print(count)
    count += 1
    if count >= 5:
        break           # exit loop if count >= 5


zoo = ["lion", 'tiger', 'elephant']
while True:                         # this condition cannot possibly be false
    animal = zoo.pop()       # extract one element from the list end
    print(animal)
    if animal == 'elephant':
        break           # exit loop
```

### continue

```python
for i in range(5):
    if i == 3:
        continue   # skip the rest of the code inside loop for current i value
    print(i)

for x in range(10):
    if x % 2 == 0:
        continue   # skip print(x) for this loop
    print(x)
```

## Функции

```python
def sum_two_numbers(a, b):
    return a + b            # return result to the function caller

c = sum_two_numbers(3, 12)  # assign result of function execution to variable 'c'


def fib(n):
    """This is documentation string for function. It'll be available by fib.__doc__()
    Return a list containing the Fibonacci series up to n."""
    result = []
    a = 1
    b = 1
    while a < n:
        result.append(a)
        tmp_var = b
        b = a + b
        a = tmp_var
    return result

print(fib(10))
```

### Значения по умолчанию

```python
def multiply_by(a, b=2):
    return a * b

print(multiply_by(3, 47))
print(multiply_by(3))    # call function using default value for b parameter


def hello(subject, name="Edik"):
    print("Hello %s! My name is %s" % (subject, name))

hello("PyCharm", "Jane")    # call 'hello' function with "PyCharm as a subject parameter and "Jane" as a name
hello("PyCharm")            # call 'hello' function with "PyCharm as a subject parameter and default value for the name
```

## Классы и объекты

### Объявление класса

```python
class MyClass:
    variable = 1

    def foo(self):   # we'll explain self parameter later in task 4
        print("Hello from function foo")

my_object = MyClass()  # variable "my_object" holds an object of the class "MyClass" that contains the variable and the "foo" function
```

### Доступ к переменным внутри объекта

```python
class MyClass:
    variable1 = 1
    variable2 = 2

    def foo(self):     # we'll explain self parameter later in task 4
        print("Hello from function foo")

my_object = MyClass()
my_object1 = MyClass()

my_object.variable2 = 3     # change value stored in variable2 in my_object

print(my_object.variable2)
print(my_object1.variable2)

my_object.foo()   # call method foo() of object my_object

print(my_object.variable1)
```

```python
class Car:
    color = ""
    def description(self):
        description_string = "This is a %s car." % self.color    # we'll explain self parameter later in task 4
        return description_string

car1 = Car()
car2 = Car()

car1.color = "blue"
car2.color = "red"

print(car1.description())
print(car2.description())
```

### self параметр

```python
class Complex:
    def create(self, real_part, imag_part):
        self.r = real_part
        self.i = imag_part

class Calculator:
    current = 0

    def add(self, amount):
        self.current += amount

    def get_current(self):
        return self.current
```

### __init__ метод

```python
class Square:

    def __init__(self):    # special method __init__
        self.sides = 4

square = Square()
print(square.sides)

class Car:
    def __init__(self, color):
        self.color = color

car = Car("blue")    # Note: you should not pass self parameter explicitly, only color parameter

print(car.color)
```

## Импорты

```python

import calculator

calc = calculator.Calculator()    # create new instance of Calculator class defined in calculator module
calc.add(2)
print(calc.get_current())

import my_module

my_module.hello_world("Edik")
```

```python

from calculator import Calculator

calc = Calculator()    # here we can use Calculator class directly without prefix calculator.
calc.add(2)
print(calc.get_current())

from my_module import hello_world

print(hello_world())    # Note: hello_world function used without prefix
```

## Ввод и вывод файла

### Чтение их файла

```python
f = open("input.txt", "r")   # here we open file "input.txt". Second argument used to identify that we want to read file
                             # Note: if you want to write to the file use "w" as second argument

for line in f.readlines():   # read lines
    print(line)

f.close()                   # It's important to close the file to free up any system resources.

f1 = open("input1.txt", "r")

print(f1.readline())

f1.close()
```

### Запись в файла

```python
zoo = ['lion', "elephant", 'monkey']

if __name__ == "__main__":
    f = open("output.txt", "a")

    for i in zoo:
        f.write(zoo[0])

    f.close()
```