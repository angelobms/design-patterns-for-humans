![Design Patterns For Humans](https://github.com/khaosdoctor/design-patterns-for-humans/blob/master/logo.png?raw=true)

***
<p align="center">
🎉 Um guia ultra simplificado para padrões de projeto! 🎉
</p>
<p align="center">
Um tópico que pode confundir a mente de muita gente. Aqui eu tento fazer ele grudar na sua mente (e talvez na minha), explicando da forma <i>mais simples</i> possível.
</p>
***

❗ Notas de tradução
=================

- Alguns trechos de código foram traduzidos para que o entendimento seja melhor de acordo com o contexto inserido
- Algumas palavras são melhor definidas em sua língua original, portanto tais palavras **não** foram traduzidas. As mesmas terão um link de referência após para melhor entendimento
- O texto possui algumas alterações de sintaxe e estrutura para facilitar a tradução.

🚀 Introdução
=================

Padrões de projeto são soluções para problemas recorrentes. **Guias de como atacar certos problemas**. Eles não são classes, pacotes ou bibliotecas que você pode plugar na sua aplicação e esperar a mágica acontecer. Ao invés disso, eles são guias de como atacar certos problemas em situações específicas.

> Padrões de projeto são soluções para problemas recorrentes. Guias de como atacar certos problemas.

A Wikipédia os descreve como: 

> Em engenharia de software, um padrão de projeto de software é uma solução geral e reutilizável para um problema que costuma ser recorrente dentro de um contexto de design de software. Não é uma solução final que pode ser transformada diretamente em códigos fonte ou de máquina. É uma descrição, um molde de como resolver um problema que pode ser utilizado em diversas situações.

⚠️ Tenha cuidado!
-----------------
- Padrões de projeto **não** são uma bala de prata para todos os seus problemas.
- Não tente forçar o uso deles; coisas ruins acontecem quando esta regra é quebrada. Tenha em mente que padrões de projeto so soluções **para** problemas, não soluções **procurando** problemas; então não tente encaixar um padrão em todo lugar.
- Se usados de forma e maneira corretas, eles podem ser grandes salvadores; ou também podem resultar em uma bagunça absurda de código.

> Note também que os exemplos de código abaixo estão em PHP-7, no entanto isto não deve te atrapalhar porque os conceitos são os mesmos em qualquer lugar. Além do mais, **O suporte para outras linguagens no guia está em desenvolvimento**.

Tipos de padrões de projeto
-----------------

* [Criacional](#criacionais)
* [Estrutural](#estruturais)
* [Comportamental](#comportamentais)

<a name="criacionais"></a>Padrões de projeto criacionais
=================

Em palavras simples::
> Padrões criacionais são focados em como instanciar um objeto ou um grupo de objetos relacionados.

Wikipedia diz:
> Em engenharia de software, padrões de projeto criacionais são padrões de projeto que tratam dos mecanismos de criação de objetos, tentando criar objetos de forma adequada para a situação. A forma básica de criação de um objeto pode resultar em problemas de design ou adicionar complexidade ao mesmo. Estes padrões de projeto resolvem este problema controlando de alguma forma o modo como estes objetos são criados.

 * [Fábrica Simples](#fabrica-simples)
 * [Método Fábrica](#fabrica)
 * [Fábrica Abstrata](#fabrica-abstrata)
 * [Construtor](#construtor)
 * [Protótipo](#prototipo)
 * [Singleton](#singleton)

<a name="fabrica-simples"></a>🏠 Fábrica Simples
--------------
Exemplo no mundo real
> Considere isso, você está construindo uma casa e você precisa de portas. Seria uma bagunça se, toda vez que você precisasse de uma porta, você tivesse que colocar as suas roupas de marceneiro e começasse a esculpir uma porta na sua casa. Ao invés disso, você pode obte-la de uma fábrica.

Em palavras simples:
> A fábrica simples simplesmente gera uma instancia para o cliente sem expor nenhuma lógica de instanciação para o cliente.

Wikipedia diz
> Em programação orientada a objetos (POO), uma fábrica é um objeto que cria outros objetos - Formalmente, uma fábrica é uma função ou método que retorna objetos de um protótipo variável ou uma classe de alguma chamada de método, assumindo ser "new" <sup>[Keyword de programação]</sup>.

**Exemplo programático**

Primeiramente temos nossa interface "porta" e a implementação:
```php
interface Porta
{
    public function obterComprimento(): float;
    public function obterAltura(): float;
}

class PortaMadeira implements Porta
{
    protected $comprimento;
    protected $altura;

    public function __construct(float $comprimento, float $altura)
    {
        $this->comprimento = $comprimento;
        $this->altura = $altura;
    }

    public function obterComprimento(): float
    {
        return $this->comprimento;
    }

    public function obterAltura(): float
    {
        return $this->altura;
    }
}
```

Então temos a nossa fábrica de portas que cria uma porta e retorna ela para nós:
```php
class FabricaPorta
{
    public static function fazerPorta($comprimento, $altura): Porta
    {
        return new PortaMadeira($comprimento, $altura);
    }
}
```
Então pode ser usada como:
```php
$porta = FabricaPorta::fazerPorta(100, 200);
echo 'Comprimento: ' . $porta->obterComprimento();
echo 'Altura: ' . $porta->obterAltura();
```

**Quando usar?**

Quando criar um objeto não envolve apenas algumas alocações de variáveis, mas sim alguma lógica mais complexa. Faz mais sentido colocar toda essa lógica em uma fábrica dedicada ao invés de repetir o mesmo código em todos os lugares.

<a name="fabrica"></a>🏭 Método Fábrica
--------------

Exemplo do mundo real:
> Considere o caso de contratar um gerente. É impossível uma única pessoa entrevistar para cada posição. Então, baseado na vaga, ela tem que escolher e delegar os passos da entrevista para diferentes pessoas.

Em palavras simples:
> Provê um modo de delegar a lógica de instanciação para as classes filhas.

Wikipédia diz:
> Em programação baseada em classes, o padrão método fábrica é um padrão criacional que usa métodos-fábrica para lidar com o problema de criar objetos sem ter de especificar a exata classe do objeto que será criado. Isto é feito criando objetos através de chamadas para um método fábrica - que esteja especificado em uma interface e implementado pelas classes filhas, ou implementado na classe base e opcionalmente sobrescrito pelas suas classes derivadas - ao invés de chamar um construtor.

 **Exemplo programático**

Vamos pegar nosso exemplo de contratação de um gerente acima. Primeiramente temos um entrevistador, que é uma interface, e algumas implementações para ele:

```php
interface Entrevistador
{
    public function fazerPerguntas();
}

class Desenvolvedor implements Entrevistador
{
    public function fazerPerguntas()
    {
        echo 'Perguntando sobre padrões de projeto!';
    }
}

class ExecutivoComunitario implements Entrevistador
{
    public function fazerPerguntas()
    {
        echo 'Perguntando sobre comunidades';
    }
}
```

Agora vamos criar nosso `GerenteDeContratacao`

```php
abstract class GerenteDeContratacao
{

    // Método fábrica
    abstract public function criarEntrevistador(): Entrevistador;

    public function fazerEntrevista()
    {
        $entrevistador = $this->criarEntrevistador();
        $entrevistador->fazerPerguntas();
    }
}

```
Agora, qualquer classe filha vai poder estender esta classe e prover o entrevistador necessário.
```php
class GerenteDeDesenvolvimento extends GerenteDeContratacao
{
    public function criarEntrevistador(): Entrevistador
    {
        return new Desenvolvedor();
    }
}

class GerenteDeMarketing extends GerenteDeContratacao
{
    public function criarEntrevistador(): Entrevistador
    {
        return new ExecutivoComunitario();
    }
}
```
Então podemos usar como:

```php
$devManager = new GerenteDeDesenvolvimento();
$devManager->fazerEntrevista(); // Saída: Perguntando sobre padrões de projeto!

$marketingManager = new GerenteDeMarketing();
$marketingManager->fazerEntrevista(); // Saída: Perguntando sobre comunidade.
```

**Quando usar?**

Útil quando existe um processamento genérico em uma classe, mas a subclasse necessária é decidida dinamicamente em tempo de execução. Ou, em outras palavras, quando o cliente não sabe exatamente qual subclasse ele pode precisar.

<a name="fabrica-abstrata"></a>🔨 Fábrica Abstrata
----------------

Exemplo do mundo real:
> Estendendo nosso examplo com a porta na Fábrica Simples. Baseado em suas necessidades você pode precisar de uma porta de madeira de uma loja de portas de madeira, uma porta de ferro de uma loja de portas de ferro ou uma porta de plástico da loja relevante. Além disso você vai precisar de um cara com diferentes especialidades para encaxar essa porta no lugar, por exemplo, uma porta de madeira vai precisar de um marceneiro, enquanto uma porta de ferro vai precisar de um soldador. Como você pode ver, existe uma dependência entre as portas agora, a porta de madeira precisa de um amrceneiro, a de ferro de um soldador e etc.

Em palavras simples:
> Uma fábrica de fábricas; uma fábrica que agrupa fábricas individuais, mas dependentes, juntas sem especificar suas classes concretas.

Wikipédia diz:
> O padrão de fábrica abstrata provê uma maneira de encapsular um grupo de fábricas individuais que tem um tema comum, sem especificar suas classes concretas.

**Exemplo programático**

Traduzindo nosso exemplo com a porta acima. Vamos, primeiramente, criar nossa interface `Porta`, e criar uma implementação dela.

```php
interface Porta
{
    public function obterDescricao();
}

class PortaMadeira implements Door
{
    public function obterDescricao()
    {
        echo 'Eu sou uma porta de madeira';
    }
}

class PortaFerro implements Porta
{
    public function obterDescricao()
    {
        echo 'Eu sou uma porta de ferro';
    }
}
```
Agora temos nossos experts em encaixe:

```php
interface ExpertEncaixePorta
{
    public function obterDescricao();
}

class Soldador implements ExpertEncaixePorta
{
    public function obterDescricao()
    {
        echo 'Eu só instalo portas de ferro';
    }
}

class Marceneiro implements ExpertEncaixePorta
{
    public function obterDescricao()
    {
        echo 'Eu só instalo portas de madeira';
    }
}
```
Agora, nossa fábrica abstrata vai permitir que façamos uma familia de objetos relacionados, por exemplo, uma fábrica de portas de madeira vai criar uma porta de madeira e também um marceneiro, assim como a fábrica de portas de ferro vai criar uma porta de ferro e um soldador.

```php
interface FabricaPortas
{
    public function fazerPorta(): Door;
    public function fazerExpertInstalacao(): ExpertEncaixePorta;
}

// Fábrica de madeira para retornar uma porta de madeira e um marceneiro
class FabricaPortasMadeira implements FabricaPortas
{
    public function fazerPorta(): Porta
    {
        return new PortaMadeira();
    }

    public function fazerExpertInstalacao(): ExpertEncaixePorta
    {
        return new Marceneiro();
    }
}

// Uma fábrica de portas de ferro para retornar os objetos relevantes à portas de ferro
class FabricaPortasFerro implements FabricaPortas
{
    public function fazerPorta(): Porta
    {
        return new PortaFerro();
    }

    public function fazerExpertInstalacao(): ExpertEncaixePorta
    {
        return new Soldador();
    }
}
```
E podemos usar assim:
```php
$fabricaMadeira = new FabricaPortasMadeira();

$porta = $fabricaMadeira->fazerPorta();
$expert = $fabricaMadeira->fazerExpertInstalacao();

$porta->obterDescricao();  // Saída: Eu sou uma porta de madeira
$expert->obterDescricao(); // Saída: Eu só instalo portas de madeira

// O mesmo para a porta de ferro
$fabricaFerro = new FabricaPortasFerro();

$porta = $fabricaFerro->fazerPorta();
$expert = $fabricaFerro->fazerExpertInstalacao();

$porta->obterDescricao();  // Saída: Eu sou uma porta de ferro
$expert->obterDescricao(); // Saída: Eu só instalo portas de ferro
```

Como você pode ver, a fábrica de portas de madeira encapsulou o `marceneiro` e a `porta de madeira`, assim como a fábrica de portas de ferro encapsulou a `porta de ferro` e o `soldador`. E, logo, nos ajudou a criar uma regra de que, para cada fábrica, vamos ter sempre as portas e os experts corretos, ou seja, temos certeza de que para cada porta criada, nunca teremos o profissional de instalação errado.

**Quando usar?**

Quando existem dependências inter-relacionadas com uma lógica não muito simples de criação envolvida.

<a name="construtor"></a>👷 Construtor
--------------------------------------------
Exemplo do mundo real:
> Imagine que você está no McDonald's e pede um combo especial, digamos um "Big Mac", e eles te entregam sem *nenhum tipo de questionamento*; Isto é o exemplo da fábrica simples. Mas existem casos que a lógica de criação pode envolver mais passos. Por exemplo, imagine agora que você está no Subway e quer um lanche especialmente feito por você, você tem diversas opções para escolher, por exemplo, com queijo? Com tomate? Quente? Frio? Tipo de pão e etc. Neste caso temos que usar o modelo construtor.

Em palavras simples:
> Permite que você crie diferentes tipos de um objeto enquanto evita poluição no construtor do mesmo. É bastante útil quando um objeto tem diversos "sabores", ou seja, diversas implementações diferentes. Ou quando existem muitos passos envolvidos na criação de um objeto.

Wikipédia diz:
> O padrão construtor é um padrão de projeto criacional de design de software com a intenção de encontrar uma solução para o anti-padrão chamado de construtor telescópico.

Tendo dito isso, vamos explicar um pouco mais o que é o *construtor telescópico*. Em algum momento das nossas vidas como programadores vimos algo assim:

```php
public function __construct($tamanho, $queijo = true, $pepperoni = true, $tomate = false, $alface = true)
{
}
```

Como você pode ver, o número de parâmetros que o construtor leva pode rápidamente sair do controle e ficar complicado de entender o seu arranjo. Além disso, a lista de parâmetros pode continuar a crescer se você decidir adicionar mais opções no futuro. Isto é conhecido por ser um *anti-padrão* <sup>O inverso de padrão de projeto</sup> chamado `construtor telescópico`.

**Exemplo programático**

Usando nosso exemplo acima. A alternativa mais sã é usar o padrão construtor. PRimeiramente temos o nosso lanche:

```php
class Lanche
{
    protected $tamanho;

    protected $queijo = false;
    protected $pepperoni = false;
    protected $alface = false;
    protected $tomate = false;

    public function __construct(ConstrutorLanche $construtor)
    {
        $this->tamanho = $construtor->tamanho;
        $this->queijo = $construtor->queijo;
        $this->pepperoni = $construtor->pepperoni;
        $this->alface = $construtor->alface;
        $this->tomate = $construtor->tomate;
    }
}
```

E agora nosso construtor:

```php
class CosntrutorLanche
{
    public $tamanho;

    public $queijo = false;
    public $pepperoni = false;
    public $alface = false;
    public $tomate = false;

    public function __construct(int $tamanho)
    {
        $this->tamanho = $tamanho;
    }

    public function adicionarPepperoni()
    {
        $this->pepperoni = true;
        return $this;
    }

    public function adicionarAlface()
    {
        $this->alface = true;
        return $this;
    }

    public function adicionarQueijo()
    {
        $this->queijo = true;
        return $this;
    }

    public function adicionarTomate()
    {
        $this->tomate = true;
        return $this;
    }

    public function montar(): Lanche
    {
        return new Lanche($this);
    }
}
```

Podemos jutar tudo em:

```php
$lanche = (new ConstrutorLanche(14))
                    ->adicionarPepperoni()
                    ->adicionarAlface()
                    ->adicionarTomate()
                    ->montar();
```

**Quando usar?**

Quando um objeto pode ter diversos "sabores" e para evitar o anti-padrão de construtor telescópico. A diferença principal entre o construtor e a fábrica é que:

- A fábrica deve ser usada quando o processo de criação tem apenas um passo.
- O construtor é justamente o oposto, ele funciona muito melhor quando o processo de criação é composto de vários passos.

<a name="prototipo"></a>🐑 Prototype
------------
Exemplo do mundo real:
> Remember dolly? The sheep that was cloned! Lets not get into the details but the key point here is that it is all about cloning

Em palavras simples:
> Create object based on an existing object through cloning.

Wikipédia diz:
> The prototype pattern is a creational design pattern in software development. It is used when the type of objects to create is determined by a prototypical instance, which is cloned to produce new objects.

In short, it allows you to create a copy of an existing object and modify it to your needs, instead of going through the trouble of creating an object from scratch and setting it up.

**Exemplo programático**

In PHP, it can be easily done using `clone`

```php
class Sheep
{
    protected $name;
    protected $category;

    public function __construct(string $name, string $category = 'Mountain Sheep')
    {
        $this->name = $name;
        $this->category = $category;
    }

    public function setName(string $name)
    {
        $this->name = $name;
    }

    public function getName()
    {
        return $this->name;
    }

    public function setCategory(string $category)
    {
        $this->category = $category;
    }

    public function getCategory()
    {
        return $this->category;
    }
}
```
Then it can be cloned like below
```php
$original = new Sheep('Jolly');
echo $original->getName(); // Jolly
echo $original->getCategory(); // Mountain Sheep

// Clone and modify what is required
$cloned = clone $original;
$cloned->setName('Dolly');
echo $cloned->getName(); // Dolly
echo $cloned->getCategory(); // Mountain sheep
```

Also you could use the magic method `__clone` to modify the cloning behavior.

**Quando usar?**

When an object is required that is similar to existing object or when the creation would be expensive as compared to cloning.

<a name="singleton"></a>💍 Singleton
------------
Exemplo do mundo real:
> There can only be one president of a country at a time. The same president has to be brought to action, whenever duty calls. President here is singleton.

Em palavras simples:
> Ensures that only one object of a particular class is ever created.

Wikipédia diz:
> In software engineering, the singleton pattern is a software design pattern that restricts the instantiation of a class to one object. This is useful when exactly one object is needed to coordinate actions across the system.

Singleton pattern is actually considered an anti-pattern and overuse of it should be avoided. It is not necessarily bad and could have some valid use-cases but should be used with caution because it introduces a global state in your application and change to it in one place could affect in the other areas and it could become pretty difficult to debug. The other bad thing about them is it makes your code tightly coupled plus it mocking the singleton could be difficult.

**Exemplo programático**

To create a singleton, make the constructor private, disable cloning, disable extension and create a static variable to house the instance
```php
final class President
{
    private static $instance;

    private function __construct()
    {
        // Hide the constructor
    }

    public static function getInstance(): President
    {
        if (!self::$instance) {
            self::$instance = new self();
        }

        return self::$instance;
    }

    private function __clone()
    {
        // Disable cloning
    }

    private function __wakeup()
    {
        // Disable unserialize
    }
}
```
Then in order to use
```php
$president1 = President::getInstance();
$president2 = President::getInstance();

var_dump($president1 === $president2); // true
```

<a name="estruturais"></a>Padrões de projeto estruturais
==========================
Em palavras simples:
> Structural patterns are mostly concerned with object composition or in other words how the entities can use each other. Or yet another explanation would be, they help in answering "How to build a software component?"

Wikipédia diz:
> In software engineering, structural design patterns are design patterns that ease the design by identifying a simple way to realize relationships between entities.

 * [Adapter](#-adapter)
 * [Bridge](#-bridge)
 * [Composite](#-composite)
 * [Decorator](#-decorator)
 * [Facade](#-facade)
 * [Flyweight](#-flyweight)
 * [Proxy](#-proxy)

🔌 Adapter
-------
Exemplo do mundo real:
> Consider that you have some pictures in your memory card and you need to transfer them to your computer. In order to transfer them you need some kind of adapter that is compatible with your computer ports so that you can attach memory card to your computer. In this case card reader is an adapter.
> Another example would be the famous power adapter; a three legged plug can't be connected to a two pronged outlet, it needs to use a power adapter that makes it compatible with the two pronged outlet.
> Yet another example would be a translator translating words spoken by one person to another

Em palavras simples:
> Adapter pattern lets you wrap an otherwise incompatible object in an adapter to make it compatible with another class.

Wikipédia diz:
> In software engineering, the adapter pattern is a software design pattern that allows the interface of an existing class to be used as another interface. It is often used to make existing classes work with others without modifying their source code.

**Exemplo programático**

Consider a game where there is a hunter and he hunts lions.

First we have an interface `Lion` that all types of lions have to implement

```php
interface Lion
{
    public function roar();
}

class AfricanLion implements Lion
{
    public function roar()
    {
    }
}

class AsianLion implements Lion
{
    public function roar()
    {
    }
}
```
And hunter expects any implementation of `Lion` interface to hunt.
```php
class Hunter
{
    public function hunt(Lion $lion)
    {
    }
}
```

Now let's say we have to add a `WildDog` in our game so that hunter can hunt that also. But we can't do that directly because dog has a different interface. To make it compatible for our hunter, we will have to create an adapter that is compatible

```php
// This needs to be added to the game
class WildDog
{
    public function bark()
    {
    }
}

// Adapter around wild dog to make it compatible with our game
class WildDogAdapter implements Lion
{
    protected $dog;

    public function __construct(WildDog $dog)
    {
        $this->dog = $dog;
    }

    public function roar()
    {
        $this->dog->bark();
    }
}
```
And now the `WildDog` can be used in our game using `WildDogAdapter`.

```php
$wildDog = new WildDog();
$wildDogAdapter = new WildDogAdapter($wildDog);

$hunter = new Hunter();
$hunter->hunt($wildDogAdapter);
```

🚡 Bridge
------
Exemplo do mundo real:
> Consider you have a website with different pages and you are supposed to allow the user to change the theme. What would you do? Create multiple copies of each of the pages for each of the themes or would you just create separate theme and load them based on the user's preferences? Bridge pattern allows you to do the second i.e.

![With and without the bridge pattern](https://cloud.githubusercontent.com/assets/11269635/23065293/33b7aea0-f515-11e6-983f-98823c9845ee.png)

In Plain Words
> Bridge pattern is about preferring composition over inheritance. Implementation details are pushed from a hierarchy to another object with a separate hierarchy.

Wikipédia diz:
> The bridge pattern is a design pattern used in software engineering that is meant to "decouple an abstraction from its implementation so that the two can vary independently"

**Exemplo programático**

Translating our WebPage example from above. Here we have the `WebPage` hierarchy

```php
interface WebPage
{
    public function __construct(Theme $theme);
    public function getContent();
}

class About implements WebPage
{
    protected $theme;

    public function __construct(Theme $theme)
    {
        $this->theme = $theme;
    }

    public function getContent()
    {
        return "About page in " . $this->theme->getColor();
    }
}

class Careers implements WebPage
{
    protected $theme;

    public function __construct(Theme $theme)
    {
        $this->theme = $theme;
    }

    public function getContent()
    {
        return "Careers page in " . $this->theme->getColor();
    }
}
```
And the separate theme hierarchy
```php

interface Theme
{
    public function getColor();
}

class DarkTheme implements Theme
{
    public function getColor()
    {
        return 'Dark Black';
    }
}
class LightTheme implements Theme
{
    public function getColor()
    {
        return 'Off white';
    }
}
class AquaTheme implements Theme
{
    public function getColor()
    {
        return 'Light blue';
    }
}
```
And both the hierarchies
```php
$darkTheme = new DarkTheme();

$about = new About($darkTheme);
$careers = new Careers($darkTheme);

echo $about->getContent(); // "About page in Dark Black";
echo $careers->getContent(); // "Careers page in Dark Black";
```

🌿 Composite
-----------------

Exemplo do mundo real:
> Every organization is composed of employees. Each of the employees has the same features i.e. has a salary, has some responsibilities, may or may not report to someone, may or may not have some subordinates etc.

Em palavras simples:
> Composite pattern lets clients treat the individual objects in a uniform manner.

Wikipédia diz:
> In software engineering, the composite pattern is a partitioning design pattern. The composite pattern describes that a group of objects is to be treated in the same way as a single instance of an object. The intent of a composite is to "compose" objects into tree structures to represent part-whole hierarchies. Implementing the composite pattern lets clients treat individual objects and compositions uniformly.

**Exemplo programático**

Taking our employees example from above. Here we have different employee types

```php
interface Employee
{
    public function __construct(string $name, float $salary);
    public function getName(): string;
    public function setSalary(float $salary);
    public function getSalary(): float;
    public function getRoles(): array;
}

class Developer implements Employee
{
    protected $salary;
    protected $name;

    public function __construct(string $name, float $salary)
    {
        $this->name = $name;
        $this->salary = $salary;
    }

    public function getName(): string
    {
        return $this->name;
    }

    public function setSalary(float $salary)
    {
        $this->salary = $salary;
    }

    public function getSalary(): float
    {
        return $this->salary;
    }

    public function getRoles(): array
    {
        return $this->roles;
    }
}

class Designer implements Employee
{
    protected $salary;
    protected $name;

    public function __construct(string $name, float $salary)
    {
        $this->name = $name;
        $this->salary = $salary;
    }

    public function getName(): string
    {
        return $this->name;
    }

    public function setSalary(float $salary)
    {
        $this->salary = $salary;
    }

    public function getSalary(): float
    {
        return $this->salary;
    }

    public function getRoles(): array
    {
        return $this->roles;
    }
}
```

Then we have an organization which consists of several different types of employees

```php
class Organization
{
    protected $employees;

    public function addEmployee(Employee $employee)
    {
        $this->employees[] = $employee;
    }

    public function getNetSalaries(): float
    {
        $netSalary = 0;

        foreach ($this->employees as $employee) {
            $netSalary += $employee->getSalary();
        }

        return $netSalary;
    }
}
```

And then it can be used as

```php
// Prepare the employees
$john = new Developer('John Doe', 12000);
$jane = new Designer('Jane', 10000);

// Add them to organization
$organization = new Organization();
$organization->addEmployee($john);
$organization->addEmployee($jane);

echo "Net salaries: " . $organization->getNetSalaries(); // Net Salaries: 22000
```

☕ Decorator
-------------

Exemplo do mundo real:

> Imagine you run a car service shop offering multiple services. Now how do you calculate the bill to be charged? You pick one service and dynamically keep adding to it the prices for the provided services till you get the final cost. Here each type of service is a decorator.

Em palavras simples:
> Decorator pattern lets you dynamically change the behavior of an object at run time by wrapping them in an object of a decorator class.

Wikipédia diz:
> In object-oriented programming, the decorator pattern is a design pattern that allows behavior to be added to an individual object, either statically or dynamically, without affecting the behavior of other objects from the same class. The decorator pattern is often useful for adhering to the Single Responsibility Principle, as it allows functionality to be divided between classes with unique areas of concern.

**Exemplo programático**

Lets take coffee for example. First of all we have a simple coffee implementing the coffee interface

```php
interface Coffee
{
    public function getCost();
    public function obterDescricao();
}

class SimpleCoffee implements Coffee
{
    public function getCost()
    {
        return 10;
    }

    public function obterDescricao()
    {
        return 'Simple coffee';
    }
}
```
We want to make the code extensible to allow options to modify it if required. Lets make some add-ons (decorators)
```php
class MilkCoffee implements Coffee
{
    protected $coffee;

    public function __construct(Coffee $coffee)
    {
        $this->coffee = $coffee;
    }

    public function getCost()
    {
        return $this->coffee->getCost() + 2;
    }

    public function obterDescricao()
    {
        return $this->coffee->obterDescricao() . ', milk';
    }
}

class WhipCoffee implements Coffee
{
    protected $coffee;

    public function __construct(Coffee $coffee)
    {
        $this->coffee = $coffee;
    }

    public function getCost()
    {
        return $this->coffee->getCost() + 5;
    }

    public function obterDescricao()
    {
        return $this->coffee->obterDescricao() . ', whip';
    }
}

class VanillaCoffee implements Coffee
{
    protected $coffee;

    public function __construct(Coffee $coffee)
    {
        $this->coffee = $coffee;
    }

    public function getCost()
    {
        return $this->coffee->getCost() + 3;
    }

    public function obterDescricao()
    {
        return $this->coffee->obterDescricao() . ', vanilla';
    }
}
```

Lets make a coffee now

```php
$someCoffee = new SimpleCoffee();
echo $someCoffee->getCost(); // 10
echo $someCoffee->obterDescricao(); // Simple Coffee

$someCoffee = new MilkCoffee($someCoffee);
echo $someCoffee->getCost(); // 12
echo $someCoffee->obterDescricao(); // Simple Coffee, milk

$someCoffee = new WhipCoffee($someCoffee);
echo $someCoffee->getCost(); // 17
echo $someCoffee->obterDescricao(); // Simple Coffee, milk, whip

$someCoffee = new VanillaCoffee($someCoffee);
echo $someCoffee->getCost(); // 20
echo $someCoffee->obterDescricao(); // Simple Coffee, milk, whip, vanilla
```

📦 Facade
----------------

Exemplo do mundo real:
> How do you turn on the computer? "Hit the power button" you say! That is what you believe because you are using a simple interface that computer provides on the outside, internally it has to do a lot of stuff to make it happen. This simple interface to the complex subsystem is a facade.

Em palavras simples:
> Facade pattern provides a simplified interface to a complex subsystem.

Wikipédia diz:
> A facade is an object that provides a simplified interface to a larger body of code, such as a class library.

**Exemplo programático**

Taking our computer example from above. Here we have the computer class

```php
class Computer
{
    public function getElectricShock()
    {
        echo "Ouch!";
    }

    public function makeSound()
    {
        echo "Beep beep!";
    }

    public function showLoadingScreen()
    {
        echo "Loading..";
    }

    public function bam()
    {
        echo "Ready to be used!";
    }

    public function closeEverything()
    {
        echo "Bup bup bup buzzzz!";
    }

    public function sooth()
    {
        echo "Zzzzz";
    }

    public function pullCurrent()
    {
        echo "Haaah!";
    }
}
```
Here we have the facade
```php
class ComputerFacade
{
    protected $computer;

    public function __construct(Computer $computer)
    {
        $this->computer = $computer;
    }

    public function turnOn()
    {
        $this->computer->getElectricShock();
        $this->computer->makeSound();
        $this->computer->showLoadingScreen();
        $this->computer->bam();
    }

    public function turnOff()
    {
        $this->computer->closeEverything();
        $this->computer->pullCurrent();
        $this->computer->sooth();
    }
}
```
Now to use the facade
```php
$computer = new ComputerFacade(new Computer());
$computer->turnOn(); // Ouch! Beep beep! Loading.. Ready to be used!
$computer->turnOff(); // Bup bup buzzz! Haah! Zzzzz
```

🍃 Flyweight
---------

Exemplo do mundo real:
> Did you ever have fresh tea from some stall? They often make more than one cup that you demanded and save the rest for any other customer so to save the resources e.g. gas etc. Flyweight pattern is all about that i.e. sharing.

Em palavras simples:
> It is used to minimize memory usage or computational expenses by sharing as much as possible with similar objects.

Wikipédia diz:
> In computer programming, flyweight is a software design pattern. A flyweight is an object that minimizes memory use by sharing as much data as possible with other similar objects; it is a way to use objects in large numbers when a simple repeated representation would use an unacceptable amount of memory.

**Programmatic example**

Translating our tea example from above. First of all we have tea types and tea maker

```php
// Anything that will be cached is flyweight.
// Types of tea here will be flyweights.
class KarakTea
{
}

// Acts as a factory and saves the tea
class TeaMaker
{
    protected $availableTea = [];

    public function make($preference)
    {
        if (empty($this->availableTea[$preference])) {
            $this->availableTea[$preference] = new KarakTea();
        }

        return $this->availableTea[$preference];
    }
}
```

Then we have the `TeaShop` which takes orders and serves them

```php
class TeaShop
{
    protected $orders;
    protected $teaMaker;

    public function __construct(TeaMaker $teaMaker)
    {
        $this->teaMaker = $teaMaker;
    }

    public function takeOrder(string $teaType, int $table)
    {
        $this->orders[$table] = $this->teaMaker->make($teaType);
    }

    public function serve()
    {
        foreach ($this->orders as $table => $tea) {
            echo "Serving tea to table# " . $table;
        }
    }
}
```
And it can be used as below

```php
$teaMaker = new TeaMaker();
$shop = new TeaShop($teaMaker);

$shop->takeOrder('less sugar', 1);
$shop->takeOrder('more milk', 2);
$shop->takeOrder('without sugar', 5);

$shop->serve();
// Serving tea to table# 1
// Serving tea to table# 2
// Serving tea to table# 5
```

🎱 Proxy
-------------------
Exemplo do mundo real:
> Have you ever used an access card to go through a door? There are multiple options to open that door i.e. it can be opened either using access card or by pressing a button that bypasses the security. The door's main functionality is to open but there is a proxy added on top of it to add some functionality. Let me better explain it using the code example below.

Em palavras simples:
> Using the proxy pattern, a class represents the functionality of another class.

Wikipédia diz:
> A proxy, in its most general form, is a class functioning as an interface to something else. A proxy is a wrapper or agent object that is being called by the client to access the real serving object behind the scenes. Use of the proxy can simply be forwarding to the real object, or can provide additional logic. In the proxy extra functionality can be provided, for example caching when operations on the real object are resource intensive, or checking preconditions before operations on the real object are invoked.

**Exemplo programático**

Taking our security door example from above. Firstly we have the door interface and an implementation of door

```php
interface Door
{
    public function open();
    public function close();
}

class LabDoor implements Door
{
    public function open()
    {
        echo "Opening lab door";
    }

    public function close()
    {
        echo "Closing the lab door";
    }
}
```
Then we have a proxy to secure any doors that we want
```php
class Security
{
    protected $porta;

    public function __construct(Door $porta)
    {
        $this->door = $porta;
    }

    public function open($password)
    {
        if ($this->authenticate($password)) {
            $this->door->open();
        } else {
            echo "Big no! It ain't possible.";
        }
    }

    public function authenticate($password)
    {
        return $password === '$ecr@t';
    }

    public function close()
    {
        $this->door->close();
    }
}
```
And here is how it can be used
```php
$porta = new Security(new LabDoor());
$porta->open('invalid'); // Big no! It ain't possible.

$porta->open('$ecr@t'); // Opening lab door
$porta->close(); // Closing lab door
```
Yet another example would be some sort of data-mapper implementation. For example, I recently made an ODM (Object Data Mapper) for MongoDB using this pattern where I wrote a proxy around mongo classes while utilizing the magic method `__call()`. All the method calls were proxied to the original mongo class and result retrieved was returned as it is but in case of `find` or `findOne` data was mapped to the required class objects and the object was returned instead of `Cursor`.

<a name="comportamentais"></a>Padrões de projeto comportamentais
==========================

Em palavras simples:
> It is concerned with assignment of responsibilities between the objects. What makes them different from structural patterns is they don't just specify the structure but also outline the patterns for message passing/communication between them. Or in other words, they assist in answering "How to run a behavior in software component?"

Wikipédia diz:
> In software engineering, behavioral design patterns are design patterns that identify common communication patterns between objects and realize these patterns. By doing so, these patterns increase flexibility in carrying out this communication.

* [Chain of Responsibility](#-chain-of-responsibility)
* [Command](#-command)
* [Iterator](#-iterator)
* [Mediator](#-mediator)
* [Memento](#-memento)
* [Observer](#-observer)
* [Visitor](#-visitor)
* [Strategy](#-strategy)
* [State](#-state)
* [Template Method](#-template-method)

🔗 Chain of Responsibility
-----------------------

Exemplo do mundo real:
> For example, you have three payment methods (`A`, `B` and `C`) setup in your account; each having a different amount in it. `A` has 100 USD, `B` has 300 USD and `C` having 1000 USD and the preference for payments is chosen as `A` then `B` then `C`. You try to purchase something that is worth 210 USD. Using Chain of Responsibility, first of all account `A` will be checked if it can make the purchase, if yes purchase will be made and the chain will be broken. If not, request will move forward to account `B` checking for amount if yes chain will be broken otherwise the request will keep forwarding till it finds the suitable handler. Here `A`, `B` and `C` are links of the chain and the whole phenomenon is Chain of Responsibility.

Em palavras simples:
> It helps building a chain of objects. Request enters from one end and keeps going from object to object till it finds the suitable handler.

Wikipédia diz:
> In object-oriented design, the chain-of-responsibility pattern is a design pattern consisting of a source of command objects and a series of processing objects. Each processing object contains logic that defines the types of command objects that it can handle; the rest are passed to the next processing object in the chain.

**Exemplo programático**

Translating our account example above. First of all we have a base account having the logic for chaining the accounts together and some accounts

```php
abstract class Account
{
    protected $successor;
    protected $balance;

    public function setNext(Account $account)
    {
        $this->successor = $account;
    }

    public function pay(float $amountToPay)
    {
        if ($this->canPay($amountToPay)) {
            echo sprintf('Paid %s using %s' . PHP_EOL, $amountToPay, get_called_class());
        } elseif ($this->successor) {
            echo sprintf('Cannot pay using %s. Proceeding ..' . PHP_EOL, get_called_class());
            $this->successor->pay($amountToPay);
        } else {
            throw new Exception('None of the accounts have enough balance');
        }
    }

    public function canPay($amount): bool
    {
        return $this->balance >= $amount;
    }
}

class Bank extends Account
{
    protected $balance;

    public function __construct(float $balance)
    {
        $this->balance = $balance;
    }
}

class Paypal extends Account
{
    protected $balance;

    public function __construct(float $balance)
    {
        $this->balance = $balance;
    }
}

class Bitcoin extends Account
{
    protected $balance;

    public function __construct(float $balance)
    {
        $this->balance = $balance;
    }
}
```

Now let's prepare the chain using the links defined above (i.e. Bank, Paypal, Bitcoin)

```php
// Let's prepare a chain like below
//      $bank->$paypal->$bitcoin
//
// First priority bank
//      If bank can't pay then paypal
//      If paypal can't pay then bit coin

$bank = new Bank(100);          // Bank with balance 100
$paypal = new Paypal(200);      // Paypal with balance 200
$bitcoin = new Bitcoin(300);    // Bitcoin with balance 300

$bank->setNext($paypal);
$paypal->setNext($bitcoin);

// Let's try to pay using the first priority i.e. bank
$bank->pay(259);

// Output will be
// ==============
// Cannot pay using bank. Proceeding ..
// Cannot pay using paypal. Proceeding ..:
// Paid 259 using Bitcoin!
```

👮 Command
-------

Exemplo do mundo real:
> A generic example would be you ordering a food at restaurant. You (i.e. `Client`) ask the waiter (i.e. `Invoker`) to bring some food (i.e. `Command`) and waiter simply forwards the request to Chef (i.e. `Receiver`) who has the knowledge of what and how to cook.
> Another example would be you (i.e. `Client`) switching on (i.e. `Command`) the television (i.e. `Receiver`) using a remote control (`Invoker`).

Em palavras simples:
> Allows you to encapsulate actions in objects. The key idea behind this pattern is to provide the means to decouple client from receiver.

Wikipédia diz:
> In object-oriented programming, the command pattern is a behavioral design pattern in which an object is used to encapsulate all information needed to perform an action or trigger an event at a later time. This information includes the method name, the object that owns the method and values for the method parameters.

**Exemplo programático**

First of all we have the receiver that has the implementation of every action that could be performed
```php
// Receiver
class Bulb
{
    public function turnOn()
    {
        echo "Bulb has been lit";
    }

    public function turnOff()
    {
        echo "Darkness!";
    }
}
```
then we have an interface that each of the commands are going to implement and then we have a set of commands
```php
interface Command
{
    public function execute();
    public function undo();
    public function redo();
}

// Command
class TurnOn implements Command
{
    protected $bulb;

    public function __construct(Bulb $bulb)
    {
        $this->bulb = $bulb;
    }

    public function execute()
    {
        $this->bulb->turnOn();
    }

    public function undo()
    {
        $this->bulb->turnOff();
    }

    public function redo()
    {
        $this->execute();
    }
}

class TurnOff implements Command
{
    protected $bulb;

    public function __construct(Bulb $bulb)
    {
        $this->bulb = $bulb;
    }

    public function execute()
    {
        $this->bulb->turnOff();
    }

    public function undo()
    {
        $this->bulb->turnOn();
    }

    public function redo()
    {
        $this->execute();
    }
}
```
Then we have an `Invoker` with whom the client will interact to process any commands
```php
// Invoker
class RemoteControl
{
    public function submit(Command $command)
    {
        $command->execute();
    }
}
```
Finally let's see how we can use it in our client
```php
$bulb = new Bulb();

$turnOn = new TurnOn($bulb);
$turnOff = new TurnOff($bulb);

$remote = new RemoteControl();
$remote->submit($turnOn); // Bulb has been lit!
$remote->submit($turnOff); // Darkness!
```

Command pattern can also be used to implement a transaction based system. Where you keep maintaining the history of commands as soon as you execute them. If the final command is successfully executed, all good otherwise just iterate through the history and keep executing the `undo` on all the executed commands.

➿ Iterator
--------

Exemplo do mundo real:
> An old radio set will be a good example of iterator, where user could start at some channel and then use next or previous buttons to go through the respective channels. Or take an example of MP3 player or a TV set where you could press the next and previous buttons to go through the consecutive channels or in other words they all provide an interface to iterate through the respective channels, songs or radio stations.  

Em palavras simples:
> It presents a way to access the elements of an object without exposing the underlying presentation.

Wikipédia diz:
> In object-oriented programming, the iterator pattern is a design pattern in which an iterator is used to traverse a container and access the container's elements. The iterator pattern decouples algorithms from containers; in some cases, algorithms are necessarily container-specific and thus cannot be decoupled.

**Programmatic example**

In PHP it is quite easy to implement using SPL (Standard PHP Library). Translating our radio stations example from above. First of all we have `RadioStation`

```php
class RadioStation
{
    protected $frequency;

    public function __construct(float $frequency)
    {
        $this->frequency = $frequency;
    }

    public function getFrequency(): float
    {
        return $this->frequency;
    }
}
```
Then we have our iterator

```php
use Countable;
use Iterator;

class StationList implements Countable, Iterator
{
    /** @var RadioStation[] $stations */
    protected $stations = [];

    /** @var int $counter */
    protected $counter;

    public function addStation(RadioStation $station)
    {
        $this->stations[] = $station;
    }

    public function removeStation(RadioStation $toRemove)
    {
        $toRemoveFrequency = $toRemove->getFrequency();
        $this->stations = array_filter($this->stations, function (RadioStation $station) use ($toRemoveFrequency) {
            return $station->getFrequency() !== $toRemoveFrequency;
        });
    }

    public function count(): int
    {
        return count($this->stations);
    }

    public function current(): RadioStation
    {
        return $this->stations[$this->counter];
    }

    public function key()
    {
        return $this->counter;
    }

    public function next()
    {
        $this->counter++;
    }

    public function rewind()
    {
        $this->counter = 0;
    }

    public function valid(): bool
    {
        return isset($this->stations[$this->counter]);
    }
}
```
And then it can be used as
```php
$stationList = new StationList();

$stationList->addStation(new RadioStation(89));
$stationList->addStation(new RadioStation(101));
$stationList->addStation(new RadioStation(102));
$stationList->addStation(new RadioStation(103.2));

foreach($stationList as $station) {
    echo $station->getFrequency() . PHP_EOL;
}

$stationList->removeStation(new RadioStation(89)); // Will remove station 89
```

👽 Mediator
========

Exemplo do mundo real:
> A general example would be when you talk to someone on your mobile phone, there is a network provider sitting between you and them and your conversation goes through it instead of being directly sent. In this case network provider is mediator.

Em palavras simples:
> Mediator pattern adds a third party object (called mediator) to control the interaction between two objects (called colleagues). It helps reduce the coupling between the classes communicating with each other. Because now they don't need to have the knowledge of each other's implementation.

Wikipédia diz:
> In software engineering, the mediator pattern defines an object that encapsulates how a set of objects interact. This pattern is considered to be a behavioral pattern due to the way it can alter the program's running behavior.

**Exemplo programático**

Here is the simplest example of a chat room (i.e. mediator) with users (i.e. colleagues) sending messages to each other.

First of all, we have the mediator i.e. the chat room

```php
interface ChatRoomMediator 
{
    public function showMessage(User $user, string $message);
}

// Mediator
class ChatRoom implements ChatRoomMediator
{
    public function showMessage(User $user, string $message)
    {
        $time = date('M d, y H:i');
        $sender = $user->getName();

        echo $time . '[' . $sender . ']:' . $message;
    }
}
```

Then we have our users i.e. colleagues
```php
class User {
    protected $name;
    protected $chatMediator;

    public function __construct(string $name, ChatRoomMediator $chatMediator) {
        $this->name = $name;
        $this->chatMediator = $chatMediator;
    }

    public function getName() {
        return $this->name;
    }

    public function send($message) {
        $this->chatMediator->showMessage($this, $message);
    }
}
```
And the usage
```php
$mediator = new ChatRoom();

$john = new User('John Doe', $mediator);
$jane = new User('Jane Doe', $mediator);

$john->send('Hi there!');
$jane->send('Hey!');

// Output will be
// Feb 14, 10:58 [John]: Hi there!
// Feb 14, 10:58 [Jane]: Hey!
```

💾 Memento
-------
Exemplo do mundo real:
> Take the example of calculator (i.e. originator), where whenever you perform some calculation the last calculation is saved in memory (i.e. memento) so that you can get back to it and maybe get it restored using some action buttons (i.e. caretaker).

Em palavras simples:
> Memento pattern is about capturing and storing the current state of an object in a manner that it can be restored later on in a smooth manner.

Wikipédia diz:
> The memento pattern is a software design pattern that provides the ability to restore an object to its previous state (undo via rollback).

Usually useful when you need to provide some sort of undo functionality.

**Exemplo programático**

Lets take an example of text editor which keeps saving the state from time to time and that you can restore if you want.

First of all we have our memento object that will be able to hold the editor state

```php
class EditorMemento
{
    protected $content;

    public function __construct(string $content)
    {
        $this->content = $content;
    }

    public function getContent()
    {
        return $this->content;
    }
}
```

Then we have our editor i.e. originator that is going to use memento object

```php
class Editor
{
    protected $content = '';

    public function type(string $words)
    {
        $this->content = $this->content . ' ' . $words;
    }

    public function getContent()
    {
        return $this->content;
    }

    public function save()
    {
        return new EditorMemento($this->content);
    }

    public function restore(EditorMemento $memento)
    {
        $this->content = $memento->getContent();
    }
}
```

And then it can be used as

```php
$editor = new Editor();

// Type some stuff
$editor->type('This is the first sentence.');
$editor->type('This is second.');

// Save the state to restore to : This is the first sentence. This is second.
$saved = $editor->save();

// Type some more
$editor->type('And this is third.');

// Output: Content before Saving
echo $editor->getContent(); // This is the first sentence. This is second. And this is third.

// Restoring to last saved state
$editor->restore($saved);

$editor->getContent(); // This is the first sentence. This is second.
```

😎 Observer
--------
Exemplo do mundo real:
> A good example would be the job seekers where they subscribe to some job posting site and they are notified whenever there is a matching job opportunity.   

Em palavras simples:
> Defines a dependency between objects so that whenever an object changes its state, all its dependents are notified.

Wikipédia diz:
> The observer pattern is a software design pattern in which an object, called the subject, maintains a list of its dependents, called observers, and notifies them automatically of any state changes, usually by calling one of their methods.

**Programmatic example**

Translating our example from above. First of all we have job seekers that need to be notified for a job posting
```php
class JobPost
{
    protected $title;

    public function __construct(string $title)
    {
        $this->title = $title;
    }

    public function getTitle()
    {
        return $this->title;
    }
}

class JobSeeker implements Observer
{
    protected $name;

    public function __construct(string $name)
    {
        $this->name = $name;
    }

    public function onJobPosted(JobPost $job)
    {
        // Do something with the job posting
        echo 'Hi ' . $this->name . '! New job posted: '. $job->getTitle();
    }
}
```
Then we have our job postings to which the job seekers will subscribe
```php
class JobPostings implements Observable
{
    protected $observers = [];

    protected function notify(JobPost $jobPosting)
    {
        foreach ($this->observers as $observer) {
            $observer->onJobPosted($jobPosting);
        }
    }

    public function attach(Observer $observer)
    {
        $this->observers[] = $observer;
    }

    public function addJob(JobPost $jobPosting)
    {
        $this->notify($jobPosting);
    }
}
```
Then it can be used as
```php
// Create subscribers
$johnDoe = new JobSeeker('John Doe');
$janeDoe = new JobSeeker('Jane Doe');

// Create publisher and attach subscribers
$jobPostings = new JobPostings();
$jobPostings->attach($johnDoe);
$jobPostings->attach($janeDoe);

// Add a new job and see if subscribers get notified
$jobPostings->addJob(new JobPost('Software Engineer'));

// Output
// Hi John Doe! New job posted: Software Engineer
// Hi Jane Doe! New job posted: Software Engineer
```

🏃 Visitor
-------
Exemplo do mundo real:
> Consider someone visiting Dubai. They just need a way (i.e. visa) to enter Dubai. After arrival, they can come and visit any place in Dubai on their own without having to ask for permission or to do some leg work in order to visit any place here; just let them know of a place and they can visit it. Visitor pattern lets you do just that, it helps you add places to visit so that they can visit as much as they can without having to do any legwork.

Em palavras simples:
> Visitor pattern lets you add further operations to objects without having to modify them.

Wikipédia diz:
> In object-oriented programming and software engineering, the visitor design pattern is a way of separating an algorithm from an object structure on which it operates. A practical result of this separation is the ability to add new operations to existing object structures without modifying those structures. It is one way to follow the open/closed principle.

**Programmatic example**

Let's take an example of a zoo simulation where we have several different kinds of animals and we have to make them Sound. Let's translate this using visitor pattern

```php
// Visitee
interface Animal
{
    public function accept(AnimalOperation $operation);
}

// Visitor
interface AnimalOperation
{
    public function visitMonkey(Monkey $monkey);
    public function visitLion(Lion $lion);
    public function visitDolphin(Dolphin $dolphin);
}
```
Then we have our implementations for the animals
```php
class Monkey implements Animal
{
    public function shout()
    {
        echo 'Ooh oo aa aa!';
    }

    public function accept(AnimalOperation $operation)
    {
        $operation->visitMonkey($this);
    }
}

class Lion implements Animal
{
    public function roar()
    {
        echo 'Roaaar!';
    }

    public function accept(AnimalOperation $operation)
    {
        $operation->visitLion($this);
    }
}

class Dolphin implements Animal
{
    public function speak()
    {
        echo 'Tuut tuttu tuutt!';
    }

    public function accept(AnimalOperation $operation)
    {
        $operation->visitDolphin($this);
    }
}
```
Let's implement our visitor
```php
class Speak implements AnimalOperation
{
    public function visitMonkey(Monkey $monkey)
    {
        $monkey->shout();
    }

    public function visitLion(Lion $lion)
    {
        $lion->roar();
    }

    public function visitDolphin(Dolphin $dolphin)
    {
        $dolphin->speak();
    }
}
```

And then it can be used as
```php
$monkey = new Monkey();
$lion = new Lion();
$dolphin = new Dolphin();

$speak = new Speak();

$monkey->accept($speak);    // Ooh oo aa aa!    
$lion->accept($speak);      // Roaaar!
$dolphin->accept($speak);   // Tuut tutt tuutt!
```
We could have done this simply by having an inheritance hierarchy for the animals but then we would have to modify the animals whenever we would have to add new actions to animals. But now we will not have to change them. For example, let's say we are asked to add the jump behavior to the animals, we can simply add that by creating a new visitor i.e.

```php
class Jump implements AnimalOperation
{
    public function visitMonkey(Monkey $monkey)
    {
        echo 'Jumped 20 feet high! on to the tree!';
    }

    public function visitLion(Lion $lion)
    {
        echo 'Jumped 7 feet! Back on the ground!';
    }

    public function visitDolphin(Dolphin $dolphin)
    {
        echo 'Walked on water a little and disappeared';
    }
}
```
And for the usage
```php
$jump = new Jump();

$monkey->accept($speak);   // Ooh oo aa aa!
$monkey->accept($jump);    // Jumped 20 feet high! on to the tree!

$lion->accept($speak);     // Roaaar!
$lion->accept($jump);      // Jumped 7 feet! Back on the ground!

$dolphin->accept($speak);  // Tuut tutt tuutt!
$dolphin->accept($jump);   // Walked on water a little and disappeared
```

💡 Strategy
--------

Exemplo do mundo real:
> Consider the example of sorting, we implemented bubble sort but the data started to grow and bubble sort started getting very slow. In order to tackle this we implemented Quick sort. But now although the quick sort algorithm was doing better for large datasets, it was very slow for smaller datasets. In order to handle this we implemented a strategy where for small datasets, bubble sort will be used and for larger, quick sort.

Em palavras simples:
> Strategy pattern allows you to switch the algorithm or strategy based upon the situation.

Wikipédia diz:
> In computer programming, the strategy pattern (also known as the policy pattern) is a behavioural software design pattern that enables an algorithm's behavior to be selected at runtime.

**Programmatic example**

Translating our example from above. First of all we have our strategy interface and different strategy implementations

```php
interface SortStrategy
{
    public function sort(array $dataset): array;
}

class BubbleSortStrategy implements SortStrategy
{
    public function sort(array $dataset): array
    {
        echo "Sorting using bubble sort";

        // Do sorting
        return $dataset;
    }
}

class QuickSortStrategy implements SortStrategy
{
    public function sort(array $dataset): array
    {
        echo "Sorting using quick sort";

        // Do sorting
        return $dataset;
    }
}
```

And then we have our client that is going to use any strategy
```php
class Sorter
{
    protected $sorter;

    public function __construct(SortStrategy $sorter)
    {
        $this->sorter = $sorter;
    }

    public function sort(array $dataset): array
    {
        return $this->sorter->sort($dataset);
    }
}
```
And it can be used as
```php
$dataset = [1, 5, 4, 3, 2, 8];

$sorter = new Sorter(new BubbleSortStrategy());
$sorter->sort($dataset); // Output : Sorting using bubble sort

$sorter = new Sorter(new QuickSortStrategy());
$sorter->sort($dataset); // Output : Sorting using quick sort
```

💢 State
-----
Exemplo do mundo real:
> Imagine you are using some drawing application, you choose the paint brush to draw. Now the brush changes its behavior based on the selected color i.e. if you have chosen red color it will draw in red, if blue then it will be in blue etc.  

Em palavras simples:
> It lets you change the behavior of a class when the state changes.

Wikipédia diz:
> The state pattern is a behavioral software design pattern that implements a state machine in an object-oriented way. With the state pattern, a state machine is implemented by implementing each individual state as a derived class of the state pattern interface, and implementing state transitions by invoking methods defined by the pattern's superclass.
> The state pattern can be interpreted as a strategy pattern which is able to switch the current strategy through invocations of methods defined in the pattern's interface.

**Programmatic example**

Let's take an example of text editor, it lets you change the state of text that is typed i.e. if you have selected bold, it starts writing in bold, if italic then in italics etc.

First of all we have our state interface and some state implementations

```php
interface WritingState
{
    public function write(string $words);
}

class UpperCase implements WritingState
{
    public function write(string $words)
    {
        echo strtoupper($words);
    }
}

class LowerCase implements WritingState
{
    public function write(string $words)
    {
        echo strtolower($words);
    }
}

class Default implements WritingState
{
    public function write(string $words)
    {
        echo $words;
    }
}
```
Then we have our editor
```php
class TextEditor
{
    protected $state;

    public function __construct(WritingState $state)
    {
        $this->state = $state;
    }

    public function setState(WritingState $state)
    {
        $this->state = $state;
    }

    public function type(string $words)
    {
        $this->state->write($words);
    }
}
```
And then it can be used as
```php
$editor = new TextEditor(new Default());

$editor->type('First line');

$editor->setState(new UpperCase());

$editor->type('Second line');
$editor->type('Third line');

$editor->setState(new LowerCase());

$editor->type('Fourth line');
$editor->type('Fifth line');

// Output:
// First line
// SECOND LINE
// THIRD LINE
// fourth line
// fifth line
```

📒 Template Method
---------------

Exemplo do mundo real:
> Suppose we are getting some house built. The steps for building might look like
> - Prepare the base of house
> - Build the walls
> - Add roof
> - Add other floors

> The order of these steps could never be changed i.e. you can't build the roof before building the walls etc but each of the steps could be modified for example walls can be made of wood or polyester or stone.

Em palavras simples:
> Template method defines the skeleton of how a certain algorithm could be performed, but defers the implementation of those steps to the children classes.

Wikipédia diz:
> In software engineering, the template method pattern is a behavioral design pattern that defines the program skeleton of an algorithm in an operation, deferring some steps to subclasses. It lets one redefine certain steps of an algorithm without changing the algorithm's structure.

**Exemplo programático**

Imagine we have a build tool that helps us test, lint, build, generate build reports (i.e. code coverage reports, linting report etc) and deploy our app on the test server.

First of all we have our base class that specifies the skeleton for the build algorithm
```php
abstract class Builder
{

    // Template method
    final public function build()
    {
        $this->test();
        $this->lint();
        $this->assemble();
        $this->deploy();
    }

    abstract public function test();
    abstract public function lint();
    abstract public function assemble();
    abstract public function deploy();
}
```

Then we can have our implementations

```php
class AndroidBuilder extends Builder
{
    public function test()
    {
        echo 'Running android tests';
    }

    public function lint()
    {
        echo 'Linting the android code';
    }

    public function assemble()
    {
        echo 'Assembling the android build';
    }

    public function deploy()
    {
        echo 'Deploying android build to server';
    }
}

class IosBuilder extends Builder
{
    public function test()
    {
        echo 'Running ios tests';
    }

    public function lint()
    {
        echo 'Linting the ios code';
    }

    public function assemble()
    {
        echo 'Assembling the ios build';
    }

    public function deploy()
    {
        echo 'Deploying ios build to server';
    }
}
```
And then it can be used as

```php
$androidBuilder = new AndroidBuilder();
$androidBuilder->build();

// Output:
// Running android tests
// Linting the android code
// Assembling the android build
// Deploying android build to server

$iosBuilder = new IosBuilder();
$iosBuilder->build();

// Output:
// Running ios tests
// Linting the ios code
// Assembling the ios build
// Deploying ios build to server
```

## 🚦 Wrap Up Folks

And that about wraps it up. I will continue to improve this, so you might want to watch/star this repository to revisit. Also, I have plans on writing the same about the architectural patterns, stay tuned for it.

## 👬 Contribution

- Report issues
- Open pull request with improvements
- Spread the word
- Reach out to me directly at kamranahmed.se@gmail.com or on twitter [@kamranahmedse](http://twitter.com/kamranahmedse)

## License
MIT © [Kamran Ahmed](http://kamranahmed.info)
