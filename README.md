# neoncompass-site

Le site de **Neon Compass** — page de présentation, politique de
confidentialité, page d'assistance, page d'arrivée des liens de confirmation,
et `app-ads.txt` —
servi sur **`https://neoncompass.app`** par GitHub Pages.

## Pourquoi un dépôt de projet et pas `antoine-teston.github.io`

Un domaine personnalisé posé sur le dépôt `<utilisateur>.github.io` fait
rediriger **tout** `antoine-teston.github.io/*` vers ce domaine : l'espace
personnel deviendrait une redirection vers une seule app.

Posé sur un dépôt de projet, le domaine est servi à sa **racine** — le chemin
`/neoncompass-site/` disparaît. `antoine-teston.github.io` reste donc libre, et
chaque app suivante prend son propre dépôt et son propre domaine.

Conséquence à ne pas oublier : **sans le domaine personnalisé, ce dépôt publie
sur un chemin** (`antoine-teston.github.io/neoncompass-site/`), où Google ne
cherche pas `app-ads.txt`. Le domaine n'est pas un confort ici, il est la
condition.

## `app-ads.txt`

Une ligne, l'éditeur AdMob du projet :

```
google.com, pub-6912645985011194, DIRECT, f08c47fec0942fa0
```

Google ne le crawle qu'**après** la publication de l'app, et seulement si
`https://neoncompass.app` est déclaré comme site du développeur dans la fiche
App Store. L'état se lit dans AdMob → Apps → onglet *app-ads.txt*. Il vaut 10 à
30 % d'eCPM.

Le fichier grandira : chaque partenaire de médiation ajouté plus tard y pose sa
propre ligne.

## `/auth/confirme/`

La page où Supabase renvoie le navigateur après avoir vérifié une adresse
e-mail. C'est la valeur de `site_url` du projet, **avec sa barre oblique** :
sans elle, GitHub Pages répond `301`, et un lien de confirmation n'a pas à
payer un saut de redirection.

Elle **ne rouvre pas l'app pour y déposer la session**, et c'est délibéré :
`signUp()` ne passe pas de `redirectTo` et l'app n'a aucun `onOpenURL`, donc les
jetons ne seraient consommés par personne — l'écran ne bougerait pas, ce qui est
pire que rien. Le parcours est : lien → cette page → retour à l'app → connexion
avec le mot de passe qu'on vient de choisir. Le bouton « Ouvrir Neon Compass »
ne fait que ramener l'app au premier plan, et n'apparaît que sur iOS : ailleurs,
le schéma d'URL produirait un « impossible d'ouvrir la page » à la fin d'un
parcours réussi.

L'état par défaut est la **réussite**, sans JavaScript — GoTrue n'ajoute des
paramètres qu'en cas d'échec, et il les met tantôt dans la requête, tantôt dans
le fragment selon le flux. Les deux sont lus : sinon la moitié des liens périmés
afficheraient « c'est bon » à quelqu'un qui doit recommencer.

```sh
curl -s -o /dev/null -w "%{http_code}\n" https://neoncompass.app/auth/confirme/
```

## Vérifier après tout changement

Toujours **à la sortie**, jamais dans le dépôt — une construction Pages peut
échouer pendant que le fichier est bien commité.

Et toujours **dans les deux familles d'adresses**. Un domaine peut répondre
parfaitement en IPv4 et être cassé en IPv6 : OVH pose à l'achat un `A` *et* un
`AAAA` de parking, et l'IPv6 gagne chez tout client à double pile. Supprimer le
seul `A` laisse donc une panne qui n'apparaît qu'une sonde sur deux, avec pour
symptôme un certificat étranger — `CN=cluster121.hosting.ovh.net` — et non un
404 qu'on aurait vu tout de suite.

```sh
dig +short neoncompass.app A ; dig +short neoncompass.app AAAA
for v in 4 6; do
  curl -$v -sS -o /dev/null \
    -w "v$v %{remote_ip} %{http_code} %{content_type} redirections=%{num_redirects}\n" \
    https://neoncompass.app/app-ads.txt
done
```

Attendu, sur les deux lignes : `200 text/plain; charset=utf-8 redirections=0`,
et une adresse GitHub (`185.199.10{8,9,10,11}.153`, `2606:50c0:800{0,1,2,3}::153`).

## À faire

Rien en attente. La politique de confidentialité est publiée sous `/privacy/`
(exigée à trois endroits : formulaire de consentement UMP, fiche App Store,
réglages de l'app), et l'adresse de support est `contact@neoncompass.app`.
La page d'assistance est sous `/support/` (FR + EN sous `#en`) : c'est l'URL
à déposer dans le champ « URL d'assistance » de la fiche App Store, qui exige
une page et non une adresse.
