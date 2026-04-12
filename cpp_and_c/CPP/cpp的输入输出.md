# C++的输入输出

## 输出

- `# include <iostream>`   没有后缀  
- `using namespace std;`   endl 和 cout、cin 一样，都是 C++ 标准库中的内容，定义在 std 命名空间中。  

```cpp
#include <iostream>
using namespace std;

int main(){
    cout << "Hello World!\n";
    cout << "Hello World!" << endl;
    cout << "Hello\tWorld!";
    //让World到下一个制表位开始输出
    cout << "Hello" << "World!\n";
    // 两者之间无空格
}
```

## 输入

```cpp
# include <iostream>
using namespace std;

int main(){
    cin >> a;
    cin >> b;
    //输入12   24
    //a = 12 b = 24
}
```

因为会先进入缓存区，cin是从缓存区取数，默认空格为分隔符  

## ::使用

被称为作用域解析运算符  

- 访问命名空间的成员，如`std::cout //表示 std 命名空间下的 cout`
- 访问类的静态成员
静态成员（变量或函数）是属于类本身的，而不是属于某个具体的对象。因此，不需要创建对象，直接通过类名加 :: 即可访问。  

```cpp
class Counter {
public:
    static int count; // 静态成员声明
    static void add() { count++; }
};

int Counter::count = 0; // 静态成员必须在类外初始化

int main() {
    // 不需要 new 对象，直接用 类名::成员
    Counter::add(); 
    std::cout << Counter::count << std::endl;
    return 0;
}
```

- 访问全局变量

```cpp
int a = 10; // 全局变量

int main() {
    int a = 20; // 局部变量
    
    std::cout << a << std::endl;   // 输出 20 (局部)
    std::cout << ::a << std::endl; // 输出 10 (全局，:: 表示“全局作用域”)
    return 0;
}
```
