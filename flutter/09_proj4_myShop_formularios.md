# Formulários - Projeto 4 Shoppe

## Aprimorando o projeto do SHopee

### Adicionar um Dialog ao remover um item `cart_item_widget.dart`

Até entâo só arrastamos e deletamos, vamos adicionar um dialog para confirmação.

o `confirmDismiss` é uma funçâoq ue vai deletar o item se receber TRUE PR FALSE, e isso vem da parte `Navigator.of(ctx).pop(false);`

````dart
confirmDismiss: (_) {
    return showDialog(
        context: context,
		// ctx é o context
        builder: (ctx) => AlertDialog(
              title: Text('Tem certeza?'),
              content: Text('Quer remover o item do carrinho?'),
              actions: <Widget>[
                FlatButton(
                  child: Text('Não'),
                  onPressed: () {
                    Navigator.of(ctx).pop(false);
                  },
                ),
                FlatButton(
                  child: Text('Sim'),
                  onPressed: () {
					// Faz a exlusao
                    Navigator.of(ctx).pop(true);
                  },
                ),
              ],
            ));
  },
```` 

### `products_screen.dart`

Tela que terá os produtos para podermos gerenciá-los.

````dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';

import '../widgets/app_drawer.dart';
import '../providers/products.dart';
import '../widgets/product_item.dart';
import '../utils/app_routes.dart';

class ProductsScreen extends StatelessWidget {

  @override
  Widget build(BuildContext context) {

	// Produtos vem do Providers
    final productsData = Provider.of<Products>(context);
    final products = productsData.items;

    return Scaffold(
      appBar: AppBar(
        title: Text('Gerenciar Produtos'),
        actions: <Widget>[
          IconButton(
            icon: const Icon(Icons.add),
            onPressed: () {
              Navigator.of(context).pushNamed(
                AppRoutes.PRODUCT_FORM,
              );
            },
          ),
        ],
      ),
      drawer: AppDrawer(),
      body: Padding(
        padding: EdgeInsets.all(8),
        child: ListView.builder(
          itemCount: productsData.itemsCount,
          itemBuilder: (ctx, i) => Column(
            children: <Widget>[
              ProductItem(products[i]),
              Divider(),
            ],
          ),
        ),
      ),
    );
  }
}
````

### `providers/products.dart`

Será modificado e adicionado alguns elemento para centralizar as funções de CRUD internamente.

````dart
import 'dart:math';

import 'package:flutter/material.dart';
import './product.dart';
import '../data/dummy_data.dart';

class Products with ChangeNotifier {

  List<Product> _items = DUMMY_PRODUCTS;

  List<Product> get items  => [ ..._items ];

  int get itemsCount {
    return _items.length;
  }

  List<Product> get favoriteItems {
    return _items.where((prod) => prod.isFavorite).toList();
  }

  void addProduct(Product newProduct) {
    _items.add(Product(
      id: Random().nextDouble().toString(),
      title: newProduct.title,
      description: newProduct.description,
      price: newProduct.price,
      imageUrl: newProduct.imageUrl
    ));
    notifyListeners();
  }

  void updateProduct(Product product) {
    if(product == null || product.id == null) {
      return;
    }

    final index = _items.indexWhere((prod) => prod.id == product.id);
    if(index >= 0) {
      _items[index] = product;
      notifyListeners();
    }
  }

  void deleteProduct(String id) {
    final index = _items.indexWhere((prod) => prod.id == id);
    if(index >= 0) {
      _items.removeWhere((prod) => prod.id == id);
      notifyListeners();
    }
  }
}


````

### `views/product_item.dart` 

Para ser os items da tela de gerenciamento de produto.

````dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';

import '../providers/product.dart';
import '../providers/products.dart';
import '../utils/app_routes.dart';

class ProductItem extends StatelessWidget {
  final Product product;

  ProductItem(this.product);

  @override
  Widget build(BuildContext context) {
    return ListTile(
      leading: CircleAvatar(
        backgroundImage: NetworkImage(product.imageUrl),
      ),
      title: Text(product.title),
      trailing: Container(
        width: 100,
        child: Row(
          children: <Widget>[
            IconButton(
              icon: Icon(Icons.edit),
              color: Theme.of(context).primaryColor,
              onPressed: () {
				// Botazionho para editar que passa o proprio produto como parâmetro para o formuláriod e produto
                Navigator.of(context)
                    .pushNamed(AppRoutes.PRODUCT_FORM, arguments: product);
              },
            ),
            IconButton(
              icon: Icon(Icons.delete),
              color: Theme.of(context).errorColor,
              onPressed: () {
				// Dialog para confirmar se deleta ou nâo
                showDialog(
                  context: context,
                  builder: (ctx) => AlertDialog(
                    title: Text('Excluir Produto'),
                    content: Text('Tem certeza?'),
                    actions: <Widget>[
                      FlatButton(
                        child: Text('Não'),
                        onPressed: () => Navigator.of(context).pop(false),
                      ),
                      FlatButton(
                        child: Text('Sim'),
                        onPressed: () => Navigator.of(context).pop(true),
                      ),
                    ],
                  ),
                ).then((value) {
                  if (value) {
                    Provider.of<Products>(context, listen: false)
                        .deleteProduct(product.id);
                  }
                });
              },
            ),
          ],
        ),
      ),
    );
  }
}
````

