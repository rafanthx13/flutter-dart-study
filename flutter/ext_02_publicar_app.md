# Como publicar um App

## Passos para publicação

1. Teste e otimize o código
+ Isso é algo cíclico

2. Definit o nome e um Identificador paara a aplicação

3. Configurar serviço de terceiros (ex: APIs)
+ Configure de forma o masi restrita possível, piis se deixar muita coisa aberta pode ter problemas de sgurança

4. Definir Icone do App e a tela Splash Screen (a tela inicial do App ao entrar nele)

5. Publicar nas lojas de aplicativo

## COnfigurar para ANdroid

Vá na pasta android e busque `AndroidManifest.xml` lá mude `andorid:label` para o nome que quieser do seu aplicativo. Esse é o nome que vai aparecer na aplicaçâo.

Nesse manifeste terá um identificficador que está em package. No exemplo da calculadora é `package="com.example.calculator`. Esse identificador é único entre todos os apps do mundo. Use um domíni único como `br.com.cod3r.calculator`.

Subistitia em todo lugar por esse nome.

OBS

Se seu arquivo precisa usar inteert, é necessário colocar uma tag que está na basta /debug no AndorinManifest.xml

<uses-permission android:name="android.permission
