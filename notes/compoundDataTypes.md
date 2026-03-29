# BIBLIOGRAFIA: https://www.learncpp.com

## Categorias de valor (lvalues e rvalues)

Um lvalue é algo que tem identidade — ou seja, você consegue apontar pra ele, ele tem um “lugar” na memória.

Enquanto um rvalue é algo temporário — um valor puro, sem identidade.

## Lvalue references

Em C++, uma referência é basicamente um apelido (alias) para um objeto que já existe. Isso significa que, depois que você cria uma referência, qualquer operação feita nela afeta diretamente o objeto original.

Obs.: uma referência não é uma cópia, não é um ponteiro (apesar de parecer), e não cria um novo objeto. Ela apenas permite acessar o mesmo objeto por outro nome. 

Assim, uma referência é essencialmente o próprio objeto ao qual ela se refere.

### Tipos de lvalue reference

```
int        // tipo normal
int&       // referência para int
double&    // referência para double
const int& // referência para int constante
```

### Variáveis de referência

Ex.:
```
#include <iostream>

int main()
{
    int x { 5 };    // x é uma variável inteira normal
    int& ref { x }; // ref é uma referência (apelido) para x

    std::cout << x << '\n';   // imprime o valor de x (5)
    std::cout << ref << '\n'; // imprime o valor de x via ref (5)

    return 0;
}
```

### Modificando valores através da referência

```
#include <iostream>

int main()
{
    int x { 5 };     // variável normal
    int& ref { x };  // ref é um apelido para x

    std::cout << x << ref << '\n'; // imprime 55

    x = 6; // x agora vale 6

    std::cout << x << ref << '\n'; // imprime 66

    ref = 7; // altera x através da referência

    std::cout << x << ref << '\n'; // imprime 77

    return 0;
}
```

### Inicialização de referências

Referências devem obrigatoriamente ser inicializadas

```
int main()
{
    int& invalidRef; // erro: referência precisa ser inicializada

    int x { 5 };
    int& ref { x }; // correto

    return 0;
}
```

Obs.: Uma lvalue reference não-const só pode ser ligada a um lvalue que seja modificado

```
int main()
{
    int x { 5 };
    int& ref { x };         // ok

    const int y { 5 };
    int& invalidRef { y };  // erro: y é const

    int& invalidRef2 { 0 }; // erro: 0 é rvalue

    return 0;
}
```

### Conversões e referências

Mesmo que exista conversão entre tipos (double → int), isso não funciona neste caso, pois conversões geram rvalues, e referência não-const não pode apontar para rvalues

```
int main()
{
    int x { 5 };
    int& ref { x };        // ok

    double d { 6.0 };
    int& invalidRef { d }; // erro

    double& invalidRef2 { x }; // erro

    return 0;
}
```

### Referências não podem mudar de alvo

```
#include <iostream>

int main()
{
    int x { 5 };
    int y { 6 };

    int& ref { x }; // ref aponta para x

    ref = y; // NÃO muda a referência!

    std::cout << x << '\n'; // imprime 6

    return 0;
}
```

ref continua apontando para x
ref = y vira x = y

### Dangling references

Se o objeto morrer antes da referência, a referência passa a apontar para algo que não existe mais.

## Referências lvalue para const

```
int main()
{
    const int x { 5 };    // x é um lvalue não-modificável
    const int& ref { x }; // ok: ref é uma referência lvalue para const

    return 0;
}
```

### Ligando uma referência const a um lvalue modificável

```
#include <iostream>

int main()
{
    int x { 5 };          // x é um lvalue modificável
    const int& ref { x }; // ok: podemos ligar a um lvalue normal

    std::cout << ref << '\n'; // ok: leitura permitida
    ref = 7;                  // erro: não pode modificar via ref

    x = 6;                // ok: ainda podemos modificar x diretamente

    return 0;
}
```

### Ligando uma referência const a um rvalue

O compilador cria um objeto temporário para armazenar o valor, e a referência passa a apontar para esse objeto.

```
#include <iostream>

int main()
{
    const int& ref { 5 }; // ok: 5 é um rvalue

    std::cout << ref << '\n'; // imprime 5

    return 0;
}
```

### Ligação com tipos diferentes (conversão implícita)

Referências const podem se ligar a valores de outro tipo, desde que exista conversão implícita.

```
#include <iostream>

int main()
{
    // caso 1
    const double& r1 { 5 };  // cria um double temporário com valor 5

    std::cout << r1 << '\n'; // imprime 5

    // caso 2
    char c { 'a' };
    const int& r2 { c };     // cria um int temporário com valor 'a'

    std::cout << r2 << '\n'; // imprime 97

    return 0;
}
```

A referência não está ligada ao objeto original, mas sim a um temporário convertido, quebrando aquela ideia simplificada de que “referência é só um apelido”.

## Passagem por referência lvalue

```
#include <iostream>
#include <string>

void printValue(std::string& y) // agora é referência
{
    std::cout << y << '\n';
} // y é destruído aqui

int main()
{
    std::string x { "Hello, world!" };

    printValue(x); // x é passado por referência (sem cópia)

    return 0;
}
```

