# Assistant Accords Fournisseurs — extension Chrome

Un assistant IA de gestion de flotte : saisissez une immatriculation, et
discutez ensuite en langage naturel comme avec un responsable flotte
expérimenté (garages recommandés, plafonds de validation, assistance,
contrôle technique, contrat, etc.), sans jamais ouvrir un PDF.

## Installation (mode développeur)

1. Ouvrez `chrome://extensions` dans Chrome.
2. Activez le **Mode développeur** (en haut à droite).
3. Cliquez sur **Charger l'extension non empaquetée**.
4. Sélectionnez ce dossier (`extension/`).
5. L'icône apparaît dans la barre d'outils : cliquez dessus pour ouvrir le chat.

Aucune compilation n'est nécessaire : le code est prêt à l'emploi.

### Autoriser l'ouverture des PDF locaux (optionnel)

Le bouton « 📂 Ouvrir le contrat » ouvre le chemin renseigné (ex.
`D:\Contrats\Euromaster.pdf`). Pour que Chrome autorise l'ouverture de
fichiers `file://` depuis l'extension : `chrome://extensions` → carte de
l'extension → **Détails** → activez **Autoriser l'accès aux URL de
fichiers**.

## Module véhicules (nouveau)

- Saisissez une immatriculation (ex. `AA-855-ZA`) dans le chat : la fiche du
  véhicule (client, marque, modèle, loueur…) se charge automatiquement, ainsi
  que la fiche d'exploitation du client si elle existe. Toutes les questions
  suivantes portent automatiquement sur ce véhicule — la barre en haut du
  chat le rappelle, avec un bouton ✕ pour changer de véhicule.
- Questions reconnues sans reformulation : garages recommandés par catégorie
  (pneus, pare-brise, carrosserie, vidange, batterie, distribution, freinage,
  climatisation), plafond de validation, comparaison automatique d'un devis
  chiffré au plafond (« devis de 1 200 € »), contrôle technique, assistance/
  dépannage, véhicule relais, garantie, facturation, assurance, cartes
  carburant, péages, contrat, et « qui appeler » (contacts de la fiche).
- **⚙ Administration → 🚗 Véhicules** : fiche par véhicule (immatriculation,
  client, société, marque/modèle, VIN, kilométrage, loueur, centre,
  conducteur, contrat, prestataires autorisés, historique).
- **⚙ Administration → 🗂 Fiches clients** : plafond, validateur/canal,
  garage préféré par catégorie (ou recherche automatique si non précisé),
  contrôle technique, assistance, véhicule relais, garantie, facturation,
  assurance, cartes carburant, péages, règles particulières, contacts.
- Un véhicule et une fiche de démonstration (`AA-855-ZA` / ABC Transport)
  sont préchargés à l'installation pour tester tout de suite ; supprimez-les
  quand vous ajoutez vos propres véhicules.
- Les accords fournisseurs supportent désormais un téléphone et une adresse,
  ce qui active les boutons 📍 Ouvrir Maps / 📞 Appeler / 📅 Prendre RDV sur
  les cartes de réponse liées à un véhicule.

## Règles imposées : marque & client (nouveau)

Certains clients ou certaines marques imposent un prestataire précis, sans
alternative possible (contrat flotte, garantie constructeur…). Deux niveaux,
cumulables :

- **Par client** — ⚙ Administration → 🗂 Fiches clients → section « Garages
  préférés par catégorie » : choisissez le prestataire pour une catégorie
  (ex. Pneumatiques) et cochez **« Obligatoire (aucune alternative) »**. Ne
  concerne que ce client.
- **Par marque** — ⚙ Administration → 🏷 Règles de marque : ex. « Iveco →
  concessionnaire officiel obligatoire ». S'applique à **tous les clients**
  ayant un véhicule de cette marque, sans rien reconfigurer par fiche.
  Laissez « Catégories concernées » vide pour que la règle s'applique à tout
  (pneus compris), ou limitez-la (ex. `Distribution;Carrosserie`).

Priorité en cas de question sur un véhicule : **règle de marque exclusive**
> **règle exclusive de la fiche client** > préférence non-exclusive de la
fiche > recherche automatique dans les accords. Quand une règle est active :

- Le chat affiche un badge **🔒 Prestataire imposé / Concession imposée**
  avec le contact réel, et une phrase explicite indiquant qu'aucune
  alternative n'est proposée.
- Si la marque impose un concessionnaire mais qu'aucun accord n'est encore
  enregistré pour cette marque, l'assistant le dit clairement et propose de
  lancer la recherche « garage à proximité » **filtrée sur cette marque
  uniquement** (pas de garage générique affiché).
