# Filtros

- `where: { slug: "js" }`
[Filtrros de coleccion](https://www.contentful.com/developers/docs/references/graphql/#/reference/collection-filters)

Puede usar operadores logicos

```
query {
  friendlyUserCollection(where: {
    OR: [
      { name: "Hans" },
      { name: "Joe" }
    ]
    age_gte: 30,
    age_lte: 40
  }) { name }
}
```

- `preview: true`
- `locale: "de-DE"`
- `limit: 2`
- `skip: 3` --> regresa limit y total
- `order: "date_DESC"` --> ordenada basandose en el campo date en orden descendiente
- `order: "sys.id"` --> ordenada basandose en el campo id
- `order: "-sys.id"` --> invierte el orden anterior


## Transformación de imagenes

```grahpql
{
  asset(id: "2") {
    title
    url(transform: {
      width: 500,
      height: 300,
      resizeStrategy: FILL,
      resizeFocus: BOTTOM,
      backgroundColor: "rgb:321032",
      cornerRadius: 100,
      format: JPG,
      quality: 90
    })
  }
}
```