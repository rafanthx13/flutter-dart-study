# Widgets e Primeiro Projeto

Primeiro Projeto da Cod3er : App de Pergunta

Tudo em flutter é uma árvore de widgets, Há raiz e essa tem outros.

## Detalhes de Dart

Como estamos em dart nâo precisa de retorno para a classe

**VS CODE**
+ CTRL+SPACE : MOstra osparâmetros nomeados par aum método

+ `CTRL`+`.` : Opçôes para o flutter sobre um trecho delimitado de código

  + Se aplicar no Widget pergunta se quer importar Material o CUpertino
  + Você pode usar `CTRL + .` para envolver Widgets com outros Widgests

+ `st` + auto-complete em `Flutter stateless widget`

  

## Template de novo widget gerado pelo VS Code

```dart
import 'package:flutter/material.dart';

class Questao extends StatelessWidget {	
	@override
	Widget build(BuildContext context) {
		return Contain(
            
		);
	}
}
```





## Como começar em Flutter - Primeiro widget

Em dart a primeira coisa a ser chamada é a função main



````dart
main() {

}
````

Vamos criar o primeiro widget, ele em geral terá o nome da minha aplicação e ele retorna um widget. 

O StatelessWidget deve implementar o método build() que retona um Widget no caso o MaterialApp

````dart

import 'package:flutter/material.dart';
main(){
	runApp(new PerguntaApp());
}

class PerguntaApp extends StatelessWidget {

	@override
	Widget build(BuildContext context){
		return MaterialApp(
			// parâmetro nomeado
			home: Text('Ola flutter'),
		);
	}
	
}
````

Scaffold:  a traduçâo seria estrutura. 
Text é outro Widget

````dart

import 'package:flutter/material.dart';
main(){
	runApp(new PerguntaApp());
}

class PerguntaApp extends StatelessWidget {

	@override
	Widget build(BuildContext context){
		return MaterialApp(
			home: Scaffold(
				appBar: AppBar(
					title: Text('Perguntas'),
				),
				body: Text('Olá Flutter')
			),
		);
	}
	
}
````

## Visibilidade de Widget

Assim como as tagas HTML os Widgets podem ser visíveis ou invisíveis

Visíveis: EM geral são de IO
+ RaisedButton(), Text(), Card()
+ Aqueles que o usuário vê

Invisíveis: EM geral para Layout e Controler
+ Row(), COlumn, ListView()
+ Widgetspara estruturar a aplicação. Ele é viível inderetamente como uma div que encapsula vários elementos

Há também o Widget Container() que pode ou nâo ser visíveil a depender do dev.

COntinuando vamos por no body o widget invisível de esturarua Column

````dart

import 'package:flutter/material.dart';

main(){
	runApp(new PerguntaApp());
}

class PerguntaApp extends StatelessWidget {

	@override
	Widget build(BuildContext context){

		final perguntas = [
			'abc',
			'def'
		]



		return MaterialApp(
			home: Scaffold(
				appBar: AppBar(
					title: Text('Perguntas'),
				),
				body: Column(
					children: <Widget>[
						Text(perguntas[0]),
						RaisedButton(
							child: Text('Receita 2'),
							onPressed: null,
						),
					],
				)
			),
		);
	}
	
}
````

### Disparar método ao clicar um botão

O método fica dentro da classe.

O `onPressed` de `RaisedButton` tem como parâmetro uma função. Porém não é executar uma funçâo e sim passar uma funçâo como parâmetro. Então, vamos passar a funçâo como parâmetro. Ou passo somente uma arrow function

````dart

import 'package:flutter/material.dart';

main(){
	runApp(new PerguntaApp());
}

class PerguntaApp extends StatelessWidget {

	void responder(){
		print('Pergunta respondidad')
	}

	@override
	Widget build(BuildContext context){

		final perguntas = [
			'Qual é a sua cor favorita?',
			'Qual é o seu animal preferido?'
		]



		return MaterialApp(
			home: Scaffold(
				appBar: AppBar(
					title: Text('Perguntas'),
				),
				body: Column(
					children: <Widget>[
						Text(perguntas[0]),
						RaisedButton(
							child: Text('Receita 2'),
							onPressed: responder,
						),
					],
				)
			),
		);
	}
	
}
````

