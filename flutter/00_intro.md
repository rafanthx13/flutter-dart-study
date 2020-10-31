# Intro

## Links

+ https://www.notion.so/Instala-o-das-ferramentas-9eee9e25550e4477b3d0b432b605aca2#ca6a8c27c56e4c588f81d9d46272f273
+ https://blog.rocketseat.com.br/ambiente-desenvolvimento-javascript/
+ https://themeselection.com/go/download-vuexy-vuejs/
+ https://www.flutterparainiciantes.com.br/

## Flutter

### O que é

Flutter é a ferramenta do Google para desenvolvimento de aplicativos nativos para Android, iOS, Web e Desktop.

Assim como [React Native](https://facebook.github.io/react-native/), [Xamarin](https://docs.microsoft.com/pt-br/xamarin/xamarin-forms/) e [Ionic](https://ionicframework.com/), o Flutter é o Google nos fornecendo mais uma opção para escolha de tecnologia ***cross-platform***.

Espero, verdadeiramente, com este livro, despertar o seu interesse para que o Flutter esteja no seu próximo projeto.

### Origem

## Origem

O projeto foi iniciado em 2014, com o codinome "sky", com o intuito de encontrar uma melhor maneira de construir interfaces para o mobile. Em 2015, foi [apresentado](https://www.youtube.com/watch?v=PnIWl33YMwA&t=67s) durante a Dart Developer Summit, onde foi possível ver o código Dart sendo executado em um aparelho Android e também apresentado com o nome **Flutter**. Em 2016, no mesmo evento, Flutter foi [definido](https://www.youtube.com/watch?v=Mx-AllVZ1VY&t=76s) como *"Uma melhor maneira de desenvolvimento mobile"*. A estréia do Flutter em um grande evento foi no Google IO 2017, em uma [sessão](https://www.youtube.com/watch?v=w2TcYP8qiRI&t=1773s) de "live coding", onde o app construido foi integrado ao Firebase e com acesso à camera. No Google IO 2018, com uma grande expectativa, Flutter já teve um destaque maior, contando com 3 excelentes palestras, cobrindo tópicos como [gerenciamento de estado](https://www.youtube.com/watch?v=RS36gBEp8OI), uso do [Material Design](https://www.youtube.com/watch?v=hA0hrpR-o8U) e novamente outra [sessão](https://www.youtube.com/watch?v=p4yLzYwy_4g) "live coding" de um app acessando o Firebase.

Em dezembro de 2018, finalmente foi [lançada](https://www.youtube.com/watch?v=D-o4BqJxmJE) a versão 1.0 e dentre tantas novidades, o principal destaque foi para o projeto Hummingbird, que futuramente se tornou o Flutter para Web.

### Por que usar flutter?

Flutter é baseado em 3 pilares:

+ Desenvolvimento rápido
+ Interfaces super bonitas
+ Performance nativa

Por estas 3 características, ele já pode ser considerado um forte candidato.

Por que escolher Flutter para o seu app?

+ Criado e mantido pelo Google;
+ Mais de 170 widgets (componentes) prontos para serem utilizados;
+ Principais plugins para acesso à recursos nativos do celular (bateria, câmera, conectividade, webview, etc ) também são mantidos pelo Google;
+ Seu código Dart é executado diretamente pelo aparelho (em um botão, Flutter não utiliza o componente já existente de cada plataforma);
+ Flutter desenha todos os pixels na tela, tornando o aplicativo altamente customizável;
+ Alta performance: Aplicativos rodam em 60 frames por segundo (ou em até 120, caso o aparelho suporte);
+ Alta produtividade. Alterações no código refletidas no celular ou emulador em até 0,5s. Caso precise reiniciar por completo o app, isto é feito em menos de 2 segundos;

## Instalação

Requer Bastante espaço, a instalação de Android Studio, mexer na BIOS para habilitar emulador

## Emulador

Pode-se usar o celular android.

Passo-a-Passo

1. Busque no número da versão do celular android e aperte 3 vezes para habilitar o modo desenvolvedor
2. Busque "Depuração USB" e habilite
3. Depois conecte o celular ao PC e na hora de emular, use como *Device* o celular

Para emular IOS seria necessário um MacOS



Você pode executar o projeto no emulador pelo VSCode se a raiz for o próprio projeto (não pode ser apartir de uma subpasta acima)

Em baixo no canto direito do VSCode vai aparecer Device, ai você seleciona.

Além disso você pode usar o seu próprio celular. Em configurações busque "modo desenvolvedor" e habilite "Depuração USB".

### Check Flutter

use flutter doctor para checar se tudo foi devidamente instalado para rodar flutter

```sh
$ flutter doctor
```

Exemplo de saída

```
Doctor summary (to see all details, run flutter doctor -v):
[✓] Flutter (Channel stable, v1.9.1+hotfix.6, on Mac OS X 10.14.6 18G87, locale en-BR)

[✓] Android toolchain - develop for Android devices (Android SDK version 29.0.1)
[✓] Xcode - develop for iOS and macOS (Xcode 10.3)
[!] Android Studio (version 3.4)
    ✗ Flutter plugin not installed; this adds Flutter specific functionality.
    ✗ Dart plugin not installed; this adds Dart specific functionality.
[✓] VS Code (version 1.39.2)
[!] Connected device
    ! No devices available
```

