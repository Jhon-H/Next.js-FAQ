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
