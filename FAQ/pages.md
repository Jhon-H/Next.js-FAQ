
# Páginas (pages/) y métodos de renderizado

## General

1. Next por defecto pre-renderiza las paginas (las que son sin datos)
2. Se recomiendas usar SSG sobre SSR siempre que se pueda por razones de performance y SEO
3. Sin importar el método de renderizado, toda información pasada por props al componente se podrá ver en el cliente (cuidado con la información sensible)
4. Estos métodos de renderizado solo están soportados en pages/, no se pueden usar en componentes u otros lugares

## Server Side Rendering (SSR)

1. SSR tambien se puede usar para solkicutar rutas dinamicas, puede regresar: la data, notFound, redirect
2. SSR se puede cachear especificando el header cache-control

  ```ts
  export async function getServerSideProps({ req, res }) {
    res.setHeader(
      'Cache-Control',
      'public, s-maxage=10, stale-while-revalidate=59'
    )

    return {
      props: {},
    }
  }
  ```

3. Es innecesario llamar a api/some-route.js desde getServerSideProps, puesto que esta funcion se ejecuta en el mismo backend
4. Si ocurre un error dentro de getServerSideProps se mostrará pages/500.js
5. [Cuando usar SSR](https://nextjs.org/docs/basic-features/data-fetching/get-server-side-props#when-should-i-use-getserversideprops)

## Static Site Generation (SSG)

1. Consejo: si tienes pocas páginas (ej: posts), generalas estaticamente, si son muchas es mejor mandar un arreglo vacio en getStatisPaths
2. [Cuando usar SSG](https://nextjs.org/docs/basic-features/data-fetching/get-static-props#when-should-i-use-getstaticprops)
3. [Cuando usar ISR](https://nextjs.org/docs/basic-features/data-fetching/get-static-paths#when-should-i-use-getstaticpaths)

## Incremental Static Generation (ISG)

1. Desúes que haber pasado el tiempo en elñ que se iba a revalidar la pagina, el sigueinte request iniciará dichq regeneracion
2. Si la regeneracion falla, se usará la pagina que ya se había generado
3. Se puede revalidar una pagina manualmente --> res.revalidate(exacatPath) (se debe tener cierta configuracion y tokens, revisar docs de next)

```bash
https://<your-site.com>/api/revalidate?secret=<token>
```

```ts
// pages/api/revalidate.js

export default async function handler(req, res) {
  // Check for secret to confirm this is a valid request
  if (req.query.secret !== process.env.MY_SECRET_TOKEN) {
    return res.status(401).json({ message: 'Invalid token' })
  }

  try {
    // this should be the actual path not a rewritten path
    // e.g. for "/blog/[slug]" this should be "/blog/post-1"
    await res.revalidate('/path-to-revalidate')
    return res.json({ revalidated: true })
  } catch (err) {
    // If there was an error, Next.js will continue
    // to show the last successfully generated page
    return res.status(500).send('Error revalidating')
  }
}
```

4. [Información técnica vercel](https://vercel.com/docs/concepts/incremental-static-regeneration/overview?utm_source=next-site&utm_medium=docs&utm_campaign=next-website)

## Client-Side Rendering (CSR)

1. No lo use a menos que sea necesario
2. Puede generar una página de forma estática e hidratarla en el cliente (puede usar las props pasadar por SSR/SSG como estado inicial)
3. SWR está interesante

## Páginas de error (404, 505, _error)

1. Se puede usar getStaticProps dentro de pages/500.js y pages/404.js
2. Para otros errores o tener pagina de error general use pages/_error.js (solo se ejecuta en produccion), da un 404 si se accede a el por rutas
3. Puedes renderizar ```<Error />``` componentes de ```next/error```
