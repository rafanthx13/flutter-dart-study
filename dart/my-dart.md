# Dart

## Índex

[TOC]

## Links

+ https://github.com/Temidtech/dart-cheat-sheet
+ https://github.com/FlutterInThai/Dart-for-Flutter-Sheet-cheet/blob/master/Dart-for-Flutter-Cheat-Sheet.pdf



## O que é Dart?

Dart (originalmente denominada Dash) é uma linguagem de script voltada à web desenvolvida pela Google. Ela foi lançada na GOTO Conference 2011,[1] que aconteceu de 10 a 11 de outubro de 2011 em Aarhus, na Dinamarca. O objetivo da linguagem Dart foi inicialmente a de substituir a JavaScript como a linguagem principal embutida nos navegadores. Programas nesta linguagem podem tanto serem executados em uma máquina virtual quanto compilados para JavaScript

**Compilador Online**

https://dartpad.dev/

**Lembrando**

Como em flutter, é recomendável não usar hífen nos nomes dos arquivos e nem pastas de dart/flutter. Use `underline`

### Comentários

Da mesma Forma que JavaScript, C, Java.

**OUTRO DETALHE, `;` é obrigatório**

```dart
main(){
    print(1);
    // comment in line
    /* 
    	comment multi-line
    */
}
```

## Check se Dart está instalado

```sh
$ dart --version
```



## Executar Dart no CodeRunner do VS Code

To run code:

+ use shortcut Ctrl+Alt+N

Obs:

+ salve o arquivo antes
+ O code runner executa ate mesmo trecho de codigo, então, tome cuidado

## Como inicia Dart

Diferente de JS ou python, Dar é como Java ou C. O programa inicia pela função main

**Hello World**

```dart
void main() {
  print('Hello World!');
}
```

## Tipos em Dart

### Tipos Básicos

Números

```
num, int, double
```

String

```
String
```

Boolean

```
bool 
```

+ Recebe `true, false`

### Tipos compostos

Lists (array)

```
[0,1,2,3]
```

+ Métodos
  + Adicionar : alist.add()
  + Acessar: alist.atElement(2) | alist[2]

Set (conjunto que tem elementos únicos)

```
{1,2,3}
```

+ Ex: `var conjunto = {0,0,1,1,2,2,3,3,3,3,4,4,4}`
  + Se fizer `conjunto.length` será 5 pois só há 5 valores únicos diferentes

Maps (Dicionário ou array associativo)

```
{'key': 'value'}
```

### Variáveis

`var`

+ `var name = 'Bob'`
+ Faz inferência, ou seja, não precisa expecificar o tipo, mas depois de definido não muda de tipo

`dynamic`

+ Como as variáveis em python pode receber qualquer coisa a qualquer hora

`final`

+ .

`const`

+ 

### Obs

Muitas vezes  nâo é necessário por o tipo, como em retorno de função ou em parâmetros. Nesses casos, a variável fica `dynamic`

```dart
soma(a,b){
    print(a + b);
    // a e b são dynamic
}

main() {
    soma(2,3);
}

void soma2(int a, int b){
    print(a + b);
    // a e b são int
}
```

### Checar Tipagem

```dart
main() {
  var n1 = 2;
  var n2 = 4.56;
  var t1 = "Texto";
  // t1 = 3;

  print(n1 + n2); // 6.56

  print(n1.runtimeType); // int
  print(n2.runtimeType); // double
  print(t1.runtimeType); // String

  print(n1 is int); // true
  print(n1 is String); // false
}
```

### Diferença entre `const` e `final`

https://qastack.com.br/programming/50431055/what-is-the-difference-between-the-const-and-final-keywords-in-dart

Se você tem uma variável que é inicializada e não vai alterar há duas formas de fazer isso:

+ **final** se o valor dela for algo já pré-defido. Exemplo: `PI = 3.14`
+ **const** para quando algo é atribuido mas nâo muda e isso ocorre em tempo de execução. `nome = stdin.readLine()`



**Final:**

> "final" significa atribuição única: uma variável ou campo final *deve* ter um inicializador. Depois de atribuído um valor, o valor de uma variável final não pode ser alterado. final modifica *variáveis* .

------

**Const:**

