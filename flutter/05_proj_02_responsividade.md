# Responsividade e Adaptabilidade da UI

Continuando o App de despesa mas agora colocando respnsividade e adapatatividade

## O que é responsividade

É ter a UI bem estruturado para qualquer tela: Tablet, Desktop, PC ou Celular.

É ser capaz de exibir em modo retrato/paissagem ou nâo permitir isso.

## O que é ser adaptavidade

É mudar de acordo com o sistema operacional, no caso de Apps é adaptar coisa do Android para iOS e vice-versa.

Há também que se considerar os sistemas operacionais:
+ Entre o Andorid e iOS há: tema diferentes (Material-Desing / Cupertino) Animaçôes diferentes e FOntes diferentes



**Como fazer isso?**

Na árvore de componente vamos colcoar a opção `if Platform.isIOS` para colocar um componente ou algo diferente, assim haverá duas sub-árvores, uma paara IOS e outra para Android

## Fazendo ajustes de responsabilidade

## Calculando o tamanho da tela com `mediaQuery`

em vez de `heighy: 430` vamos por 

````dart
height: MediaQuery.of(context).size.height * 0.5
````

Usando MEdiaQuery, ele **VAI SE ADAPATAR SE ESTIVER NO MODO PAISAGEM OU RETRATO**

### Fator de Escala de um texto

Quando agente coloca um tamanho de fonte fixa `fontSize: 10` isso não deixa nossa aplciação acessível.

Nas configuraçôes dos aparelhos, na parte de acessibiliadde há uma área que permite você aumentar ou diminuir o texto da fonte.

Se vocÊ deixar a fonte fixa essas configuraçêos nâo afetarâo essa fonte.

Para permitri que mude é necessário usar mediaQUery

`fontSize: 20 * MediaQuery.of(context).textScaleFactor`

textScaleFactor vai pegar esse zoom do aparehlo e aplicar a fonte, assim, se ele pedir para aumentar a fonte do Celular, vai aumentar a fonte do nosso app també.

### Layout Builder

Serve para termos um `context` diferente do default, via `constraints` para que peguemos o contexto do elemento pai e nâo de toda a tela em geral (que é o default)

### definido

Colocando o seguinte código no `build` da classe raiz do nosso projeto proibe que o app mude para um modo retrato, sigando sempre no modo vertical.

````dart

import 'packege:flutter/services.dart'

SystemChrome.setPreferredOrientations({
	DeviceOrientation.portraitUp
});
````

### pegando o tamnaho do teclado

as vezes o teclado vai atrapalhar a área de um frmaulario por xemplo, podemos resolver isso dando um peddaing mas pegando o tamanho deo teclaod.

Pegamos com MediaQuery.of(context).viewInserts.bottom

### Usanod media query para renderrizaçao condicional

trailing: MediaQuery.of(context).size.width > 480
                      ? FlatButton.icon(
                          onPressed: () => onRemove(tr.id),
                          icon: Icon(Icons.delete),
                          label: Text('Excluir'),
                          textColor: Theme.of(context).errorColor,
                        )
                      : IconButton(
                          icon: Icon(Icons.delete),
                          color: Theme.of(context).errorColor,
                          onPressed: () => onRemove(tr.id),
                        ),
                ),

