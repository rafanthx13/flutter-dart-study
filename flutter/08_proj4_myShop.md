# Gerencimento de Estado - App Shoppe

## Inherited Widget

O méotod com `of.context()`.

**Formas de comunicaçâo entre componentes**

Direta: Passar pelo constutor
Indireta: Mandar uma callback

**Inherited Widget**

Ele vai ser um compoennten acima de todos os outros que poderá se comunicar com qualquer componeente em qualquer ponto.

Assim num elmeneto pai eu uso `MeuWidget.of(context).data`. Assim vou percorrer a árvore de componente até chegar no Inherited Widget.

**OBS**

Mesmo que haja um gerencimaneto de estado, isso não significa que você deixara de ter widgets Steteful.

## Relembrando formas de navegaçâo

Fazer um push em uma Stack

````dart
Navigator.of(context).push(
	MaterialPageRoute(
		builder: (ctx) => ProductDetailScreen(product),
	)
)
````

Fazer por rota

````
Navigator.of(context).pushNamed(
  AppRoutes.PRODUCT_DETAIL,
  arguments: product,
);
````

Aqui estamos passando um produto como argumento, ele é recuperado em `products_overview_screen com o seguinte código

````dart
 final Product product =
        ModalRoute.of(context).settings.arguments as Product;
````



arquivo `utils` que vai ter as rotas que sâo constantes que definimnimos


// Aqui terá todas as constantes para navegar as rotas
````dart

class AppRoutes {
  static const HOME = '/';
  static const PRODUCT_DETAIL = '/product-detail';
  static const CART = '/cart';
  static const ORDERS = '/orders';
}
````

Para registrar a rota, é necessário fazer isso em main.dart

````dart
routes: {
          AppRoutes.HOME: (ctx) => ProductOverviewScreen(),
          AppRoutes.PRODUCT_DETAIL: (ctx) => ProductDetailScreen(),
          AppRoutes.CART: (ctx) => CartScreen(),
          AppRoutes.ORDERS: (ctx) => OrdersScreen(),
        },
````

### Widget Herdado : Provider

É um Widget que `extends InheritedWidget`.

### `providers/products.dart`

**ChangeNotifier** quando algo na lista for mudadno, todo mundo será notificado e poderá se atualizar. Usa o padrão OBSREVER.

`products.dart` terá nossa lsita d eprodutos de forma global, podendeo ser aessível em qualquer lugar

````dart
import 'package:flutter/material.dart';
import './product.dart';
import '../data/dummy_data.dart';

class Products with ChangeNotifier {

  List<Product> _items = DUMMY_PRODUCTS;

	// Aqui estou retornonado uma nova lista, uma cóía, para nâo alterar a lista original
  List<Product> get items  => [ ..._items ];

  List<Product> get favoriteItems {
    return _items.where((prod) => prod.isFavorite).toList();
  }

  void addProduct(Product product) {
    _items.add(product);
	// Mexer na lista de produtos é um evento, aí, tenho que notificar todos os interressados nessa clase
    notifyListeners();
  }
}

 
````

**no main.dart**

Você cobre toda a aplicaçâo com os Providers, e você os importa uando for usar.

````dart
import 'package:provider/provider.dart';

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MultiProvider(
      providers: [
		// estamos criando o estado inicial de Products/Cart/Orders que será compatilhado com outros Widgets
        ChangeNotifierProvider(
          create: (_) => new Products(),
        ),
        ChangeNotifierProvider(
          create: (_) => new Cart(),
        ),
        ChangeNotifierProvider(
          create: (_) => new Orders(),
        ),
      ],
      child: MaterialApp( ...
````

**Chamando o Provider: product_grid.dart**

Em: Provider.of<Products>(context)

````dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';

import '../widgets/product_item.dart';
import '../providers/products.dart';

class ProductGrid extends StatelessWidget {
  final bool showFavoriteOnly;

  ProductGrid(this.showFavoriteOnly);

  @override
  Widget build(BuildContext context) {
    final productsProvider = Provider.of<Products>(context);
    final products = showFavoriteOnly
        ? productsProvider.favoriteItems
        : productsProvider.items;

    return GridView.builder(
      padding: const EdgeInsets.all(10),
      itemCount: products.length,
		// Aqui eu eesotu emvolvendo um elemento já existente com CHangeNotifier, nesse caso eu nâo crio uma instância nova, nesse caso, eu tneho que passar por `.value`. VER DOCUMENTAÇÃO. Neste caso é difenrete do `main.dart`
      itemBuilder: (ctx, i) => ChangeNotifierProvider.value(
        value: products[i],
        child: ProductItem(),
      ),
      gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
        crossAxisCount: 2,
        childAspectRatio: 3 / 2,
        crossAxisSpacing: 10,
        mainAxisSpacing: 10,
      ),
    );
  }
}
````

**Um Provider como argumento**

Como ja'deve ter percebido, quando se trata de um Estado/Provider, o seu uso é diferente do de uma variável comun. EM productGrid antes agnete passava um produto como Argumento, mas, agora, cada Produto é um estado, então passaomos ele de forma difenrete como `ChangeNotifierProvider`. 

Agora o `Producttem` deverá pegar de outra forma, com `Provider.of<Product>(context, listen: false);`. COlocamos liste: false pois não será notificado nessa tela.
+ Esse listen = True, é para sempre notificar; Agora, se você só usa somente dados do provider  mas não muda nenhum desses adods então vocÊ coloca como false (como nesse caso) para aí não ficar *listening* de forma inútil.

ProductItem recebe agora o Produto a partir do Provider

````dart
// product_grid.dart
 itemBuilder: (ctx, i) => ChangeNotifierProvider.value(
        value: products[i],
        child: ProductItem(),
      ),
````

````dart
// product_item.dart
@override
  Widget build(BuildContext context) {
    final Product product = Provider.of<Product>(context, listen: false);
    final Cart cart = Provider.of<Cart>(context, listen: false);
````

Para cada idtem deverá ser criado um noficador


**Instalar Provider**

````dart
dependencies:
  flutter:
    sdk: flutter
  provider: ^4.0.5
  intl: ^0.16.1
````

**Como usar os providers**

O `provider` é um provedor de dados. VOcê o declara num ponto da árvore (em geral, no início dela) e os filhos podem chamá-la, basta importá-la. Você pode usar o provder em lugares mais embaixos, mas se fizer isso só terá acesso os filhos do Provider e nâo os seus irmãos

É feito o acesso apartir de `of(context)`. 

Quando o estado muda o método `build()` é chamado se for um widget `Stateful`


**O que é estado**

Estado sâo os dados que afetam nossa UI. **São os dados que mudam com o tempo de acordo com a ação do usuário**. 

Estado do App
+ Afeta parte significante do App
+ Dado usadoem vários componentes em locais da diferentes da árvore
+ Ex: Uusário logado, produtos no carrinho

Estado do Widget
+ Afeta somente o WIdget local
+ Ex: Exibir um Spinner

**Observação1**

O gerencimaento de estado permite mudar uma variável e ela poder ser vista em qualquer outro lugar. POREM, para que seja visto essa mudança de valor, o componente que a importa deve ser StateFUl. Se você pede um valor de provider e seu componente é Stateless, mesmo que o valor mude internmente essa mudança nunca será msotrada na tela. 

Lembre-se, apra mudar algo de um estado de um widget Stateful, você deve envolver em `setState( () { .. }`

## O projeto

### `main.dart`

Definimos um Tema em

````
theme: ThemeData(
          primarySwatch: Colors.purple,
          accentColor: Colors.deepOrange,
          fontFamily: 'Lato',
        ),
````

````dart
void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MultiProvider(
      providers: [
        ChangeNotifierProvider(
          create: (_) => new Products(),
        ),
        ChangeNotifierProvider(
          create: (_) => new Cart(),
        ),
        ChangeNotifierProvider(
          create: (_) => new Orders(),
        ),
      ],
      child: MaterialApp(
        title: 'Minha Loja',
        theme: ThemeData(
          primarySwatch: Colors.purple,
          accentColor: Colors.deepOrange,
          fontFamily: 'Lato',
        ),
        // home: ProductOverviewScreen(),
        routes: {
          AppRoutes.HOME: (ctx) => ProductOverviewScreen(),
          AppRoutes.PRODUCT_DETAIL: (ctx) => ProductDetailScreen(),
          AppRoutes.CART: (ctx) => CartScreen(),
          AppRoutes.ORDERS: (ctx) => OrdersScreen(),
        },
      ),
    );
  }
}
````

### `provides/products.dart`

Classe que define um produto.

O que esta com `final` é algo que será inicializado e nâo será mudado, enqunaot  que `isFavorite` pode ser mudado após ser inicializado.

````dart
import 'package:flutter/foundation.dart';

class Product with ChangeNotifier {
  final String id;
  final String title;
  final String description;
  final double price;
  final String imageUrl;
  bool isFavorite;

  Product({
    @required this.id,
    @required this.title,
    @required this.description,
    @required this.price,
    @required this.imageUrl,
    this.isFavorite = false
  });

	// Maracr como favorito ou não
    // Essa classe vai está com o mixin ChangeNotifier, que permitiirá notificar a todo mundo que tem ess produto que mudou seus dados, e asism atualizar
  void toggleFavorite() {
    isFavorite = !isFavorite;
    notifyListeners();
  }
}
````

### `products_overview_screen.dart`

Será a tela de produtos

````
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import '../widgets/product_grid.dart';
import '../widgets/badge.dart';
import '../widgets/app_drawer.dart';
import '../providers/cart.dart';
import '../utils/app_routes.dart';

enum FilterOptions {
  Favorite,
  All,
}

class ProductOverviewScreen extends StatefulWidget {
  @override
  _ProductOverviewScreenState createState() => _ProductOverviewScreenState();
}

class _ProductOverviewScreenState extends State<ProductOverviewScreen> {
  bool _showFavoriteOnly = false;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Minha Loja'),
        actions: <Widget>[
          PopupMenuButton(
            onSelected: (FilterOptions selectedValue) {
              setState(() {
                if (selectedValue == FilterOptions.Favorite) {
                  _showFavoriteOnly = true;
                } else {
                  _showFavoriteOnly = false;
                }
              });
            },
            icon: Icon(Icons.more_vert),
            itemBuilder: (_) => [
              PopupMenuItem(
                child: Text('Somente Favoritos'),
                value: FilterOptions.Favorite,
              ),
              PopupMenuItem(
                child: Text('Todos'),
                value: FilterOptions.All,
              ),
            ],
          ),
          Consumer<Cart>(
            child: IconButton(
              icon: Icon(Icons.shopping_cart),
              onPressed: () {
                Navigator.of(context).pushNamed(AppRoutes.CART);
              },
            ),
            builder: (_, cart, child) => Badge(
              value: cart.itemsCount.toString(),
              child: child,
            ),
          )
        ],
      ),
      body: ProductGrid(_showFavoriteOnly),
      drawer: AppDrawer(),
    );
  }
}
````

### `widgest/products_item.dart`

é a parte interna de cada item de produto, como ele é visto na tela de produtos


````dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import '../providers/product.dart';
import '../providers/cart.dart';
import '../utils/app_routes.dart';

class ProductItem extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final Product product = Provider.of<Product>(context, listen: false);
    final Cart cart = Provider.of<Cart>(context, listen: false);

    return ClipRRect(
      borderRadius: BorderRadius.circular(10),
		// É um GridTile, pois está dentro de um GridView
      child: GridTile(
			// Ao apertar o GriTile irá para uma tela
        child: GestureDetector(
          onTap: () {
            Navigator.of(context).pushNamed(
              AppRoutes.PRODUCT_DETAIL,
              arguments: product,
            );
          },
			// imagem do produto
          child: Image.network(
            product.imageUrl,
            fit: BoxFit.cover,
          ),
        ),
		// GridTileBar é uma a parte pretaque terá o ícnoe de favorito
        footer: GridTileBar(
          backgroundColor: Colors.black87,
          leading: Consumer<Product>(
            builder: (ctx, product, _) => IconButton(
              icon: Icon(
                  product.isFavorite ? Icons.favorite : Icons.favorite_border),
              color: Theme.of(context).accentColor,
              onPressed: () {
                product.toggleFavorite();
              },
            ),
          ),
          title: Text(
            product.title,
            textAlign: TextAlign.center,
          ),
          trailing: IconButton(
            icon: Icon(Icons.shopping_cart),
            color: Theme.of(context).accentColor,
            onPressed: () {
              cart.addItem(product);
            },
          ),
        ),
      ),
    );
  }
}
````

### `views/product_detail_screen.dart`

Define a tela que vÊ o produto

````dart
import 'package:flutter/material.dart';
import '../providers/product.dart';

class ProductDetailScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final Product product =
        ModalRoute.of(context).settings.arguments as Product;

    return Scaffold(
      appBar: AppBar(
        title: Text(product.title),
      ),
      body: SingleChildScrollView(
        child: Column(
          children: <Widget>[
            Container(
              height: 300,
              width: double.infinity,
              child: Image.network(
                product.imageUrl,
                fit: BoxFit.cover,
              ),
            ),
            SizedBox(height: 10),
            Text(
              'R\$ ${product.price}',
              style: TextStyle(
                color: Colors.grey,
                fontSize: 20,
              ),
            ),
            SizedBox(height: 10),
            Container(
              padding: EdgeInsets.symmetric(horizontal: 10),
              width: double.infinity,
              child: Text(
                product.description,
                textAlign: TextAlign.center,
              ),
            ),
          ],
        ),
      ),
    );
  }
}
````

### `product_item.dart` e `Consumer`

O Consumer é uma outra forma de pegar um Estado. Com o consumer, podemos ficar `listening` somente uma parte em vez de todo o objeto

````dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import '../providers/product.dart';
import '../providers/cart.dart';
import '../utils/app_routes.dart';

class ProductItem extends StatelessWidget {
  @override
  Widget build(BuildContext context) {

	// Especifico para pegar a lista de produtos de um estado mas não ficar Observer o tempo todo
    final Product product = Provider.of<Product>(context, listen: false);
    final Cart cart = Provider.of<Cart>(context, listen: false);

    return ClipRRect(
      borderRadius: BorderRadius.circular(10),
      child: GridTile(
        child: GestureDetector(
          onTap: () {
            Navigator.of(context).pushNamed(
              AppRoutes.PRODUCT_DETAIL,
              arguments: product,
            );
          },
          child: Image.network(
            product.imageUrl,
            fit: BoxFit.cover,
          ),
        ),
        footer: GridTileBar(
          backgroundColor: Colors.black87,
			// Consumer: Por mais que tenhamos colocado `listenig: false` podemos escutar somente essa parte, a do se é ou não FAVORITO.
          leading: Consumer<Product>(
            builder: (ctx, product, _) => IconButton(
              icon: Icon(
                  product.isFavorite ? Icons.favorite : Icons.favorite_border),
              color: Theme.of(context).accentColor,
              onPressed: () {
                product.toggleFavorite();
              },
            ),
          ),
          title: Text(
            product.title,
            textAlign: TextAlign.center,
          ),
          trailing: IconButton(
            icon: Icon(Icons.shopping_cart),
            color: Theme.of(context).accentColor,
            onPressed: () {
              cart.addItem(product);
            },
          ),
        ),
      ),
    );
  }
}
````

## Provider do carrinho `providers/cart.dart`

````dart
import 'dart:math';

import 'package:flutter/foundation.dart';
import './product.dart';

// Especificando um item
class CartItem {
  final String id;
  final String productId;
  final String title;
  final int quantity;
  final double price;

  CartItem({
    @required this.id,
    @required this.productId,
    @required this.title,
    @required this.quantity,
    @required this.price,
  });
}

// O carrinho que é uma lsita compartilhada globalmente
class Cart with ChangeNotifier {

	// Inicia como uma lsita vazia
  Map<String, CartItem> _items = {};

	// Ao buscar essa lista, irá retornar uma cópia
  Map<String, CartItem> get items {
    return {..._items};
  }

	// Buscar length
  int get itemsCount {
    return _items.length;
  }

	// Buscar o valor total
  double get totalAmount {
    double total = 0.0;
    _items.forEach((key, cartItem) {
      total += cartItem.price * cartItem.quantity;
    });
    return total;
  }


  void addItem(Product product) {
		// caso já tiver o item, apenas adiciona + 1 na quantidade
    if (_items.containsKey(product.id)) {
      _items.update(
        product.id,
        (existingItem) => CartItem(
          id: existingItem.id,
          productId: product.id,
          title: existingItem.title,
          quantity: existingItem.quantity + 1,
          price: existingItem.price,
        ),
      );
    } else {
		// Caso nao tiver o item, ccria e o insere na lsita de items
      _items.putIfAbsent(
        product.id,
        () => CartItem(
          id: Random().nextDouble().toString(),
          productId: product.id,
          title: product.title,
          price: product.price,
          quantity: 1,
        ),
      );
    }
	// notifica quem precisar
    notifyListeners();
  }

	// remove o item do carrinho pelo productID
  void removeItem(String productId) {
    _items.remove(productId);
    notifyListeners();
  }

  void clear() {
    _items = {};
    notifyListeners();
  }
}
````

### PULEI

218, 219, 221

### Tela de carrinho `cart_screen.dart`

````dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';

import '../widgets/cart_item_widget.dart';
import '../providers/cart.dart';
import '../providers/orders.dart';

class CartScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {

	// O carrinho vem do rpovider
    final Cart cart = Provider.of(context);
    final cartItems = cart.items.values.toList();

    return Scaffold(
      appBar: AppBar(
        title: Text('Carrinho'),
      ),
      body: Column(
        children: <Widget>[
          Card(
            margin: EdgeInsets.all(25),
            child: Padding(
              padding: EdgeInsets.all(10),
              child: Row(
					// COlocar espaço entre os elemntos com FlexB
                mainAxisAlignment: MainAxisAlignment.spaceBetween,
                children: <Widget>[
                  Text(
                    'Total',
                    style: TextStyle(fontSize: 20),
                  ),
					// PESQUISAR
                  SizedBox(width: 10),
					// Chamamos a funçâo do carrinho que pega o total
                  Chip(
                    label: Text(
                      'R\$${cart.totalAmount}',
                      style: TextStyle(
                        color: Theme.of(context).primaryTextTheme.title.color,
                      ),
                    ),
                    backgroundColor: Theme.of(context).primaryColor,
                  ),
					// Spacer: Serve parar deixar um espaço vazio na flexBox, isso empurra o proximo elemento mais para o canto
                  Spacer(),
					//Ao comprar adicionar PEDIDO e zerar o carrinho
                  FlatButton(
                    child: Text('COMPRAR'),
                    textColor: Theme.of(context).primaryColor,
                    onPressed: () {
                      Provider.of<Orders>(context, listen: false)
                          .addOrder(cart);
                      cart.clear();
                    },
                  ),
                ],
              ),
            ),
          ),
			// SizedBox: um espaçamento
          SizedBox(height: 10),
			// Expanded é usadno no contexto de Row/COlum, ele permmite que seu elemento interno estique mais no sentido do flexBox e asism ocupq mais espaço que o normal
          Expanded(
            child: ListView.builder(
              itemCount: cart.itemsCount,
              itemBuilder: (ctx, i) => CartItemWidget(cartItems[i]),
            ),
          ),
        ],
      ),
    );
  }
}
````

### `cart_item_widget.dart`
O item msotrado no carrinho

````dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import '../providers/cart.dart';

class CartItemWidget extends StatelessWidget {

  final CartItem cartItem;
  CartItemWidget(this.cartItem);

  @override
  Widget build(BuildContext context) {
	// Dismissible permite arrasta e tirra da LIstTile um item dessa lista apenas arrastando o item. Será o item ao arrastar
    return Dismissible(
      key: ValueKey(cartItem.id),
		// vamos deixar explícito que estamo deleetando
      background: Container(
		// vermelho para indicar que está deletando
        color: Theme.of(context).errorColor,
        child: Icon(
          Icons.delete,
          color: Colors.white,
          size: 40,
        ),
		//
        alignment: Alignment.centerRight,
        padding: EdgeInsets.only(right: 20),
        margin: EdgeInsets.symmetric(
          horizontal: 15,
          vertical: 4,
        ),
      ),
		// o arrastar ser somente de um lado 
      direction: DismissDirection.endToStart,
		// AO remover vamos fazer uma operaçâo, tirar da lista
      onDismissed: (_) {
        Provider.of<Cart>(context, listen: false)
            .removeItem(cartItem.productId);
      },
      child: Card(
        margin: EdgeInsets.symmetric(
          horizontal: 15,
          vertical: 4,
        ),
        child: Padding(
          padding: EdgeInsets.all(8),
			// ListTile permite é a lsita desenhado do material Desgin. É como uma Table do HTML, otimizada. Ela permite separar melhor as coisas internemnte do que fazer por ROW
          child: ListTile(
				// o que fica no início. Via mostrar o preço num círculo colorido
            leading: CircleAvatar(
              child: Padding(
                padding: EdgeInsets.all(5),
                child: FittedBox(
                  child: Text('${cartItem.price}'),
                ),
              ),
            ),
            title: Text(cartItem.title),
            subtitle: Text('Total: R\$ ${cartItem.price * cartItem.quantity}'),
				// o que vai ficar no canto da direita
            trailing: Text('${cartItem.quantity}x'),
          ),
        ),
      ),
    );
  }
}
````

### Detalhe do produto `product_detail_screen.dart`

````dart
import 'package:flutter/material.dart';
import '../providers/product.dart';

class ProductDetailScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final Product product =
        ModalRoute.of(context).settings.arguments as Product;

    return Scaffold(
      appBar: AppBar(
        title: Text(product.title),
      ),
      body: SingleChildScrollView(
        child: Column(
          children: <Widget>[
            Container(
              height: 300,
              width: double.infinity,
              child: Image.network(
                product.imageUrl,
                fit: BoxFit.cover,
              ),
            ),
            SizedBox(height: 10),
            Text(
              'R\$ ${product.price}',
              style: TextStyle(
                color: Colors.grey,
                fontSize: 20,
              ),
            ),
            SizedBox(height: 10),
            Container(
              padding: EdgeInsets.symmetric(horizontal: 10),
              width: double.infinity,
              child: Text(
                product.description,
                textAlign: TextAlign.center,
              ),
            ),
          ],
        ),
      ),
    );
  }
}
````

### Provider da Order: `orders.dart`

Há duas classse: Uma ordem e a Classe `Orders`que será a que vai ter o Provider.

````dart
import 'dart:math';

import 'package:flutter/material.dart';

import './cart.dart';

class Order {
  final String id;
  final double total;
  final List<CartItem> products;
  final DateTime date;

  Order({
    this.id,
    this.total,
    this.products,
    this.date,
  });
}

class Orders with ChangeNotifier {
  List<Order> _items = [];

  List<Order> get items {
    return [..._items];
  }

  int get itemsCount {
    return _items.length;
  }

  void addOrder(Cart cart) {
    _items.insert(
      0,
      Order(
        id: Random().nextDouble().toString(),
        total: cart.totalAmount,
        date: DateTime.now(),
        products: cart.items.values.toList(),
      ),
    );

    notifyListeners();
  }
}
````

### `app_drawer.dart`

Drawer é o Hamburgeuer a esquerda que abre uma barra que sobrepoe a tela que tem um menuzinho.

````dart
import 'package:flutter/material.dart';
import '../utils/app_routes.dart';

class AppDrawer extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Drawer(
      child: Column(
        children: <Widget>[
          AppBar(
            title: Text('Bem vindo Usuário!'),
			// amntem o hamburgue ao abrir a telinha
            automaticallyImplyLeading: false,
          ),
          Divider(),
          ListTile(
            leading: Icon(Icons.shop),
            title: Text('Loja'),
            onTap: () {
              Navigator.of(context).pushReplacementNamed(
                AppRoutes.HOME,
              );
            },
          ),
          Divider(),
          ListTile(
            leading: Icon(Icons.payment),
            title: Text('Pedidos'),
            onTap: () {
              Navigator.of(context).pushReplacementNamed(
                AppRoutes.ORDERS,
              );
            },
          ),
        ],
      ),
    );
  }
}
````

### `orders_screen.dart` e `.dart`

Vai ter uma lista com ListView de OrderWidget

````dart
//orders_screen.dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';

import '../widgets/app_drawer.dart';
import '../providers/orders.dart';
import '../widgets/order_widget.dart';


class OrdersScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final Orders orders = Provider.of(context);
    return Scaffold(
      appBar: AppBar(
        title: Text('Meus Pedidos'),
      ),
      drawer: AppDrawer(),
      body: ListView.builder(
        itemCount: orders.itemsCount,
        itemBuilder: (ctx, i) => OrderWidget(orders.items[i]),
      ),
    );
  }
}
````

order_widget.dart

````dart
// order_widget.dart
import 'dart:math';

import 'package:flutter/material.dart';
import 'package:intl/intl.dart';

import '../providers/orders.dart';

class OrderWidget extends StatefulWidget {
  final Order order;

  OrderWidget(this.order);

  @override
  _OrderWidgetState createState() => _OrderWidgetState();
}

class _OrderWidgetState extends State<OrderWidget> {
  bool _expanded = false;

  @override
  Widget build(BuildContext context) {
    return Card(
      margin: EdgeInsets.all(10),
      child: Column(
        children: <Widget>[
			// ListTIle, uma listinha boinitinha
          ListTile(
			// Arredonda, sem isso ele mostra muito lixo
            title: Text('R\$ ${widget.order.total.toStringAsFixed(2)}'),
            subtitle: Text(
              DateFormat('dd/MM/yyyy hh:mm').format(widget.order.date),
            ),
			// Terá um botão que permite expandir a ListTIle
            trailing: IconButton(
              icon: Icon(Icons.expand_more),
              onPressed: () {
                setState(() {
                  _expanded = !_expanded;
                });
              },
            ),
          ),
          if (_expanded)
			// Por um padding
            Container(
              padding: EdgeInsets.symmetric(
                horizontal: 15,
                vertical: 4,
              ),
              height: (widget.order.products.length * 25.0) + 10,
				// Usamos ListView, mapemos  cada product para um Row e retorna por fim uma lsita de Rows
              child: ListView(
                children: widget.order.products.map((product) {
                  return Row(
                    mainAxisAlignment: MainAxisAlignment.spaceBetween,
                    children: <Widget>[
                      Text(
                        product.title,
                        style: TextStyle(
                          fontSize: 18,
                          fontWeight: FontWeight.bold,
                        ),
                      ),
                      Text(
                        '${product.quantity} x R\$ ${product.price}',
                        style: TextStyle(
                          fontSize: 18,
                          color: Colors.grey,
                        ),
                      ),
                    ],
                  );
                }).toList(),
              ),
            ),
        ],
      ),
    );
  }
}

````
