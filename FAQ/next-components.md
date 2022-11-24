# Next components

## Image

- Mejora de performance: Retorna un tamaño adecuado de acuerdo al dispositivo
- Previene Cumulatice Layout Shift

### Urls

- Para imagenes locales, Next toma la información del with y heiht basandose en el archiv importado y le asigna esas medidad al componente de imagen
- Para imagenes remotas, se debe especificar el with y height
- Para dominios externos, es necesario especificar una lista de dominios permitidos

```ts
// next.confing.js

module.exports = {
  images: {
    domains: ['images.unspash.com']
  }
}
```

### Prioridad

Se recomienda agregar la propiedada ```priority``` a la imagen que será el Largest Contentful Paint para cada página

### Sizes

- Siempre especifique el tamaño de su imagen (si es una imagen local esto se hace de forma automatica)
- Si no conoce el tamaño de su imagen, use ```fill``` que se adapta al tamaño de su elemento padre. Otra opcion es normalizar las imagenes a un tamaño especifico

### Styles

- Use clases o ```style```
- Si usó ```fill```, el padre debe tener ````position: relative``` y ```display: block```

## Font

Debe instalarse por aparte. Lo que hace es descargar las fuentes de Google fonts que se estén usando en el proyecto, así evita hacer peticiones por las fuentes y previene **layout shift**

```bash
npm install @next/font
```

## Link

Por defecto hace un prefetch a las urls que estén en ```<Link>``` dentro del viewport para paginas generadas con **Static Generation**, si se desactiva aún hará el prefetch pero en el hover.

¿Se necesita especificar ```rel="noopenner noreferrer"```?