> "const" tem um significado um pouco mais complexo e sutil no Dart. const modifica *valores* . Você pode usá-lo ao criar coleções, como const [1, 2, 3], e ao construir objetos (em vez de novos) como const Point (2, 3). Aqui, const significa que todo o estado profundo do objeto pode ser determinado inteiramente em tempo de compilação e que o objeto será congelado e completamente imutável.
>
> Os objetos Const têm algumas propriedades e restrições interessantes:
>
> Eles devem ser criados a partir de dados que podem ser calculados em tempo de compilação. Um objeto const não tem acesso a nada que você precisaria calcular em tempo de execução. 1 + 2 é uma expressão const válida, mas o novo DateTime.now () não é.
>
> Eles são profundamente, transitivamente imutáveis. Se você tiver um campo final contendo uma coleção, essa coleção ainda poderá ser mutável. Se você tem uma coleção const, tudo nela também deve ser const, recursivamente.
>
> Eles são *canonizados* . É como uma cadeia interna de strings: para qualquer valor const constante, um único objeto const será criado e reutilizado, não importando quantas vezes as expressões const sejam avaliadas.

**Const:**
se o valor que você possui é calculado em tempo de execução ( `new DateTime.now()`por exemplo), **não** é possível usar uma const para ele. No entanto, se o valor for conhecido em tempo de compilação ( `const a = 1;`), você deverá usar `const`over `final`. Existem outras 2 grandes diferenças entre `const`e `final`. Em primeiro lugar, se você estiver usando `const`, você deve declará-lo como algo que `static const`não apenas `const`. Em segundo lugar, se você tiver uma `const`coleção, tudo dentro dela está incluído `const`. Se você tem uma `final`coleção, tudo dentro dela **não** é `final`.

**Final:**
`final` deve ser usado novamente `const`se você não souber o valor no tempo de compilação e será calculado / obtido no tempo de execução. Se você deseja uma resposta HTTP que não possa ser alterada, se deseja obter algo de um banco de dados ou se deseja ler de um arquivo local, use `final`. Qualquer coisa que não seja conhecida no momento da compilação deve `final`terminar `const`.

------

Com tudo isso dito, ambos `const`e `final`não podem ser reatribuídos, mas os campos em um `final`objeto, desde que não sejam `const`ou `final`possam ser reatribuídos (ao contrário `const`).

## Operadores

### Operadores comuns

Aritméticos

+, -, *, /, %

Lógicos

