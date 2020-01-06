Python
One liner python -c

Python -d -> debug

print str[2:5]     # Prints characters starting from 3rd to 5th
print str[2:]      # Prints string starting from 3rd character
print str * 2      # Prints string two times 

print list          # Prints complete list
print list[0]       # Prints first element of the list
print list[1:3]     # Prints elements starting from 2nd till 3rd 
print list[2:]      # Prints elements starting from 3rd element
print tinylist * 2  # Prints list two times
print list + tinylist # Prints concatenated lists


eval(str) Evaluates a string and returns an object.	
tuple(s) Converts s to a tuple.
list(s) Converts s to a list.

** Exponent	
//	Floor Division

for index in range(len(fruits)):


print format print "My name is %s and weight is %d kg!" % ('Zara', 21)

>>> with open('dog_breeds.txt', 'r') as reader:
>>>     for line in reader.readlines():
>>>         print(line, end='')


With with语句在我们的日常Python代码编写中时常会用到，我们通常知道可以用with语句来代替try…except…finally这样的写法

Iterator yield 与普通函数不同，生成器函数被调用后，其函数体内的代码并不会立即执行，而是返回一个生成器（generator-iterator）。当返回的生成器调用成员方法时，相应的生成器函数中的代码才会执行。

def square():
    for x in range(4):
        yield x ** 2
square_gen = square()
for x in square_gen:
    print(x)


Map 
Reduce 再看reduce的用法。reduce把一个函数作用在一个序列[x1, x2, x3...]上，这个函数必须接收两个参数，reduce把结果继续和序列的下一个元素做累积计算，其效果就是：

reduce(f, [x1, x2, x3, x4]) = f(f(f(x1, x2), x3), x4)

Lambda print filter(lambda x: x % 3 == 0, foo)

For in if print [x * 2 + 10 for x in foo]