esse mediaquery.f(Context/0 pode ser armaenada numa variavel o u seja, podemos simplificar em `main.dart`se AGENTE USAR MUITO ESSE MÉTODO NUM ARQUIVO

### DETECTAR PLATAFORM

aluns widgrts tem a prop adaptaive que permite mudar o widget de modo andoir d para ios

v
Switch.adaptive(
            //         activeColor: Theme.of(context).accentColor,

OU vocÊ pdoe fazer verificando em  qual plataforma está

````dart
body: bodyPage,
            floatingActionButton: Platform.isIOS
                ? Container()
                : FloatingActionButton(
                    child: Icon(Icons.add),
                    onPressed: () => _openTransactionFormModal(context),
                  ),
            floatingActionButtonLocation:
                FloatingActionButtonLocation.centerFloat,
          );
````

outro ex

````dart
// Essa funçâo retorna um widget de acordo com a plataforma
Widget _getIconButton(IconData icon, Function fn) {
    return Platform.isIOS
        ? GestureDetector(onTap: fn, child: Icon(icon))
        : IconButton(icon: Icon(icon), onPressed: fn);
  }
````

outro

````dart

    final iconList = Platform.isIOS ? CupertinoIcons.refresh : Icons.list;
````

### Componente SafeArea

Componente que nâo permite que seja exibido algo em uma área proibida do aplicativo.

Ex: NO iphone 11 na parte de cima há uma área que nâo é visível (iphone11), com SafeArea o widget cvai mostrar somente depois dessa área.

Para usálo basta envolver o WIdget pelo `SafeArea`

### Clsses Adapatative

Como já deu pra perceber essa questâo de adaptabildade (usar Material ou CUpertino de acordo com o SO) causa muitos Ifs. Uma forma de simplificar é criar um componente menir e deixar dentro dela o condicional de plataforma, assim vai ficar mais legível na hora de usá-lo.

adaptative_button.dart

import 'dart:io';
import 'package:flutter/cupertino.dart';
import 'package:flutter/material.dart';

````dart
class AdaptativeButton extends StatelessWidget {

  final String label;
  final Function onPressed;

  AdaptativeButton({
    this.label,
    this.onPressed,
  });

  @override
  Widget build(BuildContext context) {
    return Platform.isIOS
        ? CupertinoButton(
            child: Text(label),
            onPressed: onPressed,
            color: Theme.of(context).primaryColor,
            padding: EdgeInsets.symmetric(
              horizontal: 20,
            ),
          )
        : RaisedButton(
          color: Theme.of(context).primaryColor,
          textColor: Theme.of(context).textTheme.button.color,
          child: Text(label),
          onPressed: onPressed,
        );
  }
}
````


Agora um TextFIled Adaptative

````dart
import 'dart:io';
import 'package:flutter/cupertino.dart';
import 'package:flutter/material.dart';

class AdaptativeTextField extends StatelessWidget {

  final String label;
  final TextEditingController controller;
  final TextInputType keyboardType;
  final Function(String) onSubmitted;

  // construtor
  AdaptativeTextField({
    this.label,
    this.controller,
    this.keyboardType = TextInputType.text,
    this.onSubmitted,
  });

  @override
  Widget build(BuildContext context) {
    return Platform.isIOS
        ? Padding(
          padding: const EdgeInsets.only(
            bottom: 10,
          ),
          child: CupertinoTextField(
              controller: controller,
              keyboardType: keyboardType,
              onSubmitted: onSubmitted,
              placeholder: label,
              padding: EdgeInsets.symmetric(
                horizontal: 6,
                vertical: 12,
              ),
            ),
        )
        : TextField(
            controller: controller,
            keyboardType: keyboardType,
            onSubmitted: onSubmitted,
            decoration: InputDecoration(
              labelText: label,
            ),
          );
  }
}
````

adaptative_date_picker.dart

````dart
import 'dart:io';
import 'package:flutter/cupertino.dart';
import 'package:flutter/material.dart';

import 'package:intl/intl.dart';

class AdaptativeDatePicker extends StatelessWidget {
  final DateTime selectedDate;
  final Function(DateTime) onDateChanged;

  AdaptativeDatePicker({
    this.selectedDate,
    this.onDateChanged,
  });

  _showDatePicker(BuildContext context) {
    showDatePicker(
      context: context,
      initialDate: DateTime.now(),
      firstDate: DateTime(2019),
      lastDate: DateTime.now(),
    ).then((pickedDate) {
      if (pickedDate == null) {
        return;
      }

      onDateChanged(pickedDate);
    });
  }

  @override
  Widget build(BuildContext context) {
    return Platform.isIOS
        ? Container(
            height: 180,
            child: CupertinoDatePicker(
              mode: CupertinoDatePickerMode.date,
              initialDateTime: DateTime.now(),
              minimumDate: DateTime(2019),
              maximumDate: DateTime.now(),
              onDateTimeChanged: onDateChanged,
            ),
          )
        : Container(
            height: 70,
            child: Row(
              children: <Widget>[
                Expanded(
                  child: Text(
                    selectedDate == null
                        ? 'Nenhum data selecionada!'
                        : 'Data Selecionada: ${DateFormat('dd/MM/y').format(selectedDate)}',
                  ),
                ),
                FlatButton(
                  textColor: Theme.of(context).primaryColor,
                  child: Text(
                    'Selecionar Data',
                    style: TextStyle(
                      fontWeight: FontWeight.bold,
                    ),
                  ),
                  onPressed: () => _showDatePicker(context),
                )
              ],
            ),
          );
  }
}
````