- La recherche géolocalisée respecte la même règle : si elle est déclenchée
  alors qu'une règle de marque exclusive s'applique, seules les concessions
  officielles de cette marque sont listées.

Des exemples préchargés illustrent les deux niveaux : la fiche « ABC
Transport » impose Feu Vert pour les pneus, et une règle de marque impose
Martenat pour tout véhicule Iveco — modifiez-les ou supprimez-les selon vos
besoins réels.

## Recommandation de garage à proximité (nouveau)

Depuis la carte « 🚗 Véhicule trouvé », un bouton **🔍 Trouver un garage pour
l'entretien** lance une recherche géolocalisée :

1. L'utilisateur indique sa ville, ou autorise la géolocalisation du
   navigateur.
2. L'extension interroge **OpenStreetMap** (Nominatim pour géocoder la
   ville, Overpass API pour lister les garages/concessions à proximité) —
   **gratuit, sans clé API**.
3. Les résultats sont classés par : concession officielle de la marque du
   véhicule > prestataire déjà négocié dans vos accords fournisseurs >
   garage spécialisé dans la marque > concession d'une autre marque >
   garage multimarque, puis par distance.
4. Chaque garage affiche nom, adresse, distance, horaires et marques quand
   OpenStreetMap les renseigne, avec boutons 📞 Appeler / ✉️ E-mail /
   🌐 Site / 🧭 Itinéraire / 📅 Prendre RDV.

**Limite honnête à connaître** : contrairement à Google Places (API
payante), OpenStreetMap ne fournit ni note moyenne/avis clients ni prix
estimatif de révision. Ces deux informations ne sont donc **jamais
inventées** : la note renvoie vers un lien direct « voir sur Google Maps »,
et le prix ne s'affiche que s'il provient d'un vrai accord négocié dans vos
propres données. Si vous voulez des notes/avis réels à terme, il faudra
brancher une clé Google Places API payante (`search.js`/`garagefinder.js`
sont structurés pour accueillir une seconde source sans tout réécrire).

La carte affichée est une carte OpenStreetMap intégrée centrée sur la
position de l'utilisateur (sans épingle par garage, ce qui nécessiterait
Google Maps payant) — le bouton Itinéraire de chaque résultat ouvre la
destination exacte dans Google Maps.

## Prise en main

- À la première installation, des accords fournisseurs réels sont
  préchargés (Euromaster VL/PL, Feu Vert, Profil Plus, Martenat, Ketrucks,
  Norauto). Supprimez-les depuis **⚙ Administration** si besoin.
- **⚙ Administration** (clic sur la roue dentée, ou clic droit sur l'icône →
  Options) : gérez accords, véhicules et fiches clients.
