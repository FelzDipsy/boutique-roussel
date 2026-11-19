# Test hypothèse 1 ticket 8
Il y a bien un problème, on peut sélectionné mation ou après midi les 2 valdations de commandes affichent matin.

# 1eme Prompt:
En effet l'hyphothèse 1 ne marche pas peut tu me donner un correctif pour celle ci sans l'appliquer demande moi dabord si je l'accepte ou non

# Correction hypothèse 1 ticket 8

Voici le correctif proposé pour résoudre le problème directement dans js/app.js (Hypothèse 1).

Explication du correctif

Dans js/app.js, la fonction commander() récupère le bouton radio du moment de livraison avec document.querySelector("input[name=moment]"). Sans la pseudo-classe :checked, cette méthode sélectionne toujours le premier élément <input> rencontré dans le DOM (qui correspond par défaut au choix « Matin »), quel que soit le bouton réellement coché par la cliente.

Le correctif consiste à ajouter le sélecteur :checked : document.querySelector("input[name=moment]:checked")

Ainsi, le script ciblera bien l'option cochée par l'utilisatrice (« Matin » ou « Après-midi ») lors de la confirmation de la commande.

Proposition de modification dans js/app.js

À la ligne 116 de js/app.js :

javascript
// Avant :
function commander() {
  const message = document.querySelector("#message-commande");
  const jour = document.querySelector("#jour-livraison");
  const moment = document.querySelector("input[name=moment]");
  if (lignesPanier().length === 0) {
javascript
// Après :
function commander() {
  const message = document.querySelector("#message-commande");
  const jour = document.querySelector("#jour-livraison");
  const moment = document.querySelector("input[name=moment]:checked");
  if (lignesPanier().length === 0) {

Acceptez-vous que j'applique ce correctif dans js/app.js ?

# Test correction Ticket 8
Résultat ok le message de validation de commande affiche bien après-midi.


# Rédaction cliente

Bonjour Madame,

Toutes nos excuses pour le déplacement, nous avons corrigés le problème.
Merci beaucoup pour votre commande, en cas de prochaine commande il n'y aura plus aucun problème.

Cordialement,

Bonne journée.