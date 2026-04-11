# cpp的输入输出

## 输出
```cpp
# include <iostream>
int main(){
    std::cout << "Hello World!\n";
}

# include <iostream>
using namespace std;
/*
endl 和 cout、cin 一样，都是 C++ 标准库中的内容，定义在 std 命名空间中。
*/

int main(){
    cout << "Hello World!\n";
    cout << "Hello World!" << endl;
    cout << "Hello\tWorld!"
    //让World到下一个制表位开始输出
    cout << "Hello" << "World!\n";
    // 两者之间无空格

}
```

## 输入

`cin >> age;`

```cpp
cin >> a;
cin >> b;
//输入12   24
//a = 12 b = 24
```
因为会先进入缓存区，cin是从缓存区取数，默认空格为分隔符  