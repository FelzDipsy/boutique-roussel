# Test hypothèse 1 ticket 4
Test il y a bien le bouquet petit qui se transforme en grand.

# 1er Prompt:
Pour celle ci l'hypothèse 1 ne résoud pas le problème

# Test hypothèse 2:

En effet en rentrant
const bouquet1 = { taille: "petit", vase: false, carte: false, message: "" };
const bouquet2 = { taille: "grand", vase: false, carte: false, message: "" };
bouquet1.taille === bouquet2.taille;
J'obtiens bien false, j'ai donc essayé
const panier = [];
const options = { taille: "petit" };
panier.push({ options, quantite: 1 });
options.taille = "grand"; // l'utilisateur change la taille
console.log("Taille de l'élément au panier :", panier[0].options.taille);

Affiche "grand". Cela démontre que le problème provient de la référence partagée et non de la fonction de comparaison elle-même.


# Correction hypothèse 2 ticket 4

Voici le correctif proposé pour résoudre le problème directement dans js/panier.js (Hypothèse 2).

Explication du correctif

Dans js/panier.js, la fonction ajouterAuPanier enregistrait directement l'objet options reçu dans le tableau lignes sans le copier. Comme cet objet est conservé par référence, toute modification ultérieure faite dans le formulaire de composition altérait rétroactivement les articles déjà stockés dans le panier.

Pour isoler les articles du panier et s'assurer que la fonction memesOptions puisse distinguer deux compositions différentes, il suffit de cloner l'objet options (à l'aide de { ...options }) lors de l'ajout dans lignes.

De cette manière, chaque article du panier conserve sa propre version figée des options (la première reste en « Petit », et la seconde est créée en « Grand »).

Proposition de modification dans js/panier.js

À la ligne 26 de js/panier.js :

javascript
// Avant :
export function ajouterAuPanier(bouquetId, options) {
  const existante = lignes.find((ligne) => ligne.bouquetId === bouquetId && memesOptions(ligne.options, options));
  if (existante) {
    existante.quantite += 1;
  } else {
    lignes.push({ bouquetId, options, quantite: 1 });
  }
}
javascript
// Après :
export function ajouterAuPanier(bouquetId, options) {
  const existante = lignes.find((ligne) => ligne.bouquetId === bouquetId && memesOptions(ligne.options, options));
  if (existante) {
    existante.quantite += 1;
  } else {
    lignes.push({ bouquetId, options: { ...options }, quantite: 1 });
  }
}

Acceptez-vous que j'applique ce correctif dans js/panier.js ?

# Test correction hypothèse 2 ticket 4
Résultat ok nous pouvons donc mettre dans le panier deux mêmes bouquets de tailles différentes.

# Rédaction cliente

Bonjour Madame,

Merci pour votre retour, nous avons bien régler ce problème,
vous pourrez donc à partir de maintenant mettre deux mêmes bouquets de tailles différentes dans votre panier.
Excusez-nous pour le problème rencontré.

Cordialement,

Bonne journée.