### Form CRUD `views/product_form_screen.dart`

Entrada de dados obriga a ter widgets Stateful.

Widgets de entrada de Texto:
+ `TextFormField`

````dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';

import '../providers/product.dart';
import '../providers/products.dart';

class ProductFormScreen extends StatefulWidget {
  @override
  _ProductFormScreenState createState() => _ProductFormScreenState();
}

class _ProductFormScreenState extends State<ProductFormScreen> {

	// Essas variáveis seervem para gerenciar o foco do usuário, para eele ir digitando e automaticmanete avançar pelo formulário
  final _priceFocusNode = FocusNode();
  final _descriptionFocusNode = FocusNode();
  final _imageUrlFocusNode = FocusNode();

	// Esse controller é para ter mais acesso à URL que o usuário vai digitar
  final _imageUrlController = TextEditingController();
	//_form é uma GLobalKey que nos dá acesso aos camos do formulário
  final _form = GlobalKey<FormState>();
	// _formData é onde vai ficar o mapeamento onde vai ficar os items que inserimos. Será onde é chamado o  `OnSave` nos inputs
  final _formData = Map<String, Object>();

	// ao inciar essa tela
  @override
  void initState() {
    super.initState();
	// Adiciona um lsitener pois quadno eu mudar o foco da imagem apra qualquer outra coisa, eu vou querer que a imagem seja atualizada, isso sera feito por _updateImage()
    _imageUrlFocusNode.addListener(_updateImage);
  }

	// A atualizaçâo da magem só é feita se a imagem mudar
	void _updateImage() {
		if (isValidImageUrl(_imageUrlController.text)) {
		  setState(() {});
		}
  }

	// METODO DE CICLO DE VIDA. ASSOCIADO AO STATE
  @override
  void didChangeDependencies() {
    super.didChangeDependencies();

	// Se vazio vou preencher com os dados da rota (caso forem passados
    if (_formData.isEmpty) {
      final product = ModalRoute.of(context).settings.arguments as Product;
      
	// Inicializando os meus dados no formulário
      if(product != null) {
        _formData['id'] = product.id;
        _formData['title'] = product.title;
        _formData['description'] = product.description;
        _formData['price'] = product.price;
        _formData['imageUrl'] = product.imageUrl;

        _imageUrlController.text = _formData['imageUrl'];
      } else {
        _formData['price'] = '';
      }
    }
  }

  
	// Funçâo que valida a url da imagem, usada para validar uma imagem
  bool isValidImageUrl(String url) {
    bool startWithHttp = url.toLowerCase().startsWith('http://');
    bool startWithHttps = url.toLowerCase().startsWith('https://');
    bool endsWithPng = url.toLowerCase().endsWith('.png');
    bool endsWithJpg = url.toLowerCase().endsWith('.jpg');
    bool endsWithJpeg = url.toLowerCase().endsWith('.jpeg');
    return (startWithHttp || startWithHttps) &&
        (endsWithPng || endsWithJpg || endsWithJpeg);
  }

	// dispose é método para quando sair desse formulario
  @override
  void dispose() {
    super.dispose();
	// Vamos librar qualquer coisa que pode ficar nesses itesn
    _priceFocusNode.dispose();
    _descriptionFocusNode.dispose();
    _imageUrlFocusNode.removeListener(_updateImage);
    _imageUrlFocusNode.dispose();
  }

