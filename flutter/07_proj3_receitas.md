# Navegaçâo - App Receitas

## VS CODE

.

VS CODE

CTRL + X: Apaga uma linha

VS permite pegar o caminho relativo, basta licar com o botao direito no VS Code

## Estutura do projeto

````
lib/
	components/
	screens/
	models/
	utils/
	data/ : Tem o daod mocado
		dummy_data.dart
````

#### models/category.dart

````dart
import 'package:flutter/material.dart';

class Category {

  final String id;
  final String title;
  final Color color;

  const Category({
    @required this.id,
    @required this.title,
    this.color = Colors.orange,
  });
}
````

#### screens/categories_screen.dart

`categories_screen.dart`: É a tela inicial com a lista de categorias

Será a tela com as categorias. Perceba que ela tem um formato bem fixo, por isos nao usaremos o conecito de FlexBos mas sim de Grid.

Esse arqvuio é  esquema da tela de categorias que deverá receber  `CategoryItem`

`SliverGridDelegateWithMaxCrossAxisExtent`: Vai fazer rolagem o eixo nâo principal

`maxCrossAxisExtent: 200,` extender até with de 200

`crossAxisSpacing`: espaçamento


````dart
import 'package:flutter/material.dart';

import '../components/category_item.dart';
import '../data/dummy_data.dart';

class CategoriesScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return GridView(
      padding: const EdgeInsets.all(25),
      gridDelegate: SliverGridDelegateWithMaxCrossAxisExtent(
        childAspectRatio: 3 / 2,
        crossAxisSpacing: 20,
        mainAxisSpacing: 20,
      ),
		// Vou receber do dummy (dados mokados) e para cada elemento da lista cnveter num `CategoryItem
      children: DUMMY_CATEGORIES.map((cat) {
        return CategoryItem(cat);
      }).toList(),
    );
  }
}
````

#### components/category_item.dart

é cada quadradinho de uma categoria de item


````dart
import 'package:flutter/material.dart';

import '../models/category.dart';
import '../utils/app_routes.dart';

class CategoryItem extends StatelessWidget {

	// modelo de uma categroias, dados que vem do dumy
  final Category category;

  const CategoryItem(this.category);

  void _selectCategory(BuildContext context) {
    Navigator.of(context).pushNamed(
      AppRoutes.CATEGORIES_MEALS,
      arguments: category,
    );
  }

  @override
  Widget build(BuildContext context) {
    return InkWell(
      onTap: () => _selectCategory(context),
		// cor de splah ao vocE clicar num elemento
      splashColor: Theme.of(context).primaryColor,
      borderRadius: BorderRadius.circular(15),
      child: Container(
        padding: const EdgeInsets.all(15),
        child: Text(
          category.title,
          style: Theme.of(context).textTheme.title,
        ),
		// CSS: Borda arredondada e A cor gradiente da esque-> idr com umpouco de opacidade
        decoration: BoxDecoration(
          borderRadius: BorderRadius.circular(15),
          gradient: LinearGradient(
            colors: [
              category.color.withOpacity(0.5),
              category.color,
            ],
            begin: Alignment.topLeft,
            end: Alignment.bottomRight,
          ),
        ),
      ),
    );
  }
}
````
#### tema em `main.dart`

Definindo um tema para aplicaçâo.

canvasColor é a tela de fundo. Sem isso ela é branca

````dart
 theme: ThemeData(
        primarySwatch: Colors.pink,
        accentColor: Colors.amber,
        fontFamily: 'Raleway',

        canvasColor: Color.fromRGBO(255, 254, 229, 1),
        textTheme: ThemeData.light().textTheme.copyWith(
              title: TextStyle(
                fontSize: 20,
                fontFamily: 'RobotoCondensed',
              ),
            ),
      ),
````

como usar

em categroy_item.dart: `style: Theme.of(context).textTheme.title`

## Criando um esquema de uma nova tela : `categories_meals_screen.dart`

Por ser umatela nova, deverá ter o `Scaffold`.

Uma forma de fazer navegaçâo é a partir do conceito de STAK: COlocar tela em cima de outra. O próprio `AppBar` vai msotrar a setinha para voltar a uma teala que seria o `pop()` na pilha de telas.

`category_item.dart`: 

````
void _selectCategory(BuildContext context) {

	// Segunda FOrma: Rotas nomeadas (percebea o `pushNamed`
	// Essa forma é masi centralizada
    Navigator.of(context).pushNamed(
      AppRoutes.CATEGORIES_MEALS,
      arguments: category,
    );

	/* Primeira Forma: Essa forma é mais crua

	Navigator.of(context).push(
		MaterialPageROuter(
			builder: (_) {
				return CategoriesMealsScreen();
			}
		)
	)

	*/
  }

