# Queries

## Argumentos

Para cada campo y objeto anidado es posible pasarle argumentos

```grahpql
query {
  human(id: "1000") {
    name
  }
}
```

## Tipos de graphql

- Int  (enetero de 32 bits)
- Float
- String
- Boolean
- ID

## Alias

Es posible cambiarle el nombre al resutado de un campo

```graphql
query {
  empireHero: hero(episode: EMPIRE){
    name
  }
  jediHero: hero(episode: JEDI){
    name
  }
}
```

## Fragmentos

Son unidades reutilizables, permiten construir conjuntos de campos y luego incluirlos en consultas

Estructura:

```graphql
query BooksList($id: Int = 7) {
  books {
    ...booksFields
  }

  book(id: $id) {
    ...booksFields
  }
}

fragment booksFields on Book {
  id
  title
}
```

### Variables en fragmentos

Los fragmentos puede acceder a las variables declaradas en la consulta

```graphql
query HeroComparasion($first: Int = 3) {
  left: hero(episode:  EMPIRE) {
    ...comparisionFields
  }
  right: hero(episode:  JEDI) {
    ...comparisionFileds
  }
}


fragment comparisionFields on Character {
  name
  friendsConnection(first: $first) {
    toitalCount
  }
}
```

## Directivas

Nos permite cambiar de forma dinámica la estructura de nuestra consulta usando variables

Hay 2:

- `@include(if: Boolean)`
- `@skip(if: Boolean)`

```graphql
query Hero($episode: Episode, $withFriends: Boolean!) {
  hero(episode: $episode) {
    name
    friends @include(if: $withFriends) {
      name
    }
  }
}
```

## Fragmentos en linea

Si está haciendo una consulta a un campo que devuelve una interfaz o tipo de unión, deberá usar **fragmentos en linea** para acceder a los datos

```graphql
query Hero($ep: Episode!) {
  hero(episode: $ep) {
    name
    ...on Droid {
      primaryFunction
    }
  }
}
```

En este caso el campo hero devuelde un tipo ``Character``, que puede ser ``Human`` o ``Droid`` (dependiendo del argumento ``episode``)
Solo puede obtener campos que existen en la interfaz ``Character`` (que comparta tanto ``Human`` comop ``Droid``)
En este caso es el campo ``name``, para obtener los campo de un tipo en concreto de usan los **fragmentos en línea**

Estos campos solo se ejcutaran si el tipo devuelto por Character (el tipo que regresa hero) es el qe se solicita

## Metacampos

Algunas veces no se sabe qué tipos obtendrá del servicio (por ejemplo al usar fragmentos en linea), grahpqlñ le permite solicitar metacampos, por ejemplo __typename para ohbtnern el nombre del tipo en ese punto

```grahpql
{
  search(text: "an") {
    __typename
    ...on Droid {
      name
    }

    ...on Human {
      name
    }
  }
}
```