### Modificando o argumento

Sem referência:

```
#include <iostream>

void addOne(int y) // cópia
{
    ++y; // modifica a cópia
}

int main()
{
    int x { 5 };

    std::cout << "valor = " << x << '\n';

    addOne(x);

    std::cout << "valor = " << x << '\n'; // não mudou

    return 0;
}
```

Com referência:

```
#include <iostream>

void addOne(int& y) // referência
{
    ++y; // modifica o objeto real
}

int main()
{
    int x { 5 };

    std::cout << "valor = " << x << '\n';

    addOne(x);

    std::cout << "valor = " << x << '\n'; // mudou!

    return 0;
}
```

## Pass by const lvalue reference

Diferente de uma referência comum (int&), uma referência const (const int&) é muito mais flexível. Ela pode se ligar (bind) a praticamente qualquer tipo de argumento: lvalues modificáveis, lvalues const e até rvalues.

```
#include <iostream>

void printRef(const int& y) // y é uma referência const
{
    std::cout << y << '\n';
}

int main()
{
    int x { 5 };
    printRef(x);   // ok: x é um lvalue modificável, y referencia x

    const int z { 5 };
    printRef(z);   // ok: z é um lvalue não modificável, y referencia z

    printRef(5);   // ok: 5 é um rvalue, y referencia um temporário

    return 0;
}
```

### Conversões e objetos temporários

```
#include <iostream>

void printVal(double d)
{
    std::cout << d << '\n';
}

void printRef(const double& d)
{
    std::cout << d << '\n';
}

int main()
{
    printVal(5); // 5 é convertido para double e copiado para d
    printRef(5); // 5 é convertido para double e referenciado por d

    return 0;
}
```

## Introdução a ponteiros

### O operador de endereço (&)

```
#include <iostream>

int main()
{
    int x{ 5 };
    std::cout << x << '\n';  // imprime o valor de x
    std::cout << &x << '\n'; // imprime o endereço de x

    return 0;
}
```

### O operador de dereferência (*)

Saber o endereço sozinho não é tão útil. O mais importante é acessar o valor naquele endereço, e para isso usamos o operador *.

```
#include <iostream>

int main()
{
    int x{ 5 };
    std::cout << x << '\n';  // imprime valor
    std::cout << &x << '\n'; // imprime endereço

    std::cout << *(&x) << '\n'; // acessa o valor no endereço de x

    return 0;
}
```

&x → pega o endereço
*(&x) → volta para o valor

Ou seja, eles são operações opostas.

Dado um endereço, * permite acessar o valor armazenado nele.

### O que é um ponteiro?

Um ponteiro é uma variável que armazena um endereço de memória.

```
int main()
{
    int x { 5 };    // variável normal
    int& ref { x }; // referência

    int* ptr;       // ponteiro para int

    return 0;
}
```

int* é o tipo “ponteiro para int”
ptr vai guardar um endereço de um int

### Inicialização de ponteiros

Ponteiros devem sempre ser inicializados. Caso contrário, eles contêm lixo (wild pointer).

```
int main()
{
    int x{ 5 };

    int* ptr;        // ponteiro não inicializado (perigoso)
    int* ptr2{};     // ponteiro nulo
    int* ptr3{ &x }; // ponteiro apontando para x

    return 0;
}
```

### Usando ponteiros

Depois de armazenar um endereço, podemos acessar o valor usando *:

```
#include <iostream>

int main()
{
    int x{ 5 };
    std::cout << x << '\n'; // imprime x

    int* ptr{ &x }; 
    std::cout << *ptr << '\n'; // imprime o valor apontado (x)

    return 0;
}
```

### Tipos devem ser compatíveis

```
int main()
{
    int i{ 5 };
    double d{ 7.0 };

    int* iPtr{ &i };     // ok
    int* iPtr2 { &d };   // erro
    double* dPtr{ &d };  // ok
    double* dPtr2{ &i }; // erro

    return 0;
}
```

Um ponteiro para int só pode apontar para int.

### Atribuição com ponteiros

Ponteiros podem ser usados de duas formas diferentes: mudar o que apontam ou mudar o valor apontado.

#### Mudando o alvo do ponteiro

```
#include <iostream>

int main()
{
    int x{ 5 };
    int* ptr{ &x };

    std::cout << *ptr << '\n'; // 5

    int y{ 6 };
    ptr = &y; // agora aponta para y

    std::cout << *ptr << '\n'; // 6

    return 0;
}
```

#### Mudando o valor apontado

```
#include <iostream>

int main()
{
    int x{ 5 };
    int* ptr{ &x };

    std::cout << x << '\n';
    std::cout << *ptr << '\n';

    *ptr = 6; // modifica x através do ponteiro

    std::cout << x << '\n';
    std::cout << *ptr << '\n';

    return 0;
}
```

* ptr → acessa o endereço
* *ptr → acessa o valor

* Alterar ptr muda para onde ele aponta
* Alterar *ptr muda o valor do objeto apontado

### Ponteiros vs referências

