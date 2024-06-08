# c++动态内存管理
## 兼容c语言
`
calloc=malloc+memset，默认初始化
realloc 扩容，异地扩容会释放原空间
`

## 对比
```cpp
int main()
    {
        //内置类型
        int* p2=(int*)malloc(sizeof(int));、
        //c++兼容c语言，内置类型的动态申请，用法简化，功能一致



        int* p3= new int//new+类型
        //自动计算大小，不需要强制转换类型，不会自动初始化




        //多对象：
        int* p4=(int*)malloc(sizeof(int)*10);
        int* p5=new int[10];


        free()//c

        delete p3;//单对象
        delete[] p5;//多对象 


        //额外支持开空间+初始化
        int* p6=new int(1);//单对象
        int* p7=new int[10]{1,2,3};//多对象,//未指定的初始化为0；

    return 0；
    }

```

## 对于自定义类型的初始化
malloc：只开空间不会自己调用构造函数,不能很好的支持动态申请的自定义对象初始化
`
    //自定义类型，开空间+调用构造函数初始化
    A* p2=new A;

    //自定义类型，调用析构函数+释放空间
    A* p3=new A(3);
    //多个对象也可以
    A* p4=new A[10];
    delete[] p4;
    A aa1(1);
    A aa2(2);
    A* p5 = new A[10]{aa1,aa2};//初始化方式一
    delete[] p5;
    A* p6 = new A[10]{A(1),A(2)};//匿名对象
    delete[] p6;
    A* p7 = new A[10]{1,2};//隐式类型转换
    delete[] p7;

`

## 例子
```cpp
struct ListNode
{
    ListNode* _next;
    int _val;
    ListNode(int val=0)
        :_val(0)
        ,_next(nullptr)
    {}
}
```
例子二：
```cpp
#inlude<iostream>
using namespace std;
int main()
{
    //stack
    stack(size_t capacity=4)
    {
        _array =new datatype[capacity];
        _capacity=capacity

    }
    stack st1;


    return 0;
}


```

对于局部对象
new一个新的对象，在返回后能够继续使用，new返回的是一个对象的指针，因为new先会开一个空间，再会自动调用构造函数，构造函数开空间，delete使用先调用析构函数，再处理空间（new里面的array capcity地大）new-》（array、size、capacity's的空间）-》调用构造函数-》开空间，用指针指向构造函数开的空间

## new：
开空间+调用构造函数
开空间：
```cpp
/*
operator new：该函数实际通过malloc来申请空间，当malloc申请空间成功时直接返回；申请空间
失败，尝试执行空               间不足应对措施，如果改应对措施用户设置了，则继续申请，否
则抛异常。
*/
void *__CRTDECL operator new(size_t size) _THROW1(_STD bad_alloc)
{
// try to allocate size bytes
void *p;
while ((p = malloc(size)) == 0)

     if (_callnewh(size) == 0)
     {
         // report no memory
         // 如果申请内存失败了，这里会抛出bad_alloc 类型异常
         static const std::bad_alloc nomem;
         _RAISE(nomem);
     }
return (p);
}
/*
operator delete: 该函数最终是通过free来释放空间的
*/
void operator delete(void *pUserData)
{
     _CrtMemBlockHeader * pHead;
     RTCCALLBACK(_RTC_Free_hook, (pUserData, 0));
     if (pUserData == NULL)
         return;
     _mlock(_HEAP_LOCK); /* block other threads */
     __TRY
         /* get a pointer to memory block header */
         pHead = pHdr(pUserData);
          /* verify block type */
         _ASSERTE(_BLOCK_TYPE_IS_VALID(pHead->nBlockUse));
         _free_dbg( pUserData, pHead->nBlockUse );
     __FINALLY
         _munlock(_HEAP_LOCK); /* release other threads */
     __END_TRY_FINALLY
     return;
}
/*
free的实现
*/
#define   free(p)               _free_dbg(p, _NORMAL_BLOCK)
```
operator new和operator delete是malloc和free的封装，并且并非重载，大家功能是一模一样的
try catch 只能捕获try里面的，出问题后直接跳到try里面
operator new是为了new所准备的，因为malloc的返回值不符合要求
new是operator new和构造函数的合体，operator new本质上就是malloc只是多了报错时抛异常

## new 和 delete的匹配问题
```cpp
int *p1 =new int[10];
delete(p1);//这里不会内存泄漏
delete[] p1;//内置类型

//自定义类型，必须匹配
//**所以说一定要匹配**
```

当我们在 int *p1 =new int[10];p1具有析构函数
本来是四十个字节，但是编译器会多加十个存个数，p1指向的是10|40 ，在delete[]时知道调用几次析构函数，并且不匹配出错的根本原因是，释放的位置不对

## placement—new(定位new)
构造函数：无法显式调用
析构函数：可以显式调用


```cpp
stack* pointer =(stack*)operator new(sizeof(stack));//operator new
new(pointer)stack(8)//显示调用构造函数
//功能上等价于new


pointer->~stack();//显式调用析构
operator delete(pointer);//operator delete
//等价于delete
//这么使用是为了避免从堆上取数据，提高效率，比如stl内存池，池化技术，特点：频繁使用，提前存储起来/\
```

## malloc/free和new/delete 的主要区别
用法：
前者是函数，后者是操作符；
前者不能初始化，后者可以初始化；
前者需要手动计算空间大小，后者只需要在其后跟上空见的类型即可；
malloc返回值是void* 使用时必须要强转，new不需要，因为new后跟空间类型；
底层原理：
申请自定义类型对象时，前者只开空间，后者多步骤

## c/c++内存分布
为什么要分为栈、堆、数据段代码段：
对不同的内存方便进行管理，、
栈：函数调用建立的局部数据（局部变量），短期变量申请的需求；
堆：自由申请的空间
静态区：变量较为长期的空间申请

**函数中定义的常量const的字符串存储在代码段，所谓的常量区，但是其指针存储在栈上的**
**malloc开的空间在堆上**

## 内存泄漏