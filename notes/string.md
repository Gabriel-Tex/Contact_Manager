### BIBLIOGRAFIA: https://www.learncpp.com

### Introducing std::string

```
#include <string> // allows use of std::string

int main()
{
    std::string name {}; // empty string

    return 0;
}

```

O std::string é uma classe (não é tipo primitivo). 
Ela encapsula toda a complexidade de lidar com strings e memória.

O {} inicializa a string vazia. 

Diferente de C-style strings, é possível tratar string 
como um tipo “normal”


```
#include <string>

int main()
{
    std::string name { "Alex" }; // initialize name with string literal "Alex"
    name = "John";               // change name to "John"

    return 0;
}
```

### std::string e tamanhos dinâmicos

Internamente, std::string usa alocação dinâmica de memória. 
Isso significa:

Se precisar crescer → aloca mais memória
Se diminuir → pode reaproveitar ou ajustar

Isso torna o uso muito mais simples, mas tem custo: 
é mais lento que tipos simples, justamente por causa dessas alocações.

### String input com std::cin (problema clássico)

#### Problema

std::cin >> name; // this won't work as expected since std::cin breaks on whitespace

Exemplo:

Enter your full name: John Doe

Resultado:

name = "John"
" Doe" fica no buffer

Isso acontece porque >> para em whitespace.

#### std::getline() para ler linhas completas

getline() lê toda a string até `\n`

```
std::getline(std::cin >> std::ws, name); 
// read a full line of text into name
```

#### O que é std::ws?

std::ws é um manipulador de entrada do C++ que serve para ignorar (descartar) espaços em branco do stream, limpando whitespace antes da leitura.

Ele remove caracteres como:

* ' ' (espaço)
* '\n' (quebra de linha)
* '\t' (tab)

até encontrar o primeiro caractere não branco.

Ex.: 

```
std::getline(std::cin >> std::ws, name);
```

### Tamanho da string

```
std::string name{ "Alex" };
std::cout << name << " has " << name.length() << " characters\n";
```

Obs.: length() retorna size_t (unsigned). Converter evita warnings.

`int length { static_cast<int>(name.length()) };`

C++20: std::ssize retorna valor com sinal (signed). Mais seguro para cálculos com int.

`std::cout << std::ssize(name);`

### Custo de std::string

`std::string name { "Alex" };`

No exemplo acima, "Alex" é uma C-style string (const char*)

Assim, o std::string:
* calcula o tamanho (strlen)
* aloca memória (geralmente no heap)
* copia os caracteres

Entretando, para strings pequenas, como "Alex", esse processo é otimizado.

O real problema acontece ao passar uma string por valor:

```
void foo(std::string s);

foo(name);
```

Assim, uma nova string é criada e o conteúdo é copiado. 

Uma melhor alternativa nesse caso é utilizar referência:

* Identificador diferente para uma mesma variável, evitando cópia (referencia o dado original).

``` 
void foo(const std::string& s);
```

Outra alternativa é utilizar `string_view`

* string_view aponta direto para a sequência de caracteres, não armazenando a string de fato.   

```
void foo(std::string_view s);
```

### Introduction to std::string_view

#### Problema

```
#include <iostream>
#include <string>

void printString(std::string str) // str makes a copy of its initializer
{
    std::cout << str << '\n';
}

int main()
{
    std::string s{ "Hello, world!" }; // s makes a copy of its initializer
    printString(s);

    return 0;
}
```

Aqui há duas cópias:

"Hello, world!" → s
s → str

Ou seja, a mesma string foi copiada duas vezes só pra imprimir.

Esse é o problema real que o std::string_view resolve.

```
#include <iostream>
#include <string_view> // C++17

// str provides read-only access to whatever argument is passed in
void printSV(std::string_view str) // now a std::string_view
{
    std::cout << str << '\n';
}

int main()
{
    std::string_view s{ "Hello, world!" }; // now a std::string_view
    printSV(s);

    return 0;
}
```

std::string_view não copia a string
Ele apenas “aponta” para ela

Ele funciona como uma janela (view) para uma string existente.

#### Boa prática: 

Sempre que você não precisa modificar a string, use std::string_view, especialmente em parâmetros de função. Isso evita cópias desnecessárias e melhora performance.

#### Flexibilidade do string_view

```
#include <iostream>
#include <string>
#include <string_view>

int main()
{
    std::string_view s1 { "Hello, world!" }; // initialize with C-style string literal
    std::cout << s1 << '\n';

    std::string s{ "Hello, world!" };
    std::string_view s2 { s };  // initialize with std::string
    std::cout << s2 << '\n';

    std::string_view s3 { s2 }; // initialize with std::string_view
    std::cout << s3 << '\n';

    return 0;
}
```

#### Assignment não modifica a string original

```
#include <iostream>
#include <string>
#include <string_view>

int main()
{
    std::string name { "Alex" };
    std::string_view sv { name }; // sv is now viewing name
    std::cout << sv << '\n'; // prints Alex

    sv = "John"; // sv is now viewing "John" (does not change name)
    std::cout << sv << '\n'; // prints John

    std::cout << name << '\n'; // prints Alex

    return 0;
}
```

#### constexpr std::string_view

```
#include <iostream>
#include <string_view>

int main()
{
    constexpr std::string_view s{ "Hello, world!" }; // s is a string symbolic constant
    std::cout << s << '\n';

    return 0;
}
```

Diferente de std::string, isso funciona perfeitamente.

Isso porque string_view não aloca memória, então pode existir em tempo de compilação.

Isso faz dele a melhor escolha para constantes de string.