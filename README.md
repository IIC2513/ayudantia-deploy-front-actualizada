# Pasos para deployar tu frontend (Por si no te quedó claro con el video)

## Crea tu aplicación de React

En primer lugar, tienes que crear tu aplicación y correrla. Para ello debes correr el siguiente comando en el directorio principal de tu repositorio de GitHub:

`yarn create vite`

Luego, para correr la aplicación:

`yarn dev`

## Buildea la aplicación

Para hacer esto se debe correr el comando:

`yarn build`

Luego de haberlo ejecutado, se debe haber creado una carpeta que se llama *dist*.

## Realizar el setup del workflow de node.js

Al terminar los pasos previos, es importante que hagas push a los cambios que realizaste previamente. Después, entras a la página del repositorio y accedes a actions. Posteriormente, debes buscar node.js, realizar la configuración y commitear los cambios. Luego debes hacer `git pull` de los cambios en que realizaste del workflow de node.js.

## Login en Netlify y creación de página

En este paso vinculas tu cuenta de GitHub con Netlify y creas una página usando y arrastrando la carpeta `./dist` que se encuentra en el directorio principal del repositorio.

## Obtención de Secrets

En este paso debes crear un token en "User Settings" -> "Applications" -> "Personal access token" en la página de Netlify. Recordar que la duración del token debe ser de por lo menos la duración del curso.

Posteriormente, se debe incluir en los secrets de git, con el nombre de `NETLIFY_API_TOKEN`. Esto se puede hacer en "Repositorio de GitHub" -> "Settings" -> "Secrets and variables" -> "Actions" -> "Repository secrets".

De una manera similar se debe recuperar el id de la página de Netlify. Para ello, se ingresa a "Sites" -> "Nombre de tu sitio" -> "Site configuration" -> "Site details" -> "Site ID".

Finalmente, se incluye en los secrets del repositorio en GitHub, con el nombre `NETLIFY_SITE_ID`.

## Cambio del archivo node.js.yml

El archivo del video anterior es el siguiente:

```
name: Node.js CI

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3
    - name: Use Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v3
      with:
        node-version: ${{ matrix.node-version }}
        cache: 'npm'
    - run: npm ci
    - run: npm run build --if-present
    - run: npm test

    - name: Netlify Deploy

      env:
        NETLIFY_AUTH_TOKEN: ${{ secrets.NETLIFY_AUTH_TOKEN }}
        NETLIFY_SITE_ID: ${{ secrets.NETLIFY_SITE_ID }}
      run: netlify deploy --prod
```

Para que el workflow funcione con vite, debe ser cambiado por lo siguiente:

```
name: Deploy website

on: 
    push:
        branches: [ "main" ]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Repository Checkout
        uses: actions/checkout@v4

      - name: Setup NodeJS
        uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: "npm"
        
      - name: Install Netlify
        run: yarn add netlify-cli

      - name: Install Dependencies
        run: yarn install

      - name: Build project
        run: yarn build

      - name: Deploy to Netlify
        id: netlify_deploy
        run: |
          netlify deploy --prod \
            --dir dist \
            --site ${{ secrets.NETLIFY_SITE_ID }} \
            --auth ${{ secrets.NETLIFY_API_TOKEN }}
```

Finalmente, se debe realizar un commit de los cambios y el deployment del frontend debería estar listo.

Ayudantía realizada por @PSepulvedaS