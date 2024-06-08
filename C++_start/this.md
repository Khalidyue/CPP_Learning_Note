# 初识C++：this指针
## 情景导入
当我们创建一个类统计一本书的销量和库存时，有些时候可能会遇到整合拷贝的情况，将一本书的销量和库存拷贝给另一个对象：
`
#include<iostream>
using namespace std;

class lib_book//统计一本书的销量和剩余
	{
	public:
		lib_book(int num_1,int num_2)
			:sell_book(num_1)
			,rest_book(num_2)
		{}
		lib_book &copy(const lib_book& old);
	private:
		int sell_book;
		int rest_book;
	};

lib_book &lib_book:: copy(const lib_book& old)//拷贝一本书的销量和剩余
{
	sell_book = old.sell_book;
	rest_book = old.rest_book;
	return ???;
}

`
因为我们还需要接收拷贝的结果，所以我们需要返回这本书的对象，但是传参好像只用了一个变量，我从哪里返回一个结果来表示整个对象呢？这时候this指针应运而生

## this指针的基本使用
当我们在对一个类声明不同的对象时，this指针担任区分对象的功能，c++编译器为非静态的成员函数添加了一个隐藏的指针参数，当我们在当前对象调用的所有成员变量皆为调用this指针，此操作由编译器自动完成。

## this指针的基本特性
1.this指针作为一个关键字，无法在实参和形参的位置显示使用，但能够在类里面进行操作，这样我们就可以补充刚刚提到的情景的返回值啦
`
lib_book &lib_book:: copy(const lib_book& old)//拷贝一本书的销量和剩余
{
	this->sell_book = old.sell_book;//虽然在类里可以操作，但如此的显示使用也是不必要的，这里仅作表明可以在内部操作
	this->rest_book = old.rest_book;
	return *this;//store_1
}
`

2.在成员函数中不能给this指针赋值
我们不能将指向该对象的标识随意更改噢，对象能交换吗~

3.this指针不存储在对象之中，而是存储在栈帧






