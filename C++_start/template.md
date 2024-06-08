## 模板(template)
解决多个简单函数重载的问题：少写点代码
### 泛型编程
关键字：template
```cpp
template<class T>//t随便取名
template<typename T>//定义各种类型
template<typename T1,typename T2>//定义各种类型
//模板参数定义类型
```

解决很多因为参数类型不同从而构成的函数重载问题，但是用的并非一个函数


```cpp
template<class T>
void swap(T& left,T& right)
{
    T temp=left;
    left=right;
    right=temp;
}
//编译器用模板实例化生成对应的swap函数，swap可以不需要自己搞
//函数模板实例化
```

### 类模板
```cpp

template<class T>
//所有的地方换为T，并且需要显式实例化，因为之前编译器可以通过参数类型进行判断类型，而现在不知道了
stack<int>st1;
stack<double>st2;
add<int>(1.1,2);//显式实例化，显式调用，在具有相同的函数时，这样会执行模板的实例化
//让编译器帮我们完成
```

**必须要显式实例化**
Vector<T>::~Vector()//Vector<T>:类型，这里写类型Vector<T>,并且分离后指明类型、

### 若是传参类型不同，但是type只有一个
func((int)1.1,2)->推演
func<double>(1.1,2)->直接指定
### 模板可与函数构成重载
若调用关系都满足，则会优先调用函数，因为模板一开始并不会实例化，而函数是现成的，
add<int>(1.1,2);//显式实例化，显式调用，在具有相同的函数时，这样会执行模板的实例化
#### 为什么需要有这样的场景呢
例如:
```cpp
template<class T>
T* func(int n)
{
    return new T[n];
}
int *ptr=func<T>(10);//必须显式实例化，因为你无法通过参数去进行区分类型

```
类模板不要去做定义和声明的分离