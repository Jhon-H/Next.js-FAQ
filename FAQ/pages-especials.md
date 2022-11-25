# Pages especiales (_app, _document, _error)

}
## _app.js

- Persiste layouts entre cambios de paginas
- Mantener estado cuando navega entre paginas
- Injectar data en las paginas
- agregar css global

Component: El componente a renderizar
pageProps: props de la pagina retornado por algun metodo de fetching

## _document.js

- Solo se renderiza en el servidor
- Puede actualizar los tags `<html>` y `<body>`  de una página


## _error.js

- Define un componente de error más general y avanzado
- Puede indicarle de acuerdo al statuCode que contenido mostrar