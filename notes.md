# Nouveautés E04

## Composants

Dans nos projets React, on pourrait décrire toute l'interface en faisant un gros bloc

```js
import React from 'react';
import { render } from 'react-dom';

const rootElement = (
  <div>
    <header>
      <h1>Coucou</h1>
    </header>
    <main>
      <p>Hello world</p>
    </main>
  </div>
);

render(rootElement, document.getElementById('root'));
```

Plutot que de tout faire d'un bloc on va exploiter une architecture de composants, c'est à dire qu'on va exploser tout ça en plein de petit réutilisables et paramétrables. On va faire des fonctions qui renvoient chaque morceau d'interface, des fonctions qui retournent du JSX

```js
import React from 'react';
import { render } from 'react-dom';

// un composant est une fonction qui commence par une majuscule par convention
function Header() {
  // qui retourne un element React (React.createElement()) écrit ici en JSX
  return (
    <header>
      <h1>Coucou</h1>
    </header>
  );
}

function Content() {
  return (
    <main>
      <p>Hello world</p>
    </main>
  );
}

const rootElement = (
  <div>
    <Header />
    <Content />
  </div>
)

render(rootElement, document.getElementById('root'));
```

Plutot que de tout écrire dans un seul fichier, on va écrire dans un plusieurs, on va exporter d'un coté pour importer la ou on se sert des composants

```js
// Header.js
import React from 'react';

function Header() {
  return (
    <header>
      <h1>Coucou</h1>
    </header>
  );
}

export default Header;
```

On peut aussi écrire la fonction en fléché

```js
// Header.js
import React from 'react';

const Header = () => (
  <header>
    <h1>Coucou</h1>
  </header>
);

export default Header;
```

Ailleurs dans un autre composant je peux importer le premier

```jsx
// App.js
import React from 'react';

import Header from './chemin/vers/Header';

const App = () => (
  <div>
    <Header />
    <p>Hello world</p>
  </div>
);

export default App;
```

## Prop-types

Nos Composants peuvent être configurés en passant des props

```jsx
// Title.js
import React from 'react';

const Title = ({ text }) => (
  <h1>{text}</h1>
);

export default Title;
```

Pour éviter de mauvaises utilisations du composant, on va valider le type des données attendues en props

Pour cela on a besoin de la library `prop-types`

```jsx
// Title.js
import React from 'react';
// On importe les types de proptypes avec PropTypes écrit avec une majuscule
import PropTypes from 'prop-types';

const Title = ({ text }) => (
  <h1>{text}</h1>
);

// la propriété qu'on éfinit sur le composant s'écrit en camelCase
// on y associe un objet définissant la forme de nos props
Title.propTypes = {
  text: PropTypes.string,
}

export default Title;
```

Si une props est obligatoire pour le bon fonctionnement de mon composant je l'indique

```jsx
// Title.js
import React from 'react';
// On importe les types de proptypes avec PropTypes écrit avec une majuscule
import PropTypes from 'prop-types';

const Title = ({ text }) => (
  <h1>{text}</h1>
);

// la propriété qu'on éfinit sur le composant s'écrit en camelCase
// on y associe un objet définissant la forme de nos props
Title.propTypes = {
  text: PropTypes.string.isRequired,
}

export default Title;
```

