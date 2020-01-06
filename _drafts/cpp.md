CPP
### C++
https://zhuanlan.zhihu.com/p/54136411
https://cloud.tencent.com/developer/article/1155155
https://www.zfl9.com/cpp-polymorphism.html
http://c.biancheng.net/cpp/biancheng/view/3297.html



### c++
Stack storage is efficient and can be loaded into l-1 cache
Static storage is good in a function for not being initialized when called
Float value is stored in static storage
Thread local is inefficient since it is accessed through a pointer stored in a thread environment block.
The compiler will always select the most efficient integer size if you declare int without specifying the size
Integer addition, subtraction, shifting takes 1 clock circle, multiplication, 11 clocks, division 40-80 clocks

Pre-increment and post-increment
* x = array[i++] is faster than x = array[++i], for not having to wait for the calculation of pointer array
* a = ++b is faster than b = a++, for in the former case compiler will recognize that a and b has the same value and can use same register  

Float numbers
* float and double are same in speed
* floating point addition takes 3-6 clocks, multiplication 4-8, division 14-45, comparison is inefficient and conversion into integer takes a long time

Pointers
* the efficiency problem of the pointer - not because accessing is slow since it is almost the same (accessing a variable or a class member also needs to go through a stack pointer or this pointer), but because accessing point needs extra register storage and it may have to load from the memory when register is not enough

branch
    // Example 7.29a
    float a;  bool b;
    a = b ? 1.5f : 2.6f;
The ?: operator here is a branch. If it is poorly predictable then replace it by a table lookup: 44
// Example 7.29b
    float a;  bool b = 0;
    const float lookup[2] = {2.6f, 1.5f};
    a = lookup[b];
If a bool is used as an array index then it is important to make sure it is initialized or comes from a reliable source so that it can have no other values than 0 or 1.

* A switch statements is a kind of branch that can go more than two ways. Switch statements are most efficient if the case labels follow a sequence where each label is equal to the preceding label plus one, because it can be implemented as a table of jump targets. A switch statement with many labels that have values far from each other is inefficient because the compiler must convert it to a branch tree.
