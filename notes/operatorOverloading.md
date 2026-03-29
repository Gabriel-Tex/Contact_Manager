# BIBLIOGRAFIA: https://www.learncpp.com

## Introduction to operator overloading

Em C++, operadores também são funções. Isso significa que podemos aplicar o mesmo conceito de sobrecarga (overloading) aos operadores.

```
x + y

é interpretado como

operator+(x, y)
```

## Overloading the arithmetic operators using friend functions

```
#include <iostream>

class Cents
{
private:
	int m_cents {};

public:
	Cents(int cents) : m_cents{ cents } { }

	// add Cents + Cents using a friend function
	friend Cents operator+(const Cents& c1, const Cents& c2);

	int getCents() const { return m_cents; }
};

// note: this function is not a member function!
Cents operator+(const Cents& c1, const Cents& c2)
{
	// use the Cents constructor and operator+(int, int)
	// we can access m_cents directly because this is a friend function
	return c1.m_cents + c2.m_cents;
}

int main()
{
	Cents cents1{ 6 };
	Cents cents2{ 8 };
	Cents centsSum{ cents1 + cents2 };
	std::cout << "I have " << centsSum.getCents() << " cents.\n";

	return 0;
}
```

A função operator+ recebe dois objetos Cents e retorna um novo objeto Cents com o resultado da soma.

Como a função foi declarada como friend, ela tem acesso direto ao membro privado m_cents, o que evita a necessidade de getters internos.

## Overloading operators using normal functions

Quando usar função normal vs friend

A lógica aqui é bem direta, mas extremamente importante

Se você consegue implementar o operador usando apenas funções públicas (como getCents()), então não há motivo para usar friend.

Isso torna o código mais seguro, porque menos funções têm acesso direto aos detalhes internos da classe.

Por outro lado, se não houver uma forma eficiente ou natural de acessar os dados via interface pública, então friend passa a fazer sentido.

### Best practice 

Prefira funções normais em vez de friend sempre que possível, desde que não precise criar funções extras só para isso.

Ou seja, não vale a pena “forçar” encapsulamento criando getters só para evitar friend. Mas se a interface pública já resolve o problema, use ela.

## Overloading the I/O operators

Ex.:

```
class Point
{
private:
    double m_x{};
    double m_y{};
    double m_z{};

public:
    Point(double x=0.0, double y=0.0, double z=0.0)
      : m_x{x}, m_y{y}, m_z{z}
    {
    }

    double getX() const { return m_x; }
    double getY() const { return m_y; }
    double getZ() const { return m_z; }

    void print() const
    {
        std::cout << "Point(" << m_x << ", " << m_y << ", " << m_z << ')';
    }
};
```

Aqui, existe um problema importante: a função print criada retorna void, então não pode ser usada no meio de uma expressão com <<. Isso quebra a fluidez do código.

Para contornar esse problema, precisamos sobrecarregar o operador <<.

### Overloading operator<<

O operador << é um operador binário. No caso de `std::cout << point`, temos:

operando da esquerda: std::cout (tipo std::ostream)
operando da direita: Point

Portanto, a função deve receber esses dois parâmetros:

```
friend std::ostream& operator<< (std::ostream& out, const Point& point);
```

Daí, temos

```
#include <iostream>

class Point
{
private:
    double m_x{};
    double m_y{};
    double m_z{};

public:
    Point(double x=0.0, double y=0.0, double z=0.0)
      : m_x{x}, m_y{y}, m_z{z}
    {
    }

    friend std::ostream& operator<< (std::ostream& out, const Point& point);
};

std::ostream& operator<< (std::ostream& out, const Point& point)
{
    // acesso direto porque é friend
    out << "Point(" << point.m_x << ", " << point.m_y << ", " << point.m_z << ')';

    return out; // fundamental para permitir encadeamento
}
```

```
int main()
{
    const Point point1 { 2.0, 3.0, 4.0 };

    std::cout << point1 << '\n';

    return 0;
}
```
