# Test hypothèse 1 ticket 6
Il y a bien un problème quand on mets le code postal de tours, les frais de livraison se rajoute.

# 1eme Prompt:
En effet l'hyphothèse 1 ne marche pas peut tu me donner un correctif pour celle ci sans l'appliquer demande moi dabord si je l'accepte ou non

# Correction hypothèse 1 ticket 6

Voici le correctif proposé pour résoudre le problème directement dans js/prix.js (Hypothèse 1).

Explication du correctif

Dans js/prix.js, les calculs des prix (prixUnitaire et sousTotal) additionnent des nombres à virgule flottante sans arrondir le résultat au centime. C'est ce qui produit 59.99999999999999 au lieu de 60 lors de l'addition 38.9 + 8.2 + 12.9.

Le module js/outils.js fournit justement une fonction utilitaire arrondir(montant) conçue pour arrondir un montant au centime près (Math.round(montant * 100) / 100).

Le correctif consiste à :

Importer la fonction arrondir depuis ./outils.js au début de js/prix.js.
Utiliser arrondir() sur le résultat de prixUnitaire() et de sousTotal() (ainsi que totalCommande()) afin de garantir des montants précis au centime près.

Ainsi, le sous-total vaudra exactement 60 (et non 59.99999999999999), et la condition montant >= 60 dans livraison.js sera bien respectée pour offrir la livraison.

Proposition de modification dans js/prix.js
javascript
// Avant :
// Calcul des prix : bouquet, options, sous-total et total de la commande.
let catalogue = null;
...
export function prixUnitaire(ligne) {
  const bouquet = trouverBouquet(ligne.bouquetId);
  const options = ligne.options || {};
  const taille = trouverTaille(options.taille);
  let prix = bouquet ? bouquet.prix : 0;
  if (taille) prix += taille.supplement;
  if (options.vase) prix += catalogue.options.vase.prix;
  if (options.carte) prix += catalogue.options.carte.prix;
  return prix;
}
// Prix d'une ligne du panier : prix d'un exemplaire multiplié par la quantité.
export function prixLigne(ligne) {
  return prixUnitaire(ligne) * ligne.quantite;
}
// Sous-total du panier, avant remise et livraison.
export function sousTotal(lignes) {
  return lignes.reduce((total, ligne) => total + prixLigne(ligne), 0);
}
// Total à payer : sous-total, moins la remise, plus la livraison.
export function totalCommande(montant, remise, frais) {
  return montant - remise + (frais || 0);
}
javascript
// Après :
// Calcul des prix : bouquet, options, sous-total et total de la commande.
import { arrondir } from "./outils.js";
let catalogue = null;
...
export function prixUnitaire(ligne) {
  const bouquet = trouverBouquet(ligne.bouquetId);
  const options = ligne.options || {};
  const taille = trouverTaille(options.taille);
  let prix = bouquet ? bouquet.prix : 0;
  if (taille) prix += taille.supplement;
  if (options.vase) prix += catalogue.options.vase.prix;
  if (options.carte) prix += catalogue.options.carte.prix;
  return arrondir(prix);
}
// Prix d'une ligne du panier : prix d'un exemplaire multiplié par la quantité.
export function prixLigne(ligne) {
  return arrondir(prixUnitaire(ligne) * ligne.quantite);
}
// Sous-total du panier, avant remise et livraison.
export function sousTotal(lignes) {
  return arrondir(lignes.reduce((total, ligne) => total + prixLigne(ligne), 0));
}
// Total à payer : sous-total, moins la remise, plus la livraison.
export function totalCommande(montant, remise, frais) {
  return arrondir(montant - remise + (frais || 0));
}

Acceptez-vous que j'applique ce correctif dans js/prix.js ?

# Test correction Ticket 6
Résultat ok la livraison affiche bien offerte.


# Rédaction cliente

Bonjour Madame,

Merci pour votre retour, la commande affiche bien 60 euros donc nous vous avons remboursés la livraison, le montant total réel est de 59.99 euros, nous avons corrigé se problème maintenant 60 euros affiché mettra bien la livraison offerte.

Cordialement,

Bonne journée.