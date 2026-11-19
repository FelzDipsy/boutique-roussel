# Test hypothèse 1 tache 1
Saisir "NOEL10 " écrit en effet ce code promo n'existe pas

# 2eme Prompt:
En effet l'hyphothèse 1 ne marche pas peut tu me donner un correctif pour celle ci sans l'appliquer demande moi dabord si je l'accepte ou non

# Correction hypothèse 1 tache 1

Voici le correctif proposé pour résoudre le problème de l'espace parasite (Hypothèse 1).

Explication du correctif

Le problème vient de la comparaison directe de saisie avec le code en base. En utilisant la méthode standard JavaScript .trim(), on supprime automatiquement tous les espaces superflus situés au début et à la fin de la chaîne saisie par l'utilisatrice.

Proposition de modification dans promo.js

À la ligne 14 de js/promo.js :

javascript
// Avant :
export function appliquerPromo(saisie, montant) {
  const regle = promos.find((promo) => promo.code === saisie);
  ...
javascript
// Après :
export function appliquerPromo(saisie, montant) {
  const codeNettoye = saisie.trim();
  const regle = promos.find((promo) => promo.code === codeNettoye);
  ...

(Optionnel mais recommandé : on peut aussi ajouter .toUpperCase() pour régler en même temps le problème de minuscules/majuscules : saisie.trim().toUpperCase()).

Souhaitez-vous que j'applique cette modification dans promo.js (uniquement le .trim(), ou avec .toUpperCase()) ?

# Test correction Tache 1
Résultat ok nous pouvons donc écrire "NOEL10 "

# Rédaction cliente

Bonjour Madame,
Excusez-nous pour le soucis que vous avez rencontrez, nous avons bien régler ce problème.
Pour les prochaines commandes vous pourrez donc l'utiliser sans problème de la même façon dont vous avez procédé.
En compensation, nous vous donnons un avoir équivalement à la remise égale à la réduction de ce code sur votre prochaine commande.
Merci pour votre ticket qui nous a permis de régler ce problème et toutes nos excuses.

Cordialement,

Bonne journée.