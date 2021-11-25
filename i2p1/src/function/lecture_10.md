# Lecture 10: Function & Reference (1)

## Why Functions?

寫 function 好處
1. 可以把一些重複的程式碼抽出來，放在 function 中，並且可以在不同的地方呼叫這些 function。
2. 可以把常用的程式碼統整並放在獨立的 source file 中，方便管理。並可以將 function 先行編譯，降低整體編譯時間。
3. 可以獨立測試 function，將問題獨立出來。

## One and the only: `main` function

`main` 的定義
1. `int main () { body }`
2. `int main (int argc, char *argv[]) { body }`

> The main function is called at program startup after initialization of the non-local objects with static storage duration.
> It is the designated entry point to a program that is executed in hosted environment (that is, with an operating system).
> The entry points to freestanding programs (boot loaders, OS kernels, etc) are implementation-defined.

Example:

``` c++
#include <iostream>
#include <sstream>
#include <cstdlib> // EXIT_SUCCESS

int main(int argc, char *argv[])
{
    int a = 0, b = 0, c = 0;

    std::cout << "The program have " << argc << " arguments." << std::endl;
    for (int i = 0; i < argc; i++)
    {
        std::cout << "argv[" << i << "] = " << argv[i] << std::endl;
    }

    std::stringstream char_array_to_int;
    char_array_to_int << argv[1] << ' ' << argv[2];
    char_array_to_int >> a;
    char_array_to_int >> b;
    c = a + b;
    std::cout << "a: " << a << std::endl
              << "b: " << b << std::endl
              << "a + b = " << c << std::endl;
    return EXIT_SUCCESS;
}
```

``` console
$ ./a.out 1 2 3⏎
The program have 4 arguments.
argv[0] = ./a.out
argv[1] = 1
argv[2] = 2
argv[3] = 3
a: 1
b: 2
a + b = 3
```

## Function Definition

``` c++
<type> <name>(<parameter list>)
{
    <body>
}
```

程式 compile 是由上至下，第一個符合的 function 就會被執行。
重複定義的 function 會是錯誤的。