- **Chat** : posez une question comme à un collègue (« Le client veut
  changer ses pneus, où l'envoyer ? », ou une immatriculation). L'assistant
  reconnaît les synonymes métier (pneu/roues/train avant, pare-brise/vitrage/
  impact, vidange/huile, etc.) et les mots-clés propres à chaque accord.
- **▤ Tableau de bord** : nombre d'accords, de prestataires, de catégories,
  et contrats qui expirent sous 30 jours.
- **☆ Favoris** : épinglez les prestataires que vous recommandez le plus.
- **↺ Historique** : les questions posées sont conservées localement,
  supprimables en un clic.
- Une notification Chrome native prévient automatiquement quand un contrat
  arrive à expiration dans moins de 30 jours.

Toutes les données restent **en local** sur le poste, via `chrome.storage.local`
— rien n'est envoyé sur un serveur externe par défaut.

## Compréhension du langage naturel

Le moteur (`search.js`) ne fait pas une simple recherche de mot-clé : il
normalise la question (accents, casse), l'étend via un dictionnaire de
synonymes métier (pneus, pare-brise, vidange, batterie, distribution, freins,
carrosserie, climatisation…), puis note chaque accord actif selon le nombre
de correspondances, la priorité définie par l'administrateur et le statut
épinglé. Il reconnaît aussi les tournures directes (« quelle remise chez
Feu Vert ? ») et les cas où le prestataire cité n'a aucun accord enregistré,
en proposant alors l'alternative recommandée.

Le même moteur détecte une immatriculation n'importe où dans le message
(formats SIV `AA-123-AA` et FNI `123 ABC 75`), garde le véhicule sélectionné
en mémoire de conversation, puis route chaque question vers l'intention
métier correspondante (garage par catégorie, plafond, devis à comparer,
contrôle technique, assistance…) avant de retomber, si rien ne correspond,
sur la recherche générique d'accords fournisseurs.

Dans **⚙ Paramètres IA**, vous pouvez basculer sur un moteur OpenAI si vous
souhaitez une compréhension encore plus fine des formulations très libres —
le moteur local reste le réglage par défaut et ne fait fuiter aucune donnée.

## Un mot sur les choix techniques

Le brief initial mentionnait React/TypeScript/Vite/Fuse.js. Cet
environnement de génération n'a pas d'accès réseau pour lancer `npm install`
ou un bundler, donc j'ai livré l'équivalent fonctionnel en **JavaScript
vanilla, HTML et CSS**, organisé en couches claires (`storage.js` = modèle,
`search.js` = logique métier, `popup.js`/`admin.js` = contrôleur/vue) —
c'est aussi ce qui vous permet de charger l'extension telle quelle, sans
étape de build à maintenir. Si vous voulez migrer vers React + Vite plus
tard (par exemple pour une UI plus riche), la séparation des fichiers rend
la bascule directe : chaque fichier `.js` peut devenir un module TypeScript
sans changer la logique.

Fichiers du projet :

```
manifest.json         Déclaration de l'extension (Manifest V3)
storage.js             Accès aux données (chrome.storage.local) : accords,
                       véhicules, fiches clients, règles de marque,
                       historique, paramètres
search.js              Moteur de compréhension d'intention + synonymes +
                       détection d'immatriculation + intentions véhicule +
                       règles imposées (marque/client)
garagefinder.js        Recherche de garages à proximité (Nominatim + Overpass,
                       OpenStreetMap, sans clé API)
background.js          Service worker : alarme + notifications d'expiration,
                       données de démonstration (accords, véhicule, fiche,
                       règle de marque)
popup.html/.css/.js    Le chat (vue par défaut de l'extension) + barre
                       véhicule sélectionné
admin.html/.css/.js    L'administration (CRUD accords / véhicules / fiches
                       clients / règles de marque, import en masse, paramètres)
styles.css             Design system partagé (tokens, composants)
icons/                 Icônes 16/48/128 px
```

## Améliorations possibles

Classées à peu près par rapport effort/valeur — aucune n'est urgente, mais
voici ce que je referais en priorité si vous voulez pousser plus loin :

**Rapide à faire**
- **Carte interactive multi-marqueurs.** Aujourd'hui la carte OSM n'affiche
  qu'un seul point (votre position) — c'est une vraie limite, pas un choix :
  l'iframe OpenStreetMap ne gère qu'un marker. En passant à **Leaflet.js +
  tuiles OSM** (gratuit, sans clé API, ~15 Ko), on pourrait afficher une
  épingle par garage directement sur la carte, cliquable. C'est la
  amélioration la plus rentable si vous utilisez souvent la recherche à
  proximité.
- **Export des données** (accords/véhicules/fiches) en CSV depuis
  l'administration, symétrique de l'import en masse actuel — utile pour
  sauvegarder avant de changer d'ordinateur ou pour partager avec un
  collègue.
- **Sauvegarde/restauration en un clic** de tout `chrome.storage.local`
  (un simple export/import JSON), pour ne pas perdre les données si Chrome
  est réinstallé.

**Utile si l'usage se confirme**
- **Rappels d'entretien basés sur le kilométrage** : un champ « kilométrage
  au dernier entretien » + seuil, avec notification quand un véhicule
  approche de sa révision — réutiliserait le système d'alarme déjà en place
  pour les contrats qui expirent.
- **Historique d'intervention structuré par véhicule** (aujourd'hui un champ
  texte libre) : date, prestataire, catégorie, montant — permettrait de
  calculer un coût d'entretien par véhicule dans le tableau de bord.
- **Import en masse pour les règles de marque** (aujourd'hui uniquement via
  formulaire un par un) — pertinent seulement si vous en accumulez beaucoup
  plus qu'une poignée.

**Plus lourd, à ne faire que si le besoin est réel**
- **Partage multi-collaborateurs.** `chrome.storage.local` est local à
  l'ordinateur : si plusieurs personnes doivent voir les mêmes accords,
  véhicules et fiches en temps réel, il faudra un backend partagé (Supabase,
  Firebase, ou un simple Google Sheet en lecture/écriture) — ce n'est plus
  une extension 100% locale, donc à ne faire que si le travail en solo
  devient un vrai frein.
- **Notes clients et prix de révision réels.** Actuellement volontairement
  absents (voir plus haut) pour ne jamais inventer de chiffres. Une vraie
  intégration nécessiterait une clé Google Places API (payante au-delà d'un
  certain volume).

Dites-moi si vous voulez que j'attaque l'un de ces points — la carte
multi-marqueurs (Leaflet) est probablement le meilleur rapport effort/valeur
si vous voulez que je m'y mette maintenant.
