# Buenas practicas

## JSON (con GZIP)

Las respuestas de graphql son JSON, como es principalmente texto se integra bien con GZI´P

Se recomienda que cualquier servicio GRAPHQL habilite GZIP en producion y envielo en el encabezado `Accept-Encoding: gzip`