	// Função que valida o envio do
  void _saveForm() {
    var isValid = _form.currentState.validate();

    if (!isValid) {
      return;
    }

    _form.currentState.save();

		// Criação do produto ( apartir de sua clase) a partir dos dados do formulário
    final product = Product(
      id: _formData['id'],
      title: _formData['title'],
      price: _formData['price'],
      description: _formData['description'],
      imageUrl: _formData['imageUrl'],
    );

	// Aqui você está cahamando um Povider fora do método Build. Nesse caos você precisa adicionar "listen: false" pois aqui o Provider é um elemento lógico
    final products = Provider.of<Products>(context, listen: false);

	// Verifica se vai adicionar ou fazer o update
    if(_formData['id'] == null) {
      products.addProduct(product);
    } else {
      products.updateProduct(product);
    }
		// Voltar a tela
    Navigator.of(context).pop();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Formulário Produto'),
		// O botão de salvar o formulário estará na Scaffold, com um itme de Save a direita
        actions: <Widget>[
          IconButton(
            icon: Icon(Icons.save),
            onPressed: () {
              _saveForm();
            },
          )
        ],
      ),
      body: Padding(
        padding: const EdgeInsets.all(15.0),
        child: Form(
          key: _form,
          child: ListView(
            children: <Widget>[
              TextFormField(
                initialValue: _formData['title'],
                decoration: InputDecoration(labelText: 'Título'),
				// Quando vocÊ digitar, terá um botôzinho para ir para o proximo item do formulário
                textInputAction: TextInputAction.next,
				// Estou espeficicando para foco no item de preço, ou seja, o próximo item
                onFieldSubmitted: (_) {
                  FocusScope.of(context).requestFocus(_priceFocusNode);
                },
                onSaved: (value) => _formData['title'] = value,
				// valida no proprio Widget: a partir do validator retorna true/false internamente. Tmame permite mostrar a mensagem bastando retornando uma stirng
                validator: (value) {
                  bool isEmpty = value.trim().isEmpty;
                  bool isInvalid = value.trim().length < 3;

                  if (isEmpty || isInvalid) {
                    return 'Informe um Título válido com no mínimo 3 caracteres!';
                  }

                  return null;
                },
              ),
              TextFormField(
                initialValue: _formData['price'].toString(),
                decoration: InputDecoration(labelText: 'Preço'),
                textInputAction: TextInputAction.next,
                focusNode: _priceFocusNode,
                keyboardType: TextInputType.numberWithOptions(
                  decimal: true,
                ),
                onFieldSubmitted: (_) {
                  FocusScope.of(context).requestFocus(_descriptionFocusNode);
                },
                onSaved: (value) => _formData['price'] = double.parse(value),
                validator: (value) {
                  bool isEmpty = value.trim().isEmpty;
                  var newPrice = double.tryParse(value);
                  bool isInvalid = newPrice == null || newPrice <= 0;

                  if (isEmpty || isInvalid) {
                    return 'Informe um Preço válido!';
                  }

                  return null;
                },
              ),
              TextFormField(
                initialValue: _formData['description'],
                decoration: InputDecoration(labelText: 'Descrição'),
                maxLines: 3,
                keyboardType: TextInputType.multiline,
                focusNode: _descriptionFocusNode,
                onSaved: (value) => _formData['description'] = value,
                validator: (value) {
                  bool isEmpty = value.trim().isEmpty;
                  bool isInvalid = value.trim().length < 10;

                  if (isEmpty || isInvalid) {
                    return 'Informe uma Descrição válida com no mínimo 10 caracteres!';
                  }

                  return null;
                },
              ),
			// Inserir uma imagem por URL
              Row(
                crossAxisAlignment: CrossAxisAlignment.end,
                children: <Widget>[
				// Usamos Expanded para pegar mais espaço
                  Expanded(
                    child: TextFormField(
                      decoration: InputDecoration(labelText: 'URL da Imagem'),
                      keyboardType: TextInputType.url,
                      textInputAction: TextInputAction.done,
                      focusNode: _imageUrlFocusNode,
                      controller: _imageUrlController,
                      onFieldSubmitted: (_) {
                        _saveForm();
                      },
                      onSaved: (value) => _formData['imageUrl'] = value,
				// se a url for zaia ou invalida vai acusar      
                validator: (value) {
                        bool isEmpty = value.trim().isEmpty;
                        bool isInvalid = !isValidImageUrl(value);

                        if (isEmpty || isInvalid) {
                          return 'Informe uma URL válida!';
                        }
							// retornarr null indica que está válido
                        return null;
                      },
                    ),
                  ),
                  Container(
                    height: 100,
                    width: 100,
                    margin: EdgeInsets.only(
                      top: 8,
                      left: 10,
                    ),
                    decoration: BoxDecoration(
                      border: Border.all(
                        color: Colors.grey,
                        width: 1,
                      ),
                    ),
                    alignment: Alignment.center,
					// Dependendo se tem valor, pede para inserir ou informa a imagem msm
					// A atualizaçao da image é feita por _updateImage
                    child: _imageUrlController.text.isEmpty
                        ? Text('Informe a URL')
                        : FittedBox(
                            child: Image.network(
                              _imageUrlController.text,
                              fit: BoxFit.cover,
                            ),
                          ),
                  ),
                ],
              ),
            ],
          ),
        ),
      ),
    );
  }
}
````