[Doc des Proptypes](https://fr.reactjs.org/docs/typechecking-with-proptypes.html)

On pourra valider des types complexes par exemple un tableau de qqch avec `arrayOf` ou un objet avec une forme spécifique avec `shape`

```js
Ingredients.propTypes = {
  listOfIngredients: PropTypes.arrayOf(
    PropTypes.shape({
      id: PropTypes.number.isRequired,
      quantity: PropTypes.number.isRequired,
      name: PropTypes.string.isRequired,
      unit: PropTypes.string.isRequired,
    }),
  ).isRequired,
};
```

## Utilisation de tableau dans du jsx

Lorsqu'on crée un élement React on peut lui passer en enfant un tableau

```js
const Ingredients = () => {
  return React.createElement('ul', { className: 'ingredients' }, ['farine', 'sel']);
}
```

On peut aussi lui passer un tableau d'élements

```js
const Ingredients = () => {
  return React.createElement('ul', { className: 'ingredients' }, [
    React.createElement('li', null, 'Farine'),
    React.createElement('li', null, 'Sucre'),
  ]);
}
```

On peut se servir de map pour construire un tableau d'élements à partir d'un tableau d'origine

```js
const Ingredients = ({ listOfIngredients }) => {
  console.log(listOfIngredients); // ['sucre', 'sel']
  const myIngredients = listOfIngredients.map((currentIngredient) => {
    return React.createElement('li', null, currentIngredient);
  });
  return React.createElement('ul', { className: 'ingredients' }, myIngredients)
};
```

Ou avec JSX

```js
const Ingredients = ({ listOfIngredients }) => {
  console.log(listOfIngredients); // ['sucre', 'sel']
  const myIngredients = listOfIngredients.map((currentIngredient) => {
    return <li>{currentIngredient}</li>;
  });
  return (
    <ul className="ingredients">
      {myIngredients}
    </ul>
  );
}
```

On peut même zapper la variable intermédiaire 

```js
const Ingredients = ({ listOfIngredients }) => (
  <ul className="ingredients">
      {listOfIngredients.map((currentIngredient) => (
        <li>{currentIngredient}</li>
      ))}
  </ul>
)
```

Chaque fois qu'on manipule un tableau d'élements React il faut une propr `key` unique qui identifie chaque élement du tableau
Cela permettra à React de continuer à faire des rendus performants

```js
const Ingredients = ({ listOfIngredients }) => (
  <ul className="ingredients">
      {listOfIngredients.map((currentIngredient) => (
        <li key={currentIngredient.id}>{currentIngredient.name}</li>
      ))}
  </ul>
)
```

[Doc des proptypes](https://fr.reactjs.org/docs/typechecking-with-proptypes.html)

### Utilisation du spread operator dans du JSX

Le spread operator `...` sert à déverser le contenu d'un objet ou d'un tableau, ça peut être pratique dans du JSX pour passer toutes les propriétés d'un objet d'un coup en props

Plutot que de passer toutes les valeurs des propriétés une par une

```jsx
<Ingredient
  quantity={currentIngredient.quantity}
  name={currentIngredient.name}
  unit={currentIngredient.unit}
/>
```

Je fais la meme chose en 1 fois en deversant le contenu de l'objet

```jsx
<Ingredient 
  {...currentIngredient}
/>
```

## SASS

[Doc de Sass](https://sass-lang.com/documentation)

Sass est un preprocessor CSS qui nous permet d'écrire du CSS avec des syntaxes supplémentaires, nos fichiers seront analysés par l'outil Sass et transformés en CSS. Avec Webpack ça se fait de manière transparente au moment du build.

On va utiliser la syntaxe SCSS, il existe aussi la syntaxe SASS sans accolade et sans point virgule.

```css
.content {
  background-color: grey,
}

.content-title {
  color: red;
  font-size: 2em;
}

.content-description {
  font-size: 1.2em,
}
```

```scss
// en scss déjà on peut faire des commentaires sur une ligne
// on peut imbriquer des selecteurs
.content {
  background-color: grey;

  .content-title { // .content .content-title
    color: red;
    font-size: 2em;
  }

  .content-description { // .content .content-description
    font-size: 1.2em;
  }
}


// on peut éviter de générer des selecteurs trop longs avec &
// & sera remplacé par le selecteur parent
.content {
  background-color: grey;

  &-title { // .content-title
    color: red;
    font-size: 2em;
  }

  &-description { // .content-description
    font-size: 1.2em;
  }
}
```

Il n'y a pas de limite au niveau d'imbrication

```scss

div {
  h1 { // div h1
    color: orange;

    span { // div h1 span
      color: red;
    }
  }
}
```

## Les petits à coté de la journée

### `::before / ::after`

Il s'agit de 2 pseudo elements css, c'est à dire des selecteurs qui permettent de cibler un élement qui n'existe pas vraiment, c'est comme si on ciblait un span en début (before) ou en fin (after) de balise

Ce sera pratique pour ajouter des élement purement décoratif

Pour que le selecteur ait un effet on doit obligatoirement préciser une propriété content

```css
.element::before {
  content: '*';
  color: red;
}

/* Si on ne veut pas mettre de texte on doit mettre une chaine vide */
.item::after {
  content: '';
  height: 1px;
  width: 20px;
  background-color: #F18042;
  display: inline-block;
}
```