```
#include <iostream>

int main()
{
    int x{ 5 };
    int& ref { x };  
    int* ptr { &x };

    std::cout << x;
    std::cout << ref;  
    std::cout << *ptr << '\n';

    ref = 6;
    std::cout << x;
    std::cout << ref;  
    std::cout << *ptr << '\n';

    *ptr = 7;
    std::cout << x;
    std::cout << ref;  
    std::cout << *ptr << '\n';

    return 0;
}
```

Saída:
555
666
777

* referência → automática (não precisa de & nem *)
* ponteiro → explícito (precisa de & e *)

#### Diferenças importantes

* referências devem ser inicializadas, ponteiros não (mas devem)
* referências não podem mudar de alvo, ponteiros podem
* referências não são objetos, ponteiros são
* ponteiros podem ser nulos, referências não
* ponteiros são mais perigosos

### dangling pointer

Um ponteiro pode ficar inválido se o objeto apontado for destruído.

## Ponteiros nulos

Além de armazenar um endereço válido, um ponteiro também pode armazenar um valor nulo (null). Esse valor especial indica que o ponteiro não está apontando para nada.

```
int main()
{
    int* ptr {}; // ptr agora é um ponteiro nulo, não aponta para nenhum endereço

    return 0;
}
```

### O keyword nullptr

representa explicitamente um ponteiro nulo.

```
int main()
{
    int* ptr { nullptr }; // inicializa explicitamente como ponteiro nulo

    int value { 5 };
    int* ptr2 { &value }; // ponteiro válido
    ptr2 = nullptr; // agora ptr2 virou ponteiro nulo

    return 0;
}
```

### Evitando ponteiros dangling com nullptr

A estratégia recomendada é simples:

Um ponteiro deve sempre estar em um de dois estados:

* apontando para um objeto válido
* ou sendo nullptr

### Referências vs ponteiros pt. 2

Referências são mais seguras:
* não podem ser nulas
* não podem mudar o objeto referenciado
* Ponteiros são mais flexíveis:
* podem ser nulos
* podem mudar o que apontam

Mas essa flexibilidade traz risco. Por isso:

Prefira referências sempre que possível, e use ponteiros apenas quando realmente precisar.

## Ponteiros e const

### Ponteiro para valor constante (pointer to const)

```
int main()
{
    const int x{ 5 };
    const int* ptr { &x }; // ptr aponta para um "const int"

    *ptr = 6; // erro: não podemos modificar o valor

    return 0;
}
```

* O ponteiro pode apontar para coisas diferentes
* mas não pode modificar o valor apontado

### Ponteiro constante (const pointer)

Agora mudamos o foco: em vez de proteger o valor, vamos proteger o próprio ponteiro.

```
int main()
{
    int x{ 5 };
    int* const ptr { &x }; // ponteiro constante

    return 0;
}
```

* ptr não pode mudar de endereço
* mas pode modificar o valor apontado

### Ponteiro constante para valor constante

```
int main()
{
    int value { 5 };
    const int* const ptr { &value };

    return 0;
}
```

Esse ponteiro é basicamente somente leitura total.

## (conteúdos pendentes)

## std::optional

### Problema

Ex.:

```
int dividir(int x, int y) {
    return x / y;
}
```

Se y==0, não dá pra retornar um valor válido, então é preciso indicar erro.

#### Solução moderna

```
#include <iostream>
#include <optional>

// Nossa função agora retorna opcionalmente um int
std::optional<int> doIntDivision(int x, int y)
{
    if (y == 0)
        return {}; // ou std::nullopt (sem valor)

    return x / y; // retorna valor normalmente
}
```

#### Como usar

```
int main()
{
    std::optional<int> result1 { doIntDivision(20, 5) };

    if (result1) // tem valor?
        std::cout << "Result 1: " << *result1 << '\n';
    else
        std::cout << "Result 1: failed\n";

    std::optional<int> result2 { doIntDivision(5, 0) };

    if (result2)
        std::cout << "Result 2: " << *result2 << '\n';
    else
        std::cout << "Result 2: failed\n";

    return 0;
}
```

#### De forma geral 

```
std::optional<int>
    ├── tem valor → guarda um int
    └── não tem → vazio
```

### Criando opcionais

```
std::optional<int> o1 { 5 };            // com valor
std::optional<int> o2 {};               // sem valor
std::optional<int> o3 { std::nullopt }; // sem valor
```

### Verificando se há valor

```
if (o1.has_value())
if (o2) // conversão implícita para bool
```

### Acessando valor

```
std::cout << *o1;
std::cout << o2.value();
std::cout << o3.value_or(42);
```

* *o1 → direto (UB se vazio)
* value() → lança exceção se vazio
* value_or(42) → usa valor padrão

### Uso como parâmetro opcional

```
void printIDNumber(std::optional<const int> id = std::nullopt)
{
    if (id)
        std::cout << "Your ID number is " << *id << ".\n";
    else
        std::cout << "Your ID number is not known.\n";
}
```

```
printIDNumber();      // sem valor
printIDNumber(userid); 
printIDNumber(62);    // aceita rvalue
```

### Limitação importante: 

std::optional faz cópia.