&& (AND), || (OR), ^ (XOR), ! (NOT

Atribuição

=, +=, -=, *=, /=, %=

Relacionais

\>, <, <=, >=, ==, !=

Operadores unários

a++, ++a, --a, a--

### Operador Ternário

Sinxtax

```
ternary_result = bolol_operation ? if_true : if_false
```

```dart
String estaChovendo = reposta == true ? 'estaChovendo' : 'naoEstaChovendo'
```

### Operador Spread `...`

Como em JS é um operador que permite espalhar elementos dentro de uma lista. Assim você consegue adicionar os elementos de uma lista  dentro de outra lista de uma forma mais fácil.

## String

### Interpolação

Feita na própria string usando `$` para variáveis

Podemos usar `${}` para interpretar mais coisas ainda, como expressões

```dart
String nome = 'João';
String status = 'aprovado';
double nota = 9.2;

String frase1 = nome + " está " + status + " pq tirou nota " + nota.toString() + "!";

// somente variáveis
String frase2 = "$nome está $status pq tirou nota $nota!";

// operações
print("30 * 30 = ${30 * 30}");
```



## Funções

Função e arrow function

```dart
void exec(int a, int b, int Function(int, int))){
    return fn(a,b)
}

main(){
    final r = exec(2,3, (a,b) {
        return a*b + 200;
    });
    print('o resultado é $r');
}
```

### Parâmetro Nomeados

É como em python. Sabemos que os parâmetros de uma função tem uma ordem que em geral para todas as linguagens de programação deve ser seguido a risca. Porem, pondo os parâmetros de forma nomeado você consegue evitar essa ordem.



Para fazer isso, os parâmetros da função deve está dentro de chaves `{}`

```dart
main() {
    // Perceba que pude inverter os parâmetros
  saudarPessoa(nome: "João", idade: 33);
  saudarPessoa(idade: 47, nome: "Maria");

  imprimirData(7);
  imprimirData(7, ano: 2020);
  imprimirData(7, ano: 2021, mes: 12);
}

saudarPessoa({String nome, int idade}) {
  print("Olá $nome nem parece que vc tem $idade anos.");
}

imprimirData(int dia, {int ano = 1970, int mes = 1}) {
  print('$dia/$mes/$ano');
}
```



### Parâmetros Opcionais

Usamos com `[]` que deverá ser os último parâmetros

```dart
import 'dart:math';

main() {
  int n1 = numeroAleatorio(100); // 7
  print(n1);
  
  int n2 = numeroAleatorio(); // 2
  print(n2);

  imprimirData(10, 12, 2020); // 10/12/2020
  imprimirData(10, 12); // 10/12/1970
  imprimirData(10); // 10/1/1970
}

int numeroAleatorio([int maximo = 11]) {
  return Random().nextInt(maximo);
}

imprimirData(int dia, [int mes = 1, int ano = 1970]) {
  print('$dia/$mes/$ano');
}
```

### map, reduce e filter

map: mapeia valores da lista para outro aplicando uma função. `map`

reduce: Uma função que acumula um valor ao passar por cada valor da lista, isso diminui e no final fica só um `reduce`

filter: feito com `where`. diminui a lista a apartir de uma função que retorne `true` ou `false` para a permanência do elemento

```dart
main() {
  var notas = [8.2, 7.1, 6.2, 4.4, 3.9, 8.8, 9.1, 5.1];

  // Funções como variáveis (como arrow function)
  var notasBoasFn = (double nota) => nota >= 7;
  var notasMuitoBoasFn = (double nota) => nota >= 8.8;

  // Aplicando FILTER
  var notasBoas = notas.where(notasBoasFn);
  var notasMuitoBoas = notas.where(notasMuitoBoasFn);
  
  print(notas);
  print(notasBoas);
  print(notasMuitoBoas);
}
```

map e reduce

```dart
main() {
  var alunos = [
    {'nome': 'Alfredo', 'nota': 9.9},
    {'nome': 'Wilson', 'nota': 9.3},
    {'nome': 'Mariana', 'nota': 8.7},
    {'nome': 'Guilherme', 'nota': 8.1},
    {'nome': 'Ana', 'nota': 7.6},
    {'nome': 'Ricardo', 'nota': 6.8},
  ];

  var notasFinais = alunos
    .map((aluno) => aluno['nota'])
    .map((nota) => (nota as double).roundToDouble())
    .where((nota) => nota >= 8.5);
  print(notasFinais); // Notas dos alunos maiores que 8.5 após arredondar (10, 9, 9). VÁRIOS MAP e depois um FILTER

  var total = notasFinais.reduce((t, a) => t + a);
  print(total); // somar tudo. REDUCE

  print("O valor da média é: ${total / notasFinais.length}."); // 9.333
}
```



### Obs

**SE NÃO POR O TIPO NO ARGUMENTO, ENTÃO SEU TIPO SERÁ `dynamic`**

## Classes

Classe define um tipo personalizado. Tudo em dart é uma classe.

Podemos criar uma nova instância com o operador `new` ou sem ele.

### Construtor

Como o Java mas tem algumas coisa adicionais que torna o desenvolvimento melhor.

Há uma forma diferente de criar um construtor Default que ja atribui logo os valores aos atributos da classe

```dart
// CONSTRUTOR - FORMA ANTIGA
Data(int dia, int mes, int ano) {
	this.dia = dia;
	this.mes = mes;
	this.ano = ano;
}

// CONSTUTOR = FORMA NOVA
Data(this.dia, this.mes, this.ano)

// CONSTRUTO = Parâmetro Opicionais e agora pode ser fora de ordem
Data([this.dia = 1, this.mes = 1, this.ano = 1970]);
```

### Construtor nomeado

Como há o caso do construtor poder ser fora de ordem então **NÃO HÁ SOBRECARGA DO MÉTODO CONSTRUTOR.** Para conseguir fazer isso podemos fazer por **CONSTUTORES NOMEADOS**



```dart
Data.com({this.dia = 1, this.mes = 1, this.ano = 1970});

Data.ultimoDiaDoAno(this.ano) {
    dia = 31;
    mes = 12;
  }

// Chamando

new Data.com(ano: 2022)
```

Exemplo de classe em dart

`````dart
class Produto {
    String nome;
    double preco;

    Produto(String nome, double preco){
        this.preco = preco;
        this.nome = nome;
    }
}

main() {
    var p1 = new Produto();
    p1.nome = 'Lapis';
    p1.preco = 4.59;

    print("o produto ${p1.nome} tem o preço de R\$${p1.preco})
}
````

Classe deve ter nome Maiúsculo em CamelCase

Defininido uma classe num arquivo

### atributo privado e getter e setter

Nâo há um classificar de visibilidade nem para métodos e nem para atributo.

A FORMA DE DEIAXR PRIVADO É POR O UNDERLINE `_` NO INICIO DO ATRIBUTO OU MÉTODO.

Getter e setter: são funçôes untanto diferente:

+ Elas tem o mesm nome da variavel sem o `_`
+ Não coloca os `()` nessa função

```dart
class Carro {
  final int velocidadeMaxima;
  // ATRIBUTO PRIVADO
  int _velocidadeAtual = 0;

  Carro([this.velocidadeMaxima = 200]);

  // GETTER
  int get velocidadeAtual {
    return this._velocidadeAtual;
  }

  // SETTER
  void set velocidadeAtual(int novaVelocidade) {
    bool deltaValido = (_velocidadeAtual - novaVelocidade).abs() <= 5;
    if(deltaValido && novaVelocidade >= 0) {
      this._velocidadeAtual = novaVelocidade;
    }
  }
}
```

Usando essa classe

```dart
import '../modelo/data.dart';

main() {
  var dataAniversario = new Data(3, 10, 2020);

  Data dataCompra = Data(1, 1, 1970);
  // dataCompra.dia = 23;
  dataCompra.mes = 12;
  dataCompra.ano = 2021;

  String d1 = dataAniversario.obterFormatada();

  print("A data do aniversário é $d1");
  print("A data da compra é ${dataCompra.obterFormatada()}");

  print(dataCompra);
  print(dataCompra.toString());

  print(new Data());
  print(Data(31));
  print(Data(31, 12));
  print(Data(31, 12, 2021));

  print(Data.com(ano: 2022));

  var dataFinal = Data.com(dia: 12, mes: 7, ano: 2024);
  print("O Mickey será público em $dataFinal");

  print(Data.ultimoDiaDoAno(2023));
}
```

### Herança

```dart
class Animal {
  String cor;
  void dormir(){
    print("Dormir");
  } 
}

class Cao extends Animal { 
  String corOrelha;
  void latir(){
    print("Latir");
  }
}

class Passaro extends Animal {
  String corBico;
  void voar(){
    print("Voar");
  }
}

void main() {
  
  Cao cao = Cao();
  Passaro passaro = Passaro();
  
  cao.cor = "Branco";
  cao.corOrelha = "Preto";
  print( "Cor do cão: " + cao.cor );
  print( "Cor da orelha: " + cao.corOrelha );
  cao.latir();
  
  passaro.cor = "Marrom";
  print( passaro.cor );
  passaro.voar();
  
  
}
```



## Generics

É especificar o tipo de um tipo composto, com `<>`

```dart
 
main() {
  print('Início');

  List<String> frutas = ['banana', 'maça', 'laranja'];
  frutas.add('melão');

  print(frutas);

  Map<String, double> salarios = {
    'gerente': 19345.78,
    'vendedor': 16345.80,
    'estagiário': 600.00,
  }; 

  print(salarios);
}
```

### Sobrepor métodos: `@override`

**Não vale com o método construtor**

```dart
class Animal {
  
  String cor;
  
  Animal(this.cor);
  
  void dormir(){
    print("Dormir");
  } 
  
  void correr(){
    print("Correr ");
    print("como");
    print("um");
  }
  
}

class Cao extends Animal { 
  String corOrelha;
  
  Cao(String cor, this.corOrelha) : super(cor);
  
  void latir(){
    print("Latir");
  }
  
  @override //sobrepor
  void correr(){
    super.correr();
    print(" cão!");
  }
  
}

class Passaro extends Animal {
  String corBico;
  
  Passaro(String cor, this.corBico) : super(cor);
  
  void voar(){
    print("Voar");
  }
  
  @override //sobrepor
  void correr(){
    super.correr();
    print(" passaro!");
  }
  
}

void main() {
  
  Cao cao = Cao("Marrom", "Branco");
  Passaro passaro = Passaro("Vermelho", "Amarelo");
  
  print( "Passaro cor: ${passaro.cor} corBico: ${passaro.corBico} " );

  
}
```

### Classe abstrata

Igual ao Java

```dart
abstract class Animal {
  
  String cor;
  
  void correr();
  
}

class Cao extends Animal {
  
  @override
  void correr(){
    print("Correr");
  }
  
  void latir(){
    print("Latir");
  }
}

class Passaro extends Animal {
  @override
  void correr(){
    print("Correr");
  }
  void voar(){
    print("Voar");
  }
}

void main() {
  
  /*
   * abstrata
   * concreta
   * */
  
  Cao cao = Cao();
  cao.latir();
  cao.correr();

}
```

### Interface

Pode-se dizer, a grosso modo, que uma 	interface é um contrato que quando assumido por uma classe deve ser implementado

Interface é utilizada pois podemos ter muitos objetos (classes) que podem possuir a mesma ação (métodos), porém, podem executá-las de maneiras diferentes.

A partir de interface obriga-se a uma classe que o implementa a ter certos métodos. 

Em Flutter a interface é uma classe abtrata, sem variável que é chamad apartir de implement (enquanto que a herança de classe abtrata é por extends)

```dart
abstract class Presidenciavel {
  void participarEleicao();
}

abstract class Jornalismo {
  void escreverArtigoJornal();
}

abstract class Cidadao {
  void direitosDeveres(){
    print("Todo cidadão tem direitos e deveres");
  }
}

class Obama extends Cidadao 
  implements Presidenciavel, Jornalismo  {
  
  @override
  void escreverArtigoJornal(){
    print("Escrever artigo Jornal");
  }
  
  @override
  void participarEleicao(){
    print("Eleição nos Estados Unidos para o Obama");
  } 
  
}

class Jamilton extends Cidadao {
  
}

void main() {
	
  Obama obama = Obama();
  obama.direitosDeveres();
  obama.participarEleicao();
  
  Jamilton jamilton = Jamilton();
  jamilton.direitosDeveres();
  
  
}
```

### Mixins

usamos `with` para usar um mixin

O mixin é uma forma de vocÊ adicionar um método ou variável a uma classe por apenas chamalo com `with`

Diferente de classe abstrata ou interface, você nâo precisa redefinir e nem fazer um `override`

**Mixin é um techo de código que vocÊ quer executar em determinadas classes**

```
/* 
Mixins é uma maneira de utilizar códigos em múltiplas hierarquias de classes
*/

abstract class Presidenciavel {
  void participarEleicao();
}

abstract class Jornalismo {
  void escreverArtigoJornal();
}

mixin Escrita {
  
  void escreverArtigoJornal(){
    print("Escrever um artigo para o Jornal");
  }
  
}

abstract class Cidadao {
  void direitosDeveres(){
    print("Todo cidadão tem direitos e deveres");
  }
}

class Obama extends Cidadao 
  implements Presidenciavel, Jornalismo  {
  
  @override
  void escreverArtigoJornal(){
    print("Escrever artigo Jornal");
  }
  
  @override
  void participarEleicao(){
    print("Eleição nos Estados Unidos para o Obama");
  } 
  
}

class Jamilton extends Cidadao with Escrita {
  
}

void main() {
	
  Obama obama = Obama();
  obama.direitosDeveres();
  obama.participarEleicao();
  
  Jamilton jamilton = Jamilton();
  jamilton.direitosDeveres();
  // Perceba que jamilton nâo tem esse método na sua classe, mas ele está com o mixin e por isso pode ser e xecutado
  jamilton.escreverArtigoJornal();
  
  
}
```



## import

De preferencia, quando o  arquivo `.dart` for classe, ela deve está sozinha e asism vocÊ consegue importar ela

```dart
import '../modelo/carro.dart';
```

## Decorators

### `@required`

````dart
class Questionario extends StatelessWidget {
  final List<Map<String, Object>> perguntas;
  final int perguntaSelecionada;
  final void Function() quantoResponder;

    // parametro nomeados 9podem ser usados em qualquer hora
  Questionario({
      // @required: decolrador default do dart que oprbirga que ocomponente receba estes atributos
    @required this.perguntas,
    @required this.perguntaSelecionada,
    @required this.quantoResponder,
  });
````

