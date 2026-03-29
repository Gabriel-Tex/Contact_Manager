# BIBLIOGRAFIA: https://www.learncpp.com

## Friend non-member functions

Quando você declara algo como friend dentro de uma classe, está dizendo:

“Essa função (ou classe) pode acessar meus membros privados como se fosse parte de mim.”

Ou seja, você está abrindo uma exceção controlada no sistema de acesso.

Ex.:

```
class Accumulator
{
private:
    int m_value { 0 };

public:
    void add(int value) { m_value += value; }

    friend void print(const Accumulator& accumulator);
};
```

Aqui, a função print não pertence à classe. Mesmo assim, ela pode acessar m_value diretamente

```
void print(const Accumulator& accumulator)
{
    std::cout << accumulator.m_value;
}
```

Normalmente isso daria erro, porque m_value é privado. Mas como print é amiga, ela tem acesso total.

Além disso, como print não é membro, ela não tem this. Então você precisa passar o objeto explicitamente.

## Friend classes

Não só funções podem ser friend, mas classes inteiras também, isto é, uma classe pode acessar diretamente os membros privados de outra

```
class Storage
{
private:
    int m_nValue {};
    double m_dValue {};

public:
    Storage(int nValue, double dValue)
       : m_nValue { nValue }, m_dValue { dValue }
    { }

    friend class Display;
};
```

toda a classe Display pode acessar os membros privados de Storage

```
void displayStorage(const Storage& storage)
{
    if (m_displayIntFirst)
        std::cout << storage.m_nValue << ' ' << storage.m_dValue << '\n';
    else
        std::cout << storage.m_dValue << ' ' << storage.m_nValue << '\n';
}
```
