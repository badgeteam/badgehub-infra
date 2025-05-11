## Keycloak setup

For CORS settings, we add the header modifications inside keycloak instead of NGINX. We add them under:

`Realm settings -> Security defenses`

For example:

`frame-src 'self' https://keycloak.domain.example http://localhost:3000; default-src 'self'; style-src 'self' 'unsafe-inline'; script-src 'self' 'unsafe-inline'; img-src 'self'; connect-src 'self'; form-action 'self' https://badgehub.domain.example/ http://localhost:3000; frame-ancestors 'self'; object-src 'none';`
