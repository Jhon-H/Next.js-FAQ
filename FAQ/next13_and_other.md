# Otros conceptos a tener en cuenta

## app/

- app/ (layouts, React server components, streaming react)

## /src

Si se agrega ``/pages`` dentro de /src este será el nuevo **root directory** (``/src``)

Si ya existe un directory ``/pages``, se ignorara ``/src/pages``

## Debugging

Cree un archivo **.vscode/launch.json**

```ts
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Next.js: debug server-side",
      "type": "node-terminal",
      "request": "launch",
      "command": "npm run dev"
    },
    {
      "name": "Next.js: debug client-side",
      "type": "chrome",
      "request": "launch",
      "url": "http://localhost:3000"
    },
    {
      "name": "Next.js: debug full stack",
      "type": "node-terminal",
      "request": "launch",
      "command": "npm run dev",
      "serverReadyAction": {
        "pattern": "started server on .+, url: (https?://.+)",
        "uriFormat": "%s",
        "action": "debugWithChrome"
      }
    }
  ]
}
```

Para de debuguear server-side `"dev": "NODE_OPTIONS='--inspect' next dev"`

## Measure performance

- Next.js Analytics
- Para construir uno propio
  
```ts
// pages/_app.js
export function reportWebVitals(metric) {
  console.log(metric)
}
```

## Preview mode

Nos permite saltarnos la generación estática, por ejemplo, cuando quiero ver los cambios draft en un CMS, en lugar de construir la página de forma estática, se renderizará en tiempo de request (para ese caso específico)

```ts
```

## Dynamic import

Lazy loading para componentes de React y librerias de terceros

```ts
import dynamic from 'next/dynamic'
import { Suspense } from 'react'

const DynamicHeader = dynamic(() => import('../components/header'), {
  suspense: true,
})

export default function Home() {
  return (
    <Suspense fallback={`Loading...`}>
      <DynamicHeader />
    </Suspense>
  )
}
```

## Muti zones

- Una zona es una implementación única de una aplicacion next
- Puede tener multiples zonas y fusionarlas en una sola app

Qué se necesita para implementar mutizonas ?

1. Asegurarse que cada aplicación tiene solo las páginas que necesita y no se repiten, es decir, la app **A** tiene `/blog`, entonces la app B no pueden tenerlo
2. Asegurarse de haber configurado un basePath
  
Se puede combinar usando ``rewrites`` o un Http proxy

## Absolute paths

```ts
{
  "compilerOptions": {
    "baseUrl": "."
  }
}

{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/components/*": ["components/*"]
    }
  }
}

// Home
import Button from 'components/button'
import Button from '@/components/button'
```

# getInitialProps

- Habilita SSR en una página y permite hacer **initial data population**
- Deshabilita la generación estática **automática**
- Es una versión legacy de getServerSideProps