# neoncompass-site

Le site de **Neon Compass** — page de présentation et `app-ads.txt` — servi sur
**`https://neoncompass.app`** par GitHub Pages.

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

- Politique de confidentialité (exigée à trois endroits : formulaire de
  consentement UMP, fiche App Store, réglages de l'app).
- Adresse de contact pour le support App Store.