InkWell(
      onTap: () => _selectCategory(context),
````

InkWeel singifica que vai ser um compoenten que ao clipcar acionara alguma coisa. QUando aperta vai executar o método _selectCategory.

````dart
import 'package:flutter/material.dart';

import '../components/meal_item.dart';
import '../models/category.dart';
import '../models/meal.dart';

class CategoriesMealsScreen extends StatelessWidget {

  final List<Meal> meals;

  const CategoriesMealsScreen(this.meals);

  @override
  Widget build(BuildContext context) {
    final category = ModalRoute.of(context).settings.arguments as Category;

	// Filtragem de  doas as refeiçêos de uma unica categoria
	// Usada em MealItem
    final categoryMeals = meals.where((meal) {
      return meal.categories.contains(category.id);
    }).toList();

    return Scaffold(
      appBar: AppBar(
        title: Text(category.title),
      ),
      body: ListView.builder(
        itemCount: categoryMeals.length, // QUantidade de refeiçôes par auma categoria
        itemBuilder: (ctx, index) {
          return MealItem(categoryMeals[index]);
        },
      ),
    );
  }
}
````

##### Rotas nomeadas

no main.dart no componente razi temos a opçâo de  `routes`. 

Vamos especificar as rotas parecida com rotas de API. Cada uma deverá receber um contexto e ir para um Widget.

Podemos criara as contantaes das rotas como constantes num arquivo separado

````dart
// main.dart
routes: {
		// ou '/' que é a primeira tela, home
        AppRoutes.HOME: (ctx) => TabsScreen(_favoriteMeals),
		// /categories-meals
        AppRoutes.CATEGORIES_MEALS: (ctx) =>
            CategoriesMealsScreen(_availableMeals),
        AppRoutes.MEAL_DETAIL: (ctx) => MealDetailScreen(_toggleFavorite, _isFavorite),
        AppRoutes.SETTINGS: (ctx) => SettingsScreen(settings, _filterMeals),
      },
````

`lib/utils/app_routes.dart`

````dart
class AppRoutes {
  static const HOME = '/';
  static const CATEGORIES_MEALS = '/categories-meals';
  static const MEAL_DETAIL = '/meal-detail';
  static const SETTINGS = '/settings';
}
````

Com isso vamos substituir de string para uma variavel. Assim vai evitar erro de digitaçâo e melhorar o codigo.

## Criando conteudo da tela de refeições

#### Modelo de uma meal `model/meal.dart`

````dart

import 'package:flutter/foundation.dart';

enum Complexity {
  Simple,
  Medium,
  Difficult,
}

enum Cost {
  Cheap,
  Fair,
  Expensive,
}

class Meal {

  final String id;
  final List<String> categories;
  final String title;
  final String imageUrl;
  final List<String> ingredients;
  final List<String> steps;
  final int duration;
  final bool isGlutenFree;
  final bool isLactoseFree;
  final bool isVegan;
  final bool isVegetarian;
  final Complexity complexity;
  final Cost cost;

  const Meal({
    @required this.id,
    @required this.categories,
    @required this.title,
    @required this.imageUrl,
    @required this.ingredients,
    @required this.steps,
    @required this.duration,
    @required this.isGlutenFree,
    @required this.isLactoseFree,
    @required this.isVegan,
    @required this.isVegetarian,
    @required this.complexity,
    @required this.cost,
  });

	// mapeamento de complexidade para a mensagem a ser mostrada
  String get complexityText {
    switch (complexity) {
      case Complexity.Simple:
        return 'Simples';
      case Complexity.Medium:
        return 'Normal';
      case Complexity.Difficult:
        return 'Difícil';
      default:
        return 'Desconhecida';
    }
  }

	// Mapear denovo
  String get costText {
    switch (cost) {
      case Cost.Cheap:
        return 'Barato';
      case Cost.Fair:
        return 'Justo';
      case Cost.Expensive:
        return 'Caro';
      default:
        return 'Desconhecido';
    }
  }
}

````

#### Meal list of one categroy

````dart
import 'package:flutter/material.dart';

import '../models/meal.dart';
import '../utils/app_routes.dart';

class MealItem extends StatelessWidget {
  final Meal meal;

  const MealItem(this.meal);

	// Roteamento para Meal Detail
  void _selectMeal(BuildContext context) {
    Navigator.of(context).pushNamed(
      AppRoutes.MEAL_DETAIL,
      arguments: meal,
    ).then((result) {
      if(result == null) {
        print('Sem resultado!');
      } else {
        print('O nome da refeição é $result.');
      }
    });
  }

  @override
  Widget build(BuildContext context) {
    return InkWell(
		// Listen Tap para ir para a tela de detalhes do meal
      onTap: () => _selectMeal(context),
      child: Card(
        shape: RoundedRectangleBorder(
          borderRadius: BorderRadius.circular(15),
        ),
        elevation: 4,
        margin: const EdgeInsets.all(10),
        child: Column(
          children: <Widget>[	
			// Stack: Um elemento sobreposto a outro na tela
            Stack(
              children: <Widget>[
				// ClipRRect: Vai por borda na imagem
                ClipRRect(
                  borderRadius: BorderRadius.only(
                    topLeft: Radius.circular(15),
                    topRight: Radius.circular(15),
                  ),
                  child: Image.network(
                    meal.imageUrl,
                    height: 250,
                    width: double.infinity,
                    fit: BoxFit.cover,
                  ),
                ),
				// So funciona dentro de um widget Stack. COm isso colocamos o texto na parte de baixo da stack
                Positioned(
                  right: 10,
                  bottom: 20,
					// o caontianer aqui é usadao para permitir a extilizaçãp
                  child: Container(
                    width: 300,
                    color: Colors.black54, //54% de opcacidade
                    padding: EdgeInsets.symmetric(
                      vertical: 5,
                      horizontal: 20,
                    ),
                    child: Text(
                      meal.title,
                      style: TextStyle(
                        fontSize: 26,
                        color: Colors.white,
                      ),
						// Para o caso de quebra de linha, quebrala
                      softWrap: true,
                      overflow: TextOverflow.fade,
                    ),
                  ),
                ),
              ],
            ),
            Padding(
              padding: EdgeInsets.all(20),
              child: Row(
                mainAxisAlignment: MainAxisAlignment.spaceAround,
                children: <Widget>[
                  Row(
                    children: <Widget>[
                      Icon(Icons.schedule),
                      SizedBox(width: 6),
                      Text('${meal.duration} min'),
                    ],
                  ),
                  Row(
                    children: <Widget>[
                      Icon(Icons.work),
                      SizedBox(width: 6),
                      Text(meal.complexityText),
                    ],
                  ),
                  Row(
                    children: <Widget>[
                      Icon(Icons.attach_money),
                      SizedBox(width: 6),
                      Text(meal.costText),
                    ],
                  ),
                ],
              ),
            )
          ],
        ),
      ),
    );
  }
}
````

## Tela da refeiççao

#### Esquema da tela da meal  `screens/meal_detai_screen.dart`

`_createSectionTitle` e `_createSectionContainer` são metodos que não so separam a estilizaçao do conteudo como tambem serviram para serem reutilizados

````dart
import 'package:flutter/material.dart';

import '../models/meal.dart';

class MealDetailScreen extends StatelessWidget {

  final Function(Meal) onToggleFavorite;
  final bool Function(Meal) isFavorite;

	// construtor
  const MealDetailScreen(this.onToggleFavorite, this.isFavorite);

  Widget _createSectionTitle(BuildContext context, String title) {
    return Container(
      margin: EdgeInsets.symmetric(vertical: 10),
      child: Text(
        title,
        style: Theme.of(context).textTheme.title,
      ),
    );
  }

	// esse método é para apenas separar a estilização pois está grande
  Widget _createSectionContainer(Widget child) {
    return Container(
      width: 330,
      height: 200,
      padding: EdgeInsets.all(10),
      margin: EdgeInsets.all(10),
      decoration: BoxDecoration(
        color: Colors.white,
        border: Border.all(color: Colors.grey),
        borderRadius: BorderRadius.circular(10),
      ),
      child: child,
    );
  }

  @override
  Widget build(BuildContext context) {
    final meal = ModalRoute.of(context).settings.arguments as Meal;

	// Começa com Scaffold por ser toda uma tela nova
    return Scaffold(
		// nome na appBar
      appBar: AppBar(
        title: Text(meal.title),
      ),
		// O corpo será um ScrollViewer
      body: SingleChildScrollView(
        child: Column(
          children: <Widget>[
			// Imagem
            Container(
              height: 300,
              width: double.infinity,
              child: Image.network(
                meal.imageUrl,
                fit: BoxFit.cover,
              ),
            ),
			// seçao de titulo definida num metodo separdo
            _createSectionTitle(context, 'Ingredientes'),

			// Criamos na verdade um Container separado para agente separar a estilizaçâo do conteudo mesmo dos ingredientes
            _createSectionContainer(

				// Lista de ingredientes, é necessário definri a quantiadded e elemento da lista
              ListView.builder(
                itemCount: meal.ingredients.length,
                itemBuilder: (ctx, index) {
                  return Card(
                    child: Padding(
                      padding: const EdgeInsets.symmetric(
                        vertical: 5,
                        horizontal: 10,
                      ),
                      child: Text(meal.ingredients[index]),
                    ),
                    color: Theme.of(context).accentColor,
                  );
                },
              ),
            ),
			// Titulo da seçâo de Passos
            _createSectionTitle(context, 'Passos'),
            _createSectionContainer(
              ListView.builder(
                itemCount: meal.steps.length,
                itemBuilder: (ctx, index) {
                  return Column(
                    children: <Widget>[
                      ListTile(
                        leading: CircleAvatar(
                          child: Text('${index + 1}'),
                        ),
                        title: Text(meal.steps[index]),
                      ),
						// barrra horitonal entre os compoentnes
                      Divider(),
                    ],
                  );
                },
              ),
            ),
          ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
        child: Icon(isFavorite(meal) ? Icons.star : Icons.star_border),
        onPressed: () {
          onToggleFavorite(meal);
        },
      ),
    );
  }
}
````

## onGenerateRoute e onUnkwonROute

Além da propriedade `routes` que vocE define em `main.dart` você pode definir `onGenerateROute` que é um metodo que vai gerar rotas mais dinamicamente e `onUnkwonRoute` que é a pageina a ser mostrada se for solicitado uma rota e ela não for encontrada.

````dart
onGenerateRoute: (setting) {
	if(settings.name = '/alguma-coisa){
		return null;
	} else if ( settings.name = '/outra-coisa){
		return null;
	else {
		return MaterialPageRoute(
			builder: (_){
				return CategoriesScreen();
		}
	}	
}
}

````dart
onUnkwonRoute: (setting) {
	return MaterialPageRoute(
			builder: (_){
				return CategoriesScreen();
		}
)}
````

### Tela de favoritos e App estruturado em Abas


tabs_screen.dart
+ Widget que vai controlar a transiçâo de tabs
+ Vamo por a tab na parte inferior em vez de superior (que foi a primeira que ele fez). vamos fazer com Bottom NavigationBar
+ Vamos também adicionar o `Drawer()`
````dart
import 'package:flutter/material.dart';

import 'categories_screen.dart';
import 'favorite_screen.dart';
import '../components/main_drawer.dart';
import '../models/meal.dart';

// State
class TabsScreen extends StatefulWidget {

  final List<Meal> favoriteMeals;

  const TabsScreen(this.favoriteMeals);

  @override
  _TabsScreenState createState() => _TabsScreenState();
}

class _TabsScreenState extends State<TabsScreen> {
  int _selectedScreenIndex = 0;
  List<Map<String, Object>> _screens;

  @override
  void initState() {
    super.initState();
    _screens = [
      {
        'title': 'Lista de Categorias',
        'screen': CategoriesScreen(),
      },
      {
        'title': 'Meus Favoritos',
        'screen': FavoriteScreen(widget.favoriteMeals),
      },
    ];
  }

  _selectScreen(int index) {
    setState(() {
      _selectedScreenIndex = index;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(
          _screens[_selectedScreenIndex]['title'],
        ),
      ),
      drawer: MainDrawer(),
      body: _screens[_selectedScreenIndex]['screen'],
      bottomNavigationBar: BottomNavigationBar(
        onTap: _selectScreen,
        backgroundColor: Theme.of(context).primaryColor,
        unselectedItemColor: Colors.white,
		// mudar a cor do icone da tab em que se está
        selectedItemColor: Theme.of(context).accentColor,
		// necessaria pra sabre qual tab esta usando
        currentIndex: _selectedScreenIndex,
        items: [
          BottomNavigationBarItem(
            icon: Icon(Icons.category),
            title: Text('Categorias'),
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.star),
            title: Text('Favoritos'),
          ),
        ],
      ),
    );
  }
}
````


````dart
import 'package:flutter/material.dart';
import '../components/meal_item.dart';
import '../models/meal.dart';

class FavoriteScreen extends StatelessWidget {

  final List<Meal> favoriteMeals;

  const FavoriteScreen(this.favoriteMeals);

  @override
  Widget build(BuildContext context) {
    if (favoriteMeals.isEmpty) {
      return Center(
        child: Text('Nenhuma refeição foi marcada como favorita!'),
      );
    } else {a
      return ListView.builder(
        itemCount: favoriteMeals.length,
        itemBuilder: (ctx, index) {
          return MealItem(favoriteMeals[index]);
        },
      );
    }
  }
}
````
			
Drawer
+definindo Drawer um menuzinho que é puxado a esquerda ao clicar no hamburguer.
+ `pushReplacementNamed` Nâo é um simples push, ele retira a ultima e coloca uma outra, sem isos fica bugado pois vai ficar tela1 > Drawer > tela1 pois acessar o Drawe vai ser uma tela separada ai se você ir para a mesma tela anterior vai haver a mesma tela duas vezes na stack.

````dart
// components/main_drawer.dart
import 'package:flutter/material.dart';

import '../utils/app_routes.dart';

class MainDrawer extends StatelessWidget {

  Widget _createItem(IconData icon, String label, Function onTap) {
    return ListTile(
      leading: Icon(
        icon,
        size: 26,
      ),
      title: Text(
        label,
        style: TextStyle(
          fontFamily: 'RobotoCondensed',
          fontSize: 24,
          fontWeight: FontWeight.bold,
        ),
      ),
      onTap: onTap,
    );
  }

  @override
  Widget build(BuildContext context) {
    return Drawer(
      child: Column(
        children: <Widget>[
          Container(
            height: 120,
            width: double.infinity,
            padding: EdgeInsets.all(20),
            color: Theme.of(context).accentColor,
            alignment: Alignment.bottomRight,
            child: Text(
              'Vamos Cozinhar?',
              style: TextStyle(
                fontWeight: FontWeight.w900,
                fontSize: 30,
                color: Theme.of(context).primaryColor,
              ),
            ),
          ),
          SizedBox(height: 20),
          _createItem(
            Icons.restaurant,
            'Refeições',
            () => Navigator.of(context).pushReplacementNamed(AppRoutes.HOME),
          ),
          _createItem(
            Icons.settings,
            'Configurações',
            () => Navigator.of(context).pushReplacementNamed(AppRoutes.SETTINGS),
          ),
        ],
      ),
    );
  }
}
`````

## Tela de configuraçao

settings_scren.dart

PULEI ESSA PARTE E a parte de maracr favorito e a seguinte

````dart
import 'package:flutter/material.dart';

import '../components/main_drawer.dart';
import '../models/settings.dart';

class SettingsScreen extends StatefulWidget {

  final Settings settings;
  final Function(Settings) onSettingsChanged;

  const SettingsScreen(this.settings, this.onSettingsChanged);

  @override
  _SettingsScreenState createState() => _SettingsScreenState();
}

class _SettingsScreenState extends State<SettingsScreen> {

  Settings settings;

  @override
  void initState() {
    super.initState();
    settings = widget.settings;
  }

  Widget _createSwitch(
    String title,
    String subtitle,
    bool value,
    Function(bool) onChanged,
  ) {
    return SwitchListTile.adaptive(
      title: Text(title),
      subtitle: Text(subtitle),
      value: value,
      onChanged: (value) {
        onChanged(value);
        widget.onSettingsChanged(settings);
      }
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Configurações'),
      ),
      drawer: MainDrawer(),
      body: Column(
        children: <Widget>[
          Container(
            padding: EdgeInsets.all(20),
            child: Text(
              'Configurações',
              style: Theme.of(context).textTheme.title,
            ),
          ),
          Expanded(
            child: ListView(
              children: <Widget>[
                _createSwitch(
                  'Sem Glutén',
                  'Só exibe refeições sem glúten!',
                  settings.isGlutenFree,
                  (value) => setState(() => settings.isGlutenFree = value),
                ),
                _createSwitch(
                  'Sem Lactose',
                  'Só exibe refeições sem lactose!',
                  settings.isLactoseFree,
                  (value) => setState(() => settings.isLactoseFree = value),
                ),
                _createSwitch(
                  'Vegana',
                  'Só exibe refeições veganas!',
                  settings.isVegan,
                  (value) => setState(() => settings.isVegan = value),
                ),
                _createSwitch(
                  'Vegetariana',
                  'Só exibe refeições vegetarianas!',
                  settings.isVegetarian,
                  (value) => setState(() => settings.isVegetarian = value),
                ),
              ],
            ),
          )
        ],
      ),
    );
  }
}
````
