# Criar Projeto em Flutter

## Criar projeto

Em uma pasta digite

```sh
$ flutter create projeto_perguntas
```

Isso vai criar a pasta 'projeto_perguntas' com todos os arquivos que precisamos

**LEMBRE-SE** sendo que em flutter não se pode usar hífen, então sera tudo underline `_`.

## Estrutura do Projeto

O `flutter create` gera também o `.gitignore` e as seguintes pastas

+ `/.dart_load` 
  + dart coloca arquivos compilados; Essa pasta não deve ir para o repositório, ela é sempre reconstruido.
+ `/.idea`
  + Configurações do intelij
+ `/android` & `/ios`
  + a parte nativa para o android
  + Projeto XCode do MacOS. Mesmo que essa pasta seja gerada não da pra mexer nela a não ser que seja por um mecanismo Mac.
  + o código específico de cada plataforma, incluindo ícones e configurações de aplicativos nos quais você define as permissões necessárias para o seu aplicativo (como acesso à localização, Bluetooth).
+ `/build`
  +  Cria o projeto compilado que é subido para o emulador
+ `/lib`
  +  todo o código do projeto estará aqui.
+ `/test`
  + Parte de teste caso precisar
  + Pasta responsável por armazenar os testes automatizados do teu projeto. Quanto mais recursos seu aplicativo tiver, mais difícil será testar manualmente. Os testes automatizados ajudam a garantir que o seu aplicativo funcione corretamente antes de publicá-lo, mantendo o recurso e a velocidade de correção do bug.

**Outros Arquivos**

+ `.metada`, `.packages` não mexe
+ `.iml` é extensão de configuração de projeto intelij, como vamos usar VSCode ele é desnecessário mas pode deixar ai.
+ `pubspec.yaml`:
  + tipo o `package.json` do npm; ONDE INFORMAMOS dependências e assets. É também ai que definimos as imagens para nosso projetos
  + Armazena uma lista de pacotes necessários para executar o aplicativo, assim como o `package.json` faz nas aplicações web. Você deve se lembrar que nos projetos Flutter você não pode usar o pub(gerenciador de pacotes do dart) diretamente, mas, em vez disso, você utilizará o comando Flutter: `flutter pub get ` ou `flutter packages get`

**Resumindo**

ficaremos 99% do tempo na pasta `/lib` e 1% em ``pubspec.yaml``

## `main.dart`

a primeira função a ser executada no flutter é `main.dart` na função main que executa `runApp()` que é passado o `MyApp()` que é um widget.

## Detalhs de Dart/FLutter

**Comentários em Dart/Flutter**

Mesmo que C: // para uma linha e /* e */

**undeline**

Como em pyhon/js podemos usar undliner para pegar uma variável que nâ vamos usar

**Objeto com `,`**

Como em JS, podemo colocar virgula mesmo no ultimo elemento de uma lista que não vai dar erro



