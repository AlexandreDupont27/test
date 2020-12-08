# Nuxt PWA

Installer [Nuxt PWA](https://pwa.nuxtjs.org/) si vous ne l'avez pas installé lors de l'installation de Nuxt JS

```sh
$ npm i --dev @nuxtjs/pwa
```

Ensuite ajouté le plugin dans le BuildModule
```js
buildModule{
    '@nuxtjs/pwa',
}
```

# Icon
`nuxt.config`
```js
pwa:{
    icon:{ 
      sizes: [64, 120, 144, 152, 192, 384, 512], //(Valeur par défaut) l'icone doit être carré et dans le meilleur des cas mesuré 512x512px (Nuxt s'occupe de générer l'image dans les formats plus petit)
      targetDir: 'icon name' //(Optionnel) Si vous devez changer le nom de l'icone il faut le faire ici
      source: "@/static/icon.png" //(Valeur par défaut)
    },
} 
```
Plus d'info sur les [Icones](https://pwa.nuxtjs.org/icon)

# Manifest
`nuxt.config`
```js
pwa: {
    manifest: {
      name: 'Nom de l`application', //(Requis) Max 45 caractère
      short_name: 'Nom de l`application'(Optionnel) // Moin de 12 caractère si possible (Utiliser si le nom de l'application est trop long)
      description: ''(Optionnel) // Pour ajouter une description au manifeste
      lang: 'fr', //(Optionnel) langue du manifeste
      start_url: "/", //(Requis) Url de départ pour l'ouverture du manifeste
      theme_color: "#couleur" //(Requis) Couleur du thème
      background_color: '#couleur' //(Optionnel) Couleur du background
    },
}
```
Choix du display
Display | Description | Par défaut
-|-|-
fullscreen |  Ouvre le site en plein écran sans le style du navigateur 
|standalone| Ouvre le site comme si c'étais une application et cache le style du navigateur ainsi que l'url | Oui
|minimal-ui| Ouvre le site comme le mode `standalone` avec le minimum d'élément UI
|browser| Ouvre le site dans un navigateur standard
Plus d'info sur le [Display](https://developer.mozilla.org/en-US/docs/Web/Manifest/display)
Plus d'info sur le [Manifest](https://pwa.nuxtjs.org/manifest)

## Configuration du Workbox
`nuxt.config`

Config de base :
```js
workbox: {
    cacheName:'nom de la cache', //(Optionnel)
    offlineAnalytics:true,(Optionnel) //Permet le fonctionnement de Google Analytics en mode Offline (par défaut : false)
    preCaching: ['',''], //(Optionnel) Permet de pré-cacher des fichier ex : '/css/googleFont.css'
    config: {
        debug:true, // Mettre à false en Prod
    },
}
```
Pour spécifier la [Strategy](https://developers.google.com/web/tools/workbox/modules/workbox-strategies) :
```sh
offlineStrategy: 'StaleWhileRevalidate', // (Valeur par défaut : 'NetworkFirst')
```
Pour safari il faut créer le fichier `workbox-range-request.js` dans le dossier `plugins` : 

Ensuite coller ce code dans le fichier : `workbox-range-request.js`
```js
workbox.routing.registerRoute(
  /\.(mp4|webm)/,
  new workbox.strategies.CacheFirst({
    plugins: [
      new workbox.rangeRequests.RangeRequestsPlugin(),
    ],
  }),
  'GET'
);
```

Et ajouter le lien dans la config du workbox
```js
  cachingExtensions: ['@/plugins/workbox-range-request.js'], //Safari require rangeRequest
```

Pour mettre en cache les `Assets`
( Avec le `Debug:true`, vous pouvez voir les fichier manquant lorsque vous êtes en mode `Offline`)
```js
  offlineAssets: ['/css/webfonts', '/store', '/images'], //<-- Exemple de dossier à mettre en cache 
```

Pour ajouter d'autre serviceWorker : 
```js
importScripts:['~/plugins/nom du script']
```

Si vous voulez avoir une `Strategy` spécifique pour une route
```js
workbox: {
    runtimeCaching: [ //Custom routes to cache with specific strategy. Useful for caching requests to other origins.
        {
            urlPattern: '/produits', // <-- exemple
            handler: "staleWhileRevalidate", // <-- Choix de la Strategy
            options: {
                cacheName: "nom de la cache", 
                expiration: {
                    maxAgeSeconds: 60 * 60 * 24 * 7,
                },
            },
        }
    ],
}
```

Si votre site envoie des requêtes(Post, Fetch, etc) et que vous n'avez plus de connection, vous pouvez faires en sorte de stocker la requete et lorsque la connection sera revenu la requête va s'envoyer automatiquement

Pour commencer vous devez créer ce fichier : `workbox-sync.js`

Coller ce code : 
```js
const bgSyncPlugin = new workbox.backgroundSync.BackgroundSyncPlugin('formQueue', {
  maxRetentionTime: 24 * 60 // Retry for max of 24 Hours (specified in minutes)
});

workbox.routing.registerRoute(
  'Link API', //<-- Mettre le lien de l'API Ex : https:\\www.nmedia.ca/getNmedien
  new workbox.strategies.NetworkOnly({
    plugins: [bgSyncPlugin]
  }),
  'POST' <-- Spécifier le type de requête
);
```

Et ajouter le lien du plugin dans la config `cachingExtensions`
```js
workbox: {
   cachingExtensions: [
        '~/plugins/workbox-sync.js'
    ],
}

```
[Doc pour workbox Background Sync](https://developers.google.com/web/tools/workbox/modules/workbox-background-sync)
[Exemple pour Background Sync](https://medium.com/@mario.brendel1990/offline-post-requests-with-workbox-vuejs-4df0e9f93da9)






# Doc officiel : [pwa.nuxtjs.org](https://pwa.nuxtjs.org/)

[Information sur les PWA](https://web.dev/progressive-web-apps/)














