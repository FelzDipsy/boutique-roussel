# Test hypothèse 1 ticket 5
Il y a bien un problème quand on supprime un des bouquets tous ceux sélectionnés après sont supprimés au passage.

# 1eme Prompt:
En effet l'hyphothèse 1 ne marche pas peut tu me donner un correctif pour celle ci sans l'appliquer demande moi dabord si je l'accepte ou non

# Correction hypothèse 1 ticket 5

Voici le correctif proposé pour résoudre le problème directement dans js/panier.js (Hypothèse 1).

Explication du correctif

Dans js/panier.js, la fonction supprimerLigne appelle la méthode lignes.splice(index) sans préciser le second paramètre (deleteCount). En JavaScript, omission de ce paramètre entraîne la suppression de tous les éléments à partir de index jusqu'à la fin du tableau.

Pour ne retirer que la seule ligne sélectionnée par la cliente, il faut spécifier 1 en second argument : lignes.splice(index, 1). Cela corrigera également la suppression automatique lorsque la quantité d'un article tombe à 0 via le bouton « − ».

Proposition de modification dans js/panier.js

À la ligne 42 de js/panier.js :

javascript
// Avant :
export function supprimerLigne(index) {
  lignes.splice(index);
}
javascript
// Après :
export function supprimerLigne(index) {
  lignes.splice(index, 1);
}

Acceptez-vous que j'applique ce correctif dans js/panier.js ?

# Test correction Ticket 5
Résultat ok nous pouvons donc supprimer n'importe qu'elle élément du panier sans supprimer les autres.


# Rédaction cliente

Bonjour Madame,

Merci pour votre retour, le site n'était pas en panne il y avait bien un problème lors de suppresion d'un bouquet. Le problème est réglé vous pourrez donc maintenant supprimer le bouquet souhaité san supprimer les autres. Toutes nos excuses pour ce problème rencontré.

Cordialement,

Bonne journée.