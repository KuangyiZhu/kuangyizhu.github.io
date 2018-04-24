C++ 筆記 delete this

在 reference count based 的 class，通常當 refCount 在 release function 被減至 0 時，使用 delete this 來結束自己的生命。使用 delete this 是一個合法但危險的指令。在 delete this 之後，基本上 this 即是一個 dangling pointer。

只能使用在在 heap 空間生成的 object，也是符合 new/delete 成對的原則。相對於 stack 空間的物件，呼叫 delete 是 unsafe 的。 Calling delete on variable allocated on the stack
delete this 所做的其中之一動作，就是呼叫 destructor。故你無法在 destructor 裡使用 delete this。
stack 生成的自動物件，都會在 out-of-block 的時候，呼叫 destructor。可以將 desturctor label 成 private 的。這樣子就能保證，只能由 delete this 這端將 object 消滅。(stack based 的自動變會造成 compile error)

### reference
* https://medium.com/@andrestc/implementing-malloc-and-free-ba7e7704a473 malloc
* https://blog.csdn.net/jasonblog/article/details/5989224 delete this
* https://git.kernel.org/pub/scm/virt/kvm/kvm.git/tree/Documentation/memory-barriers.txt memory barrier
