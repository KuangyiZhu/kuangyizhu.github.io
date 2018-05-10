### Atomic
```
    using AtomicDefaultSet::Set;

    template <typename T>
    static void Exchange(volatile T* target, T value, T* old)
    {   
        __asm__ __volatile__(
            "xchgq %0, %1"
            :"=r"(*old)
            :"m"(*target), "0"(value)
            :"memory");
    }   

    template <typename T>
    static T ExchangeAdd(volatile T* target, T value)
    {   
        __asm__ __volatile__
        (   
            "lock; xaddq %0, %1;"
            :"=r"(value)
            :"m"(*target), "0"(value)
        );  
        return value;
    }   

    template <typename T>
    static bool CompareExchange(volatile T* target, T compare, T exchange, T* old)
    {   
        bool result;
        __asm__ __volatile__(
            "lock; cmpxchgq %2,%3\n\t"
            "setz %1"
            : "=a"(*old), "=q"(result)
            : "r"(exchange), "m"(*target), "0"(compare)
            : "memory");
        return result;
    }   

```


### reference 
* [1] https://zhuanlan.zhihu.com/p/31386431 memory order
* [2] http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.152.5245&rep=rep1&type=pdf Memory Barriers: a Hardware View for Software Hackers
