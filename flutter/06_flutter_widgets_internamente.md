# Flutter e Widgets Internamente

Como o flutter funciona intermanete

Escrever bons codigos em flutter

recursos avançados em flutter e dart

## Como o flutter funciona intermanete

FLutter trabalha 60 vezes por segundo. É um FrameWOrk extremamente raído por ***NÂO RECRIAR A ÁRVORE 60 VEZES POR SEGUNDO**.

O metodo build executa em cascata, o da raiz chama todos até a folha. Isso faz parecer que a medida que colocamos novos componentes o custo crsce de forma exponencial, porem, em flutter há diversos algortimos modernod que faz qcom que essa execuçâo em cascata fique otimizada ficando proximo de uma complexidade linear

### Árvore de FLutter

Tem um nivel de composiçâo agressiva, ou seja, é uma composiçâo de vários componentes. Por conta disso a disposiçâo dos elementos da árvore deve ser utimizada

Widget Tree
+ Componentes imutáveis
+ é onde tem o método `build()
+ É reconstruida Frequentemente ou seja É renderizada a toda hora
+ **imutável e reconstruida?** Acontece que a estrutura lógica da árvore ` Container -> Column -> MyStateless` nâo muda, nunca, mas se algo muda na tela, a partirdo componete marcado como sujo por ter mudançaa de estdo, toda a árvore a partir daquele ponto é reconstruida **COM A MESMA ESTRUTUTRA**


Element Tree
+ Estrutura lógica da árvore de componente(é onde fica o `state` de componentes `Stateful`)
+ Nâo é renderizada com tanta frequencia
+ Liga o Widget com o Objeto renderizao (raramente reconstruido) é o que faz a ponte entre os Widgets e os objetos de renderizaçâo

Render Tree
+ É o que vocÊ ver na tela, é o objeto que escrreve cada pixel da pela
+ É o Objeto  renderizado na tela
+ raramente recosntruido
+ é o `renderObject` do deveTools

![](/home/rhavel/Documentos/Personal Projects/flutter-dart-study/flutter/imgs/img-001.png)

![](/home/rhavel/Documentos/Personal Projects/flutter-dart-study/flutter/imgs/img-002.png)

### Quando o método `build()` é chamado

**1.inicializaçao**

Quando monta a tela numa primeira vez. O primeiro WIdget, o raiz, chama o build que vai chamar o restante dos `build()` da árvore de componentes

**2.mudança de estado em StateFul**

Quando  o métpdp `setState()` for invocado. QUando o estado é alterado, o compoentne é marcado como sujoe e no proximo ciclo de frame o elemento é recriado e altera os pixels na tela.

**3.MediaQuery.of()**

Quando algo dependenda tela

ex:

`MediaQuery.of(context).size.width > 480`

Qundo a tela mudar, isso será verificado e talvez será chamado o médodo `build()`

## COnstruir codigos melhores

![](/home/rhavel/Documentos/Personal Projects/flutter-dart-study/flutter/imgs/img-003.png)

#### final e constant

final
+ É uma constante em tempo de execuçâo, ou seja, o valor não muda apos ser setada mas a cada execuçâo do codigo esse valor pode ser diferente. É uma variavel que nâo foi conhecida antes de compilar o código

const
+ Pra usar const é necessário ser um valor literal, ou seja, um numero ou uma string (escrita de forma direta), pois é necessário saber o valor na hora de compilar o código.

### construtores constantes: otimizaçâo

É possíel fazer um contrutotr constante se as variaveis internas forem todas final. Aí, o widget é constante e o flutter opera de forma otimizada sobre ele.

**VOCÊ USA UM WIDGET CONSTANTE SE SEUS PARAMERTOS FOREM CONEHCIDOS EM TEMPO DE COMPILAÂO.

Ex: WIdget TExt

Text("Excluir")

O parametro é semrpe o mesmo entoa pode ficar como

const TExt('execluit")

A otimizaçâo que isso caiusa é que ao reescrbvre toda a árvore **O WIDGET CONSTATEN NÂO SERÁ RECRIADO**. ENtaô, da pra usar se os parametros forem fixos e for stateless

## Cico de Vida dos Widgets

**stateless**

COnstruto => build()

há o constturto que vai direot par ao build

**stateful**

Construtor => initState() => build() => setState() => didUpdateWidget() => build() => dispose()

initState(0: Serve para inicializar o estado como fazer uma chamada do BackEnd antes de criar o cmpnentes

didUpdateWidget(): Caso o estado mudar esse método é chamado, esse método recebe o estado antigo e novo

dispose(): quando o widget é excluido

Ex

````dart
class _TransactionFormState extends State<TransactionForm> {
  final _titleController = TextEditingController();
  final _valueController = TextEditingController();
  DateTime _selectedDate = DateTime.now();

  _TransactionFormState() {
    print('Constructor _TransactionFormState');
  }

  @override
  void initState() {
    super.initState();
    print('initState() _TransactionFormState');
  }

  @override
  void didUpdateWidget(TransactionForm oldWidget) {
    super.didUpdateWidget(oldWidget);
    print('didUpdateWidget() _TransactionFormState');
  }

  @override
  void dispose() {
    super.dispose();
    print('dispose() _TransactionFormState');
  }

  _submitForm() {
    final title = _titleController.text;
    final value = double.tryParse(_valueController.text) ?? 0.0;

    if (title.isEmpty || value <= 0 || _selectedDate == null) {
      return;
    }

    widget.onSubmit(title, value, _selectedDate);
  }

  @override
  Widget build(BuildContext context) {
	...
}
}
````

### Clico de vida de uma aplicaçâ dart

![](/home/rhavel/Documentos/Personal Projects/flutter-dart-study/flutter/imgs/img-004.png)

Para percebemos a mudança de estaod da aplicaçâo vamos usar um MIxin (mixin é como colar um trecho de codigo). Fazmoe isos importando o mixin `with WidgetBididingObserver`.



````dart
@override
  void didChangeAppLifecycleState(AppLifecycleState state) {
    print(state);
  }
````


Depois registramos nosso widget para poder observar essa mudança em `WidgetsBinding.instance.addObserver(this)`

````dart
@override
  void initState() {
    super.initState();
    WidgetsBinding.instance.addObserver(this);
  }
````

e removo esse observado do `dipose()`.

Agora se eu iniciar a aplicaçâo vai mostrar o status da aplicaçâo

inactive: se nao houver nehuama entrada do usaurio
paused: se eu saio da aplicaçâo e deixo ela em background ela fica em paused
resumed: ao entrar ou voltar na aplicaçâo


codigo compelto

````dart
lass _MyHomePageState extends State<MyHomePage> with WidgetsBindingObserver {
  final List<Transaction> _transactions = [];
  bool _showChart = false;

  @override
  void initState() {
    super.initState();
    WidgetsBinding.instance.addObserver(this);
  }

  @override
  void didChangeAppLifecycleState(AppLifecycleState state) {
    print(state);
  }

  @override
  void dispose() {
    super.dispose();
    WidgetsBinding.instance.removeObserver(this);
  }


  List<Transaction> get _recentTransactions {
    return _transactions.where((tr) {
      return tr.date.isAfter(DateTime.now().subtract(
        Duration(days: 7),
      ));
    }).toList();
  }

  _addTransaction(String title, double value, DateTime date) {
    final newTransaction = Transaction(
      id: Random().nextDouble().toString(),
      title: title,
      value: value,
      date: date,
    );

    setState(() {
      _transactions.add(newTransaction);
    });

    Navigator.of(context).pop();
  }

  _removeTransaction(String id) {
    setState(() {
      _transactions.removeWhere((tr) => tr.id == id);
    });
  }

  _openTransactionFormModal(BuildContext context) {
    showModalBottomSheet(
      context: context, 
      builder: (_) {
        return TransactionForm(_addTransaction);
      },
    );
  }

  Widget _getIconButton(IconData icon, Function fn) {
    return Platform.isIOS
        ? GestureDetector(onTap: fn, child: Icon(icon))
        : IconButton(icon: Icon(icon), onPressed: fn);
  }

  @override
  Widget build(BuildContext context) {
````

## O BuildContext - context

Cada widget tem seu proprio contexto e cada contexto relacionaa com seu pai. A partir de um contexto atual se encontra o contexto do elemento pai, há assim uma intra-comunicação.

O que é ese contexto: meta informaçôes sobre o componente e sua localizaçâo naquele componente na árvore. 

Ele é *SEMPRE** passado no método build mas nem sempre agente o usa (as vezes usa como em MediaQuery)

## InheritedWidget

Ele é um Widget que ajuda entre compunicaçâo masi distantes. Se algo muito abaixo da árvroe precisa de algo muito acima da árvore em vez de passar por ários componente até chegar na folha eu posso usar o `InheritedWidget` que vai do topo para a folha, um tundel direot de comunuicaçâo entre os componenets.

## ListView e Listas com Stetefull e `key`

É necessário usar uma chave par alikar as coisas. Sem isso se vocÊ altera a ordem de uma LisView ou remove/adicona um elemento isso vai embananar se essa listView estiver sendo usado por um estado.

Nem sempre agente acaba usando isso.

Agente adiciona a key no estado para poder mapear corretamente uma lista. usamos um id unico para identificar

`transaction_list`

```dart
ListView.builder(
            itemCount: transactions.length,
            itemBuilder: (ctx, index) {
              final tr = transactions[index];
              return TransactionItem(
                key: GlobalObjectKey(tr), // isso é umpouco infeficiente, o melhor seria ser somente VlaueKey mas se nao funcionar usa esse global
                tr: tr,
                onRemove: onRemove,
              );
            },
          );
