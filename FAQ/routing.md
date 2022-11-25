# Sitema de rutas

## Qué es `pages/posts/[slug].js` ?

Son segmentos dinámicos, por ejemplo, `http://localhost/posts/post-1` y `http://localhost/posts/123` entraran a `[slug].ts`

- Tambien está el caso que lo dinámico no sea la última parte del path, por ejemplo, el archivo settings que sirve para todos los usuarios

Nota: el segmento dinámico y cualquier query params se combinará en un solo objeto: `post/bar?foo=123` --> `{ slug: "bar", foo: "123" }`

Nota2: segmentos dinámicos mutiples trabajarán de la misma manera `pages/post/[pid]/[comment].js`

`pages/[username]/settings.js`, se llama con `http://localhost/jhon/settings` y `http://localhost/maria/settings`

## Qué pasa si tengo más de una ruta dinámica en un mismo path ?

¿¿¿???. Mire **precendencia**

## Qué es `pages/auth/[...others].js`

Captura todas las rutas, va a coincidir con cualquier ruta que inicia con `/auth`, ejemplo, `/auth/login`, `/auth/user/3`, `/auth/user/profile/basic`

En este caso el objeto de params en el router va a tener `{ others: ["a", "b"] }`

Nota: **Optional catch all routes** si se agrega otros corchetes `pages/posts/[[..slug]].js` va a coincidir con `pages/post`, `pages/post/a`, `pages/post/a/b`, ...
Nota 2: Para optional catch all routes, para el caso `pages/posts` el objeto params del route va a llegar sin la propiedads **slug**

## Precedencia

1. Rutas definidas
2. Rutas dinámicas
3. Catch all routes

Ej:

- `pages/post/create.js` --> match con `post/create`
- `pages/post/[pid].js` --> match con `post/a`, `post/b`, pero no `post/create`
- `pages/post/[...slug].js` --> match con `post/1/2`, `post/a/b/c`, pero no `post/create` ni `post/abc`

## Shallow

Permite cambiar la URL sin ejecutar métodos de fetching, se actualizará **pathname** y **query** via **router**

Nota: Solo funciona para cambios de url de la misma página

```ts
// Current URL is '/'
function Page() {
  const router = useRouter()

  useEffect(() => {
    // Always do navigations after the first render
    router.push('/?counter=10', undefined, { shallow: true })
  }, [])

  useEffect(() => {
    // The counter changed!
  }, [router.query.counter])
}
```

## Internacionalizacio rutas

Next soporta internacionalizacion en las rutas

Primero configure que **locales** va a soportar su app, y los dominios de cada una si los hay

```ts
// next.config.js
module.exports = {
  i18n: {
    // These are all the locales you want to support in
    // your application
    locales: ['en-US', 'fr', 'nl-NL'],
    // This is the default locale you want to be used when visiting
    // a non-locale prefixed path e.g. `/hello`
    defaultLocale: 'en-US',
    // This is a list of locale domains and the default locale they
    // should handle (these are only required when setting up domain routing)
    // Note: subdomains must be included in the domain value to be matched e.g. "fr.example.com".
    domains: [
      {
        domain: 'example.com',
        defaultLocale: 'en-US',
      },
      {
        domain: 'example.nl',
        defaultLocale: 'nl-NL',
      },
      {
        domain: 'example.fr',
        defaultLocale: 'fr',
        // an optional http field can also be used to test
        // locale domains locally with http instead of https
        http: true,
      },
    ],
  },
}
```

Hay 2 estrategias de **locale**

- usar subpath: `/blog`, `fr/blog`, `en-US/blog`
- usar subdominio: `myapp.com`, `myapp.fr`

Para acceder al **locale** actual mire ``locale`` de ``useRouter``

Nota: por defecto next trata de detectar el **locale** basandose en el header ``Accept-Language`` y en el dominio.

Para moverse entre **locales**

`<Link href="/another" locale="fr">`

`router.push('/another', '/another', { locale: 'fr' })`

## Rutas públicas y privadas (estrategias)

- Si la página es generada de forma estática, use middlewares para verificar si está autenticado/logueado

```ts
export async function middleware(request: NextRequest, event: NextFetchEvent) {
  if (request.nextUrl.pathname.startsWith('/checkout')) {
    await checkoutMiddleware(request, event)
  }
}

async function checkoutMiddleware(req: NextRequest, ev: NextFetchEvent) {

  const token = req.cookies.get('token')?.value || ''

  try {
    await jwt.isValidToken(token)
    return NextResponse.next()

  } catch(error) {
    const { protocol, host, pathname } = req.nextUrl;

    return NextResponse.redirect(`${protocol}//${host}/auth/login?p=${pathname}`)
  }
}
```

- Si la página es generada con SSR, use el mismo SSR para vertificar si está atutenticado/logueado

```ts
export const getServerSideProps: GetServerSideProps = async ({ req, query }) => {
  const session = await getSession({ req })

  const { p = '/' } = query

  if (session) {
    return {
      redirect: {
        destination: p.toString(),
        permanent: false
      }
    }
  }

  return {
    props: { }
  }
}
```

- De la forma tradicional de react, cree un componente WithPrivateRoute que sea un HOC y que revisa como chidlren los que va a renderizar

```ts
const WithPrivateRoute = ({ children }) => {
  const router = useRouter();

  useEffect(() => {
    onAuthStateChanged((user) => {
      if (!user) {
        router.push('/login?p=currentPage');
      }
    });
  }, []);

  // Tener cuidado porque se renderiza el children la primera vez
  // ej: usar useState para saber si ya se verificó la autenticación

  return <>{children}</>;
};
```

```ts
export default function ProfilePage () => {}
ProfilePage.Auth = WithPrivateRoute
```

```ts
const PublicRoute = ({ children }) => <>{children}</>;

function MyApp({ Component, pageProps }) {
  const Auth = Component.Auth || PublicRoute;

  return (
    <Auth>
      <Component {...pageProps} />
    </Auth>
  );
}
```