Pordeira ser
````dart

() {
	print('Reposta 2')
}

ou

() => print('Resposta2')
````

## Widget Stateless e StateFull

quando vocÊ exetende um StateLess, ele nâo deverá ter um estado interno. Entao, as vaiaveis dentro dele devera ser  do tipo final no mínimo para respeitsr isso.

O estado sâo as informaçôes inteernas da App.

Stateless
+ A partir dos parametro de entrada contruo o meu widget e renderizo na UI


Stateful
+ A mesma coisa do StteLess mas aqui ele rendeinrza a nas mudança itneras

A diferença é que o WIdget Statefull muda toda vez que seu estado intero mude

### Convertendo para StatefulState

Para isso, de stateles para stateful (trocamos a herança)

```
extends StatelessWidget => extends StatefulWidget
```

O estado do meu Widget Steteful é uma classe **SEPARADA** que extende  a classe do Widget. Nele ficara tudo que estava no Widget Stateless.

Agora para **PARA ATUALIZAR UM STATE DEVEMOS USAR DENTRO DA FUNÇÃO `setState()`**. Para ficaer reativo e a UI reagir.

**OBS**: Nesse caso movemos tudo porque as coisas que estão no método `build()` dependem do estado

```dart
class PerguntaAppState extends State<PerguntaApp> {
    var perguntaSelecionada = 0;
    
    void responder() {
        setState( () {
           perguntaSelecionada++; 
        });
        print(perguntaSelecionada)
    }
    
    
	@override
	Widget build(BuildContext context){

		final perguntas = [
			'Qual é a sua cor favorita?',
			'Qual é o seu animal preferido?'
		]



		return MaterialApp(
			home: Scaffold(
				appBar: AppBar(
					title: Text('Perguntas'),
				),
				body: Column(
					children: <Widget>[
						Text(perguntas[0]),
						RaisedButton(
							child: Text('Receita 2'),
							onPressed: responder,
						),
					],
				)
			),
		);
	}
    
    
}
```

No Widget Stateful agora, temos que criar o método `createState` para criar o estado quando chamarmos

```dart
class PerguntaApp extends StatefulWidget {
	return new PerguntaAppState();
}
```

**Flutter é cirúrgico**

o flutter renderiza apenas o que mudou nao uma dezena de componentes

## Tornando as coisa privadas

Como em dar, para algo ser privado deverá começar com underline `__`.

Fica privada para quem é fora do arquivo/classe

para acessar devemos criar os métdos getter e setter e os uso como um atributo

Exemplo do dart:

```dart
class Pessoa {
	String nome;
	String _cpf;
	
	set cpf(String cpf){
		this._cpf = cpf;
	}
	
	get cpf {
	return _cpf
	}
}

main() {
	var p1 = Pessoa();
	p1.nome = "anome";
	p1.cpf = "123.456.789"; // setter
	print('O ${p1.nom} tem CPF ${p1.cpf}'); // getter
}
```

Da nossa antiga clase podemos transformar as classes e seus métodos e variáveis internos em privado colocando `_` na frente.

Em privado significa que é visível somente dentro do arquivo

## Container em Flutter

O container tem como em CSS como o box-model

+ Margin > Border > Padding > child (o conteudo)

  

Criando novas classes

**Questão:** Texto central

```dart
import 'package:flutter/material.dart';

class Questao extends StatelessWidget {
  final String texto;

  Questao(this.texto);

  @override
  Widget build(BuildContext context) {
    return Container(
      width: double.infinity, // O comprimento é o máximo
      margin: EdgeInsets.all(10),  //margin em todas as direções
      child: Text(
        texto, // o texto em si
        style: TextStyle(fontSize: 28), //font
        textAlign: TextAlign.center, // alinhamento
      ),
    );
  }
}
```

Respota: Será um botão que executará o método `quandoSelecionado()`  que será uma `callback()` passada

