immo-app/
├── src/
│   ├── components/
│   │   ├── PropertyCard.vue   # Carte d'une propriété
│   │   ├── PropertyList.vue   # Liste des propriétés
│   │   └── PropertyFilter.vue # Filtres de recherche
│   ├── views/
│   │   ├── HomeView.vue
│   │   ├── PropertiesView.vue  # Liste des biens
│   │   └── PropertyDetail.vue  # Détail d'un bien
│   ├── stores/
│   │   ├── properties.js       # Gestion des propriétés
│   │   └── favorites.js        # Gestion des favoris
│   ├── data/
│   │   └── properties.js       # Catalogue des biens
│   ├── router/
│   │   └── index.js            # Configuration des routes
│   ├── App.vue                 # Composant racine
│   └── main.js                 # Point d'entrée 
└── index.html



# UNE SEULE COMMANDE sur le VPS
kubectl apply -f https://raw.githubusercontent.com/VOTRE_USERNAME_GITHUB/immo-app/main/argocd/application.yaml

testons