Exception: 同名字但不同參數的 function 會是不同的 function ([Function Overloading](./lecture_10.html#function_overloading))。

Example:

``` c++
#include <iostream>
#include <sstream>
#include <cstdlib> // EXIT_SUCCESS

int add(int a, int b)
{
    return a + b;
}

int main(int argc, char *argv[])
{
    int a = 0, b = 0, c = 0;

    std::cout << "The program have " << argc << " arguments." << std::endl;
    for (int i = 0; i < argc; i++)
    {
        std::cout << "argv[" << i << "] = " << argv[i] << std::endl;
    }

    std::stringstream char_array_to_int;
    char_array_to_int << argv[1] << ' ' << argv[2];
    char_array_to_int >> a;
    char_array_to_int >> b;
    c = add(a, b);
    std::cout << "a: " << a << std::endl
              << "b: " << b << std::endl
              << "a + b = " << c << std::endl;
    return EXIT_SUCCESS;
}
```

### Function Prototype

若要將 function 放在其他位置或是在其他 source file 中，需要寫 function prototype。

``` c++
#include <iostream>
#include <sstream>
#include <cstdlib> // EXIT_SUCCESS

int add(int a, int b); // function prototype

int main(int argc, char *argv[])
{
    int a = 0, b = 0, c = 0;

    std::cout << "The program have " << argc << " arguments." << std::endl;
    for (int i = 0; i < argc; i++)
    {
        std::cout << "argv[" << i << "] = " << argv[i] << std::endl;
    }

    std::stringstream char_array_to_int;
    char_array_to_int << argv[1] << ' ' << argv[2];
    char_array_to_int >> a;
    char_array_to_int >> b;
    c = add(a, b); // function call
    std::cout << "a: " << a << std::endl
              << "b: " << b << std::endl
              << "a + b = " << c << std::endl;
    return EXIT_SUCCESS;
}

int add(int a, int b) // function definition
{
    return a + b;
}
```

## Function Call

如何呼叫 function？
僅需要使用的時候直接寫該 function 的名字，並將要傳入的相對應的變數放到參數 `()` 內。

Example:

``` c++
    add(a, b); // function call, order is important
```

Note: 放參數的順序和數量一定要和 function 定義的順序一樣，數量不同會 compile 錯誤，
順序不同意思不同，而且可能形成 compiler 無法揪錯，直到執行階段才有機會被發掘的重大錯誤。

Wrong example:

``` c++
#include <iostream>
#include <sstream>
#include <cstdlib> // EXIT_SUCCESS

int sub(int a, int b); // function prototype

int main(int argc, char *argv[])
{
    int a = 0, b = 0, c = 0;

    std::cout << "The program have " << argc << " arguments." << std::endl;
    for (int i = 0; i < argc; i++)
    {
        std::cout << "argv[" << i << "] = " << argv[i] << std::endl;
    }

    std::stringstream char_array_to_int;
    char_array_to_int << argv[1] << ' ' << argv[2];
    char_array_to_int >> a;
    char_array_to_int >> b;
    c = sub(a, b); // function call
    std::cout << "a: " << a << std::endl
              << "b: " << b << std::endl
              << "a - b = " << c << std::endl;
    c = sub(b, a); // function call, wrong example
    std::cout << "a: " << a << std::endl
              << "b: " << b << std::endl
              << "a - b = " << c << std::endl;
    return EXIT_SUCCESS;
}

int sub(int a, int b) // function definition
{
    return a - b;
}
```

## Function Parameter

Function 參數可以是任何型態，並且有以下三種方式來傳遞變數：

### Call by Value

Call by Value 參數傳遞的方式是：傳遞變數的值，而非變數本身。
傳遞時會多複製一份變數的值。

Example:

``` c++
#include <iostream>
#include <sstream>
#include <cstdlib> // EXIT_SUCCESS

int sub(int a, int b); // function prototype

int main(int argc, char *argv[])
{
    int a = 0, b = 0, c = 0;

    std::cout << "The program have " << argc << " arguments." << std::endl;
    for (int i = 0; i < argc; i++)
    {
        std::cout << "argv[" << i << "] = " << argv[i] << std::endl;
    }

    std::stringstream char_array_to_int;
    char_array_to_int << argv[1] << ' ' << argv[2];
    char_array_to_int >> a;
    char_array_to_int >> b;
    c = sub(a, b); // function call
    std::cout << "a: " << a << std::endl
              << "b: " << b << std::endl
              << "a - b = " << c << std::endl;
    return EXIT_SUCCESS;
}

int sub(int a, int b) // function definition
{
    a = a - b;
    return a;
}
```

### Call by Reference

Call by Reference 參數傳遞的方式是：傳遞變數本身，可減少傳遞時候所需要的記憶體。

Example:

``` c++
#include <iostream>
#include <sstream>
#include <cstdlib> // EXIT_SUCCESS

void sub(int &c, int &a, int &b); // call by reference

int main(int argc, char *argv[])
{
    int a = 0, b = 0, c = 0;

    std::cout << "The program have " << argc << " arguments." << std::endl;
    for (int i = 0; i < argc; i++)
    {
        std::cout << "argv[" << i << "] = " << argv[i] << std::endl;
    }

    std::stringstream char_array_to_int;
    char_array_to_int << argv[1] << ' ' << argv[2];
    char_array_to_int >> a;
    char_array_to_int >> b;
    sub(c, a, b); // function call
    std::cout << "a: " << a << std::endl
              << "b: " << b << std::endl
              << "a - b = " << c << std::endl;
    return EXIT_SUCCESS;
}

void sub(int &c, int &a, int &b) // call by reference, a and b can be changed
{
    a = a - b; // a in main function is changed
    c = a;
    return; // return value is not used if return type is void
}
```

### `const` Reference

若需要使用 call by reference 參數，且參數值不能被改變，則可以使用 `const` reference 參數。

Example:

``` c++
#include <iostream>
#include <sstream>
#include <cstdlib> // EXIT_SUCCESS

void sub(int &c, const int &a, const int &b); // call by reference

int main(int argc, char *argv[])
{
    int a = 0, b = 0, c = 0;

    std::cout << "The program have " << argc << " arguments." << std::endl;
    for (int i = 0; i < argc; i++)
    {
        std::cout << "argv[" << i << "] = " << argv[i] << std::endl;
    }

    std::stringstream char_array_to_int;
    char_array_to_int << argv[1] << ' ' << argv[2];
    char_array_to_int >> a;
    char_array_to_int >> b;
    sub(c, a, b); // function call
    std::cout << "a: " << a << std::endl
              << "b: " << b << std::endl
              << "a - b = " << c << std::endl;
    return EXIT_SUCCESS;
}

void sub(int &c, const int &a, const int &b)
// call by reference, a and b cannot be changed
{
    a = a - b; // compile error, a cannot be changed
    c = a;
    //c = a - b;
    return; // return value is not used if return type is void
}
```

### Call by Pointer (Optional, Old Style)

Call by Pointer 參數傳遞的方式是：傳遞變數的記憶體位置，與 Call by Reference 相同，但處理起來較為複雜。

Note: 看看就好

Example:

``` c++
#include <iostream>
#include <sstream>
#include <cstdlib> // EXIT_SUCCESS

void sub(int *c, const int *a, const int *b); // call by pointer

int main(int argc, char *argv[])
{
    int a = 0, b = 0, c = 0;

    std::cout << "The program have " << argc << " arguments." << std::endl;
    for (int i = 0; i < argc; i++)
    {
        std::cout << "argv[" << i << "] = " << argv[i] << std::endl;
    }

    std::stringstream char_array_to_int;
    char_array_to_int << argv[1] << ' ' << argv[2];
    char_array_to_int >> a;
    char_array_to_int >> b;
    sub(&c, &a, &b); // function call
    // use &a to get the address of a
    std::cout << "a: " << a << " @ " << &a << std::endl
              << "b: " << b << " @ " << &b << std::endl
              << "a - b = " << c << " @ " << &c << std::endl;
    return EXIT_SUCCESS;
}

void sub(int *c, const int *a, const int *b)
// call by pointer, a and b cannot be changed, c can be changed
{
    std::cout << "a: " << *a << " @ " << a << std::endl
              << "b: " << *b << " @ " << b << std::endl
              << "c: " << *c << " @ " << c << std::endl;
    *a = *a - *b; // compile error, a cannot be changed
    *c = *a;
    //*c = *a - *b;
    return; // return value is not used if return type is void
}
```

## Function Return

Function return 傳回值的方式是：傳回變數本身，任何型態皆可，但只能回傳一個值。
若不需回傳值，則可以使用 `void` 型態，並且不需要寫 `return` 或是 `return;` 即可。
若要回傳多個變數，則會需要使用 call by reference。

``` c++
#include <iostream>
#include <sstream>
#include <cstdlib> // EXIT_SUCCESS

int sub(int &c_complement, const int &a, const int &b); // call by reference

int main(int argc, char *argv[])
{
    int a = 0, b = 0, c = 0, c_complement = 0;

    std::cout << "The program have " << argc << " arguments." << std::endl;
    for (int i = 0; i < argc; i++)
    {
        std::cout << "argv[" << i << "] = " << argv[i] << std::endl;
    }

    std::stringstream char_array_to_int;
    char_array_to_int << argv[1] << ' ' << argv[2];
    char_array_to_int >> a;
    char_array_to_int >> b;
    c = sub(c, a, b); // function call
    std::cout << "a: " << a << std::endl
              << "b: " << b << std::endl
              << "a - b = " << c << std::endl;
    return EXIT_SUCCESS;
}

int sub(int &c_complement, const int &a, const int &b)
// use call by reference to return multiple values
{
    c_complement = b - a;
    return a - b;
}
```

## Function example

``` c++
#include <iostream>
#include <cctype>    // isdigit()
#include <algorithm> // reverse()
#include <vector>    // vector<int>()

using namespace std;

vector<int> parse_big_number(const char input_buffer[])
{
    vector<int> big_number;
    for (int i = 0; input_buffer[i] != '\0'; i++)
    {
        if (!isdigit(input_buffer[i]))
        {
            cout << "Invalid input: " << input_buffer << endl;
            return vector<int>();
        }
        else
        {
            int digit = input_buffer[i] - '0';
            big_number.push_back(digit);
        }
    }

    reverse(big_number.begin(), big_number.end());

    // remove leading zeros
    for (int i = big_number.size() - 1; i >= 1; i--)
    {
        if (big_number[i] == 0)
        {
            big_number.pop_back();
        }
        else
        {
            break;
        }
    }

    return big_number;
}

void print_big_number(const vector<int> &big_number)
{
    for (int i = big_number.size() - 1; i >= 0; i--)
    {
        cout << big_number[i];
    }
    cout << endl;
}

void clean_up_input_buffer(char input_buffer[])
{
    for (int i = 0; input_buffer[i] != '\0'; i++)
    {
        input_buffer[i] = '\0';
    }
}

int main()
{
    char input_buffer[10000 + 1]; // + 1 for the last '\0'
    vector<int> a, b;

    cout << "Input a non-negative integer number (a): ";
    cin >> input_buffer;

    a = parse_big_number(input_buffer);

    clean_up_input_buffer(input_buffer);

    cout << "(a) = ";
    print_big_number(a);

    cout << "Input a non-negative integer number (b): ";
    cin >> input_buffer;

    b = parse_big_number(input_buffer);

    clean_up_input_buffer(input_buffer);

    cout << "(b) = ";
    print_big_number(b);

    return 0;
}
```