```


`transaction_item`

````dart
import 'dart:math';

import 'package:flutter/material.dart';
import '../models/transaction.dart';
import 'package:intl/intl.dart';

class TransactionItem extends StatefulWidget {
  final Transaction tr;
  final void Function(String) onRemove;

  const TransactionItem({
    Key key,
    @required this.tr,
    @required this.onRemove,
  }) : super(key: key); // estamos chamando o construtor da classe pai. Dessa forma elee passa uma chave para linkar mais corretamnete

  @override
  _TransactionItemState createState() => _TransactionItemState();
}

class _TransactionItemState extends State<TransactionItem> {

  static const colors = [
    Colors.red,
    Colors.purple,
    Colors.orange,
    Colors.blue,
    Colors.black
  ];

  Color _backgroundColor;

  @override
  void initState() {
    super.initState();

    int i = Random().nextInt(5);
    _backgroundColor = colors[i];
  }

  @override
  Widget build(BuildContext context) {
    return Card(
      elevation: 5,
      margin: EdgeInsets.symmetric(
        vertical: 8,
        horizontal: 5,
      ),
      child: ListTile(
        leading: CircleAvatar(
          backgroundColor: _backgroundColor,
          radius: 30,
          child: Padding(
            padding: const EdgeInsets.all(6),
            child: FittedBox(
              child: Text('R\$${widget.tr.value}'),
            ),
          ),
        ),
        title: Text(
          widget.tr.title,
          style: Theme.of(context).textTheme.title,
        ),
        subtitle: Text(
          DateFormat('d MMM y').format(widget.tr.date),
        ),
        trailing: MediaQuery.of(context).size.width > 480
            ? FlatButton.icon(
                onPressed: () => widget.onRemove(widget.tr.id),
                icon: const Icon(Icons.delete),
                label: const Text('Excluir'),
                textColor: Theme.of(context).errorColor,
              )
            : IconButton(
                icon: Icon(Icons.delete),
                color: Theme.of(context).errorColor,
                onPressed: () => widget.onRemove(widget.tr.id),
              ),
      ),
    );
  }
}
````