```dart
import 'package:flutter/material.dart';

class Resposta extends StatelessWidget {
  final String texto;
  final void Function() quandoSelecionado;

  Resposta(this.texto, this.quandoSelecionado);

  @override
  Widget build(BuildContext context) {
    return Container(
      width: double.infinity,
      child: RaisedButton(
        textColor: Colors.white,
        color: Colors.blue,
        child: Text(texto),
        onPressed: quandoSelecionado,
      ),
    );
  }
}
```

## Como está agora a `man.dart`

Após deixar provada e seprar alguns componentes, vamos ter

```dart
import 'package:flutter/material.dart';
import './questao.dart';
import './resposta.dart';

main() => runApp(PerguntaApp());

class _PerguntaAppState extends State<PerguntaApp> {
  var _perguntaSelecionada = 0;

  void _responder() {
    setState(() {
      _perguntaSelecionada++;
    });
  }

  @override
  Widget build(BuildContext context) {
    final perguntas = [
      'Qual é a sua cor favorita?',
      'Qual é o seu animal favorito?',
    ];

    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: Text('Perguntas'),
        ),
        body: Column(
          children: <Widget>[
            Questao(perguntas[_perguntaSelecionada]),
            Resposta('Resposta 1', _responder),
            Resposta('Resposta 2', _responder),
            Resposta('Resposta 3', _responder),
          ],
        ),
      ),
    );
  }
}

class PerguntaApp extends StatefulWidget {

  _PerguntaAppState createState() {
    return _PerguntaAppState();
  }
}
```

Em vez de perguntas ser uma lista vamos por como u

```
final List<Map<String, Object>> perguntas = [
      {
        'texto': 'Qual é a sua cor favorita?',
        'respostas': ['Preto', 'Vermelho', 'Verde', 'Branco'],
      },
      {
        'texto': 'Qual é o seu animal favorito?',
        'respostas': ['Coelho', 'Cobra', 'Elefante', 'Leão'],
      },
      {
        'texto': 'Qual é o seu instrutor favorito?',
        'respostas': ['Maria', 'João', 'Leo', 'Pedro'],
      }
    ];
```

Converter Textos de repsostas em  novos WIdgets

+ Faço um `map `sobre cada resposta mapeadno para widgets e colocando tudo dentro duma lista

```dart
...respostas.map((t) => Resposta(t, _responder)).toList()
```

A geraçâo de Widgets é como um objeto. VOcÊ pode até mesmo usar condicionais ternários no meio da definiçâo de um Widget pai para dcidir se cria um objeto ou não

```dart
......
    
    void _responder() {
    if(temPerguntaSelecionada) {
      setState(() {
        _perguntaSelecionada++;
      });
    }
  }

bool get temPerguntaSelecionada {
    return _perguntaSelecionada < _perguntas.length;
}

@override
  Widget build(BuildContext context) {
    List<String> respostas = temPerguntaSelecionada 
      ? _perguntas[_perguntaSelecionada]['respostas']
      : null;

    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: Text('Perguntas'),
        ),
        body: temPerguntaSelecionada ? Column(
          children: <Widget>[
            Questao(_perguntas[_perguntaSelecionada]['texto']),
            ...respostas.map((t) => Resposta(t, _responder)).toList(),
          ],
        ) : Center(
                child: Text(
                  'Parabéns!',
                  style: TextStyle(fontSize: 28),
                ),
              ),
      ),
    );
  }
......
```

## Quando criar componentes

Como está nossa aplicaçâo

+ PerguntaApp

  + Scaffold

    + Column: 

      + 1 PErgunta e 4 repostas

    + Center : A resposta que é mostrada com condicional quando acabar as perguntas

      

Em geral, busque ter componentes pequenos e principalmente componentes que sejam reutilizáveis. Use também o bom senso junto com a separaçâo de funçôes e momento para cada componente.

Por exemplo, agora, vamos criar dois components 1 que vai englobar o COlumn e outro o Center para separar melhor o que é **QUESTIONÁRIO** e o que é **TELA DE FIM**