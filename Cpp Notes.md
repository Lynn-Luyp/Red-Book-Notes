# STL

## 迭代器

```cpp
int ia[] = {1, 2, 3, 4, 5};
vector<int> v1(ia, std::end(ia)); // 这里std::end(ia)返回的是ia的数组末尾的指针，不是5！不能解引用
```

## map

### 排序相关

- 随机访问迭代器（vector<int>）内部实现了 < 的比较运算符，调用的是按照字典序进行排序，vector<int>::iterator可以用来作为map的key

- 非随机访问迭代器（list<int>）内部没有实现 < 的比较运算符，list<int>::iterator不能作为map的key

### 基本操作

`insert`：insert一个已经存在的key，会失效，并不会用新的值覆盖原先的值





# lambda

`auto f = [](int a, int b){ return a + b; };`

[] 是捕获列表，如果为空表示不捕获任何值，只使用传入的参数或者全局变量。

### find_if

### for_each

### remove_if

### merge

 

# 变量

1. 局部变量，类成员变量，初始化的时候不会初始化为0，需要 {} 进行初始化
2. static变量，类的对象（vector等）初始化，默认调用构造函数。

原因：C++为了性能，不会自动初始化局部变量。



# 算法

需要 `#include <numeric>`

## accumulate

`vector<int> v; std::accumulate(v.begin(), v.end(), 0);`

````cpp
// 这是错的，输入第三个参数是初始值，也决定了type T。如果第三个参数是0，那么累加的结果返回类型也为T
vector<double> v; std::accumulate(v.begin(), v.end(), 0);
````



# 函数

## 类型

- 前置返回类型

更清晰，更符合C语言规范

`int _sum(int a, int b) { return a + b; };`

- 尾置返回类型

C++ 11引入，解决返回类型依赖参数类型的问题

`auto _sum(T a, T b) -> decltype(a + b) { return a + b; };`

## bind

```cpp
int _sum(int a = 100, int b = 200) { return a + b; };
int _minus(int a = 100, int b = 200) { return a - b; };
// 10 作为_sum的第一个入参，std::placeholders::_1作为_sum的第二个入参
auto f1 = std::bind(_sum, 10, std::placeholders::_1);
// 这里std::placeholders::_2 指的是函数调用时入参的第2个实参，不是函数的第2个入参
auto f2 = std::bind(_sum, 10, std::placeholders::_2);
auto f3 = std::bind(_sum, 10, std::placeholders::_1);
auto f4 = std::bind(_sum, 10, std::placeholders::_2);
auto f5 = std::bind(_sum, std::placeholders::_1, 10);
cout << f1(5) << endl; // 输出15
cout << f2(5) << endl; // 编译报错，第2个实参需要输入2个参数
cout << f3(5) << endl; // 输出5
cout << f4(-100, 5) << endl; // 输出5
cout << f5(5) << endl; // 输出-5
```





# 智能指针

## 构造函数

### make_shared

```cpp
shared_ptr<int> ptr1 = make_shared<int>(5); // 优先使用
shared_ptr<int> ptr2 = shared_ptr<int>(new int(5));
```

区别：

1. 优先使用 ptr1 语法，因为只有一次内存申请，包括智能指针和new int控制块，所以智能指针和控制块内存是连续的；如果使用 ptr2 语法，涉及到两次内存申请，一次是new int(5); 一次是make_shared<int> 对象的内存申请，内存是不连续的。如果在new int(5) 内存申请正常后报错，则会出现内存泄露。 





```cpp
shared_ptr<vector<int>> ptr = make_shared<vector<int>>();
&ptr; // 返回智能指针的地址
ptr.get(); // 返回裸指针地址，也就是vector<int>对象的地址
ptr->data(); // 返回vector<int>对象首元素的地址 
&(*ptr.get())[0]; // 同ptr->data();
```

&(*ptr.get())[0] 解释：

- ptr.get(): 返回裸指针（vector<int>对象）
- *ptr.get(): 返回vector<int>对象的地址
- *ptr.get()[0]: 返回vector<int>[0]元素
- &(*ptr.get())[0]: 返回vector<int>[0]元素的地址，同ptr->data();



## 易错点

```cpp
shared_ptr<int> p(5);
shared_ptr<int>(p.get()); // 会导致p变成悬垂指针
```



