# Test hypothèse 1 ticket 7
Il y a bien un problème quand on écrit le message s'arrete à 150 caractères.

# 1eme Prompt:
En effet l'hyphothèse 1 ne marche pas peut tu me donner un correctif pour celle ci sans l'appliquer demande moi dabord si je l'accepte ou non

# Correction hypothèse 1 ticket 7

Voici le correctif proposé pour lever la limite de caractères sur la carte, directement dans js/composition.js (Hypothèse 1).

Explication du correctif

Dans js/composition.js, la restriction de longueur intervient à deux endroits :

À la ligne 36 : l'instruction document.querySelector("#message-carte").maxLength = reglesOptions.carte.longueurMax; assigne la limite (150 caractères) à la propriété maxLength du <textarea>. C'est ce qui force le navigateur à bloquer la saisie au clavier dès que le 150ᵉ caractère est atteint.
À la ligne 75 : l'instruction options.message = options.carte ? message.slice(0, reglesOptions.carte.longueurMax) : ""; tronque le message à 150 caractères lors de la lecture du formulaire.

Pour permettre aux clientes d'écrire un message sans limitation de longueur, le correctif consiste à :

Supprimer l'affectation de maxLength (ligne 36).
Enregistrer le message complet dans options.message sans le tronquer avec .slice(...) (ligne 75).
Proposition de modification dans js/composition.js
1. À la ligne 36 de js/composition.js :
javascript
// Avant :
  document.querySelector("#libelle-carte").textContent =
    reglesOptions.carte.libelle + " (+ " + formaterPrix(reglesOptions.carte.prix) + ")";
  document.querySelector("#message-carte").maxLength = reglesOptions.carte.longueurMax;
  document.querySelector("#form-composition").addEventListener("change", lireFormulaire);
javascript
// Après :
  document.querySelector("#libelle-carte").textContent =
    reglesOptions.carte.libelle + " (+ " + formaterPrix(reglesOptions.carte.prix) + ")";
  document.querySelector("#form-composition").addEventListener("change", lireFormulaire);
2. À la ligne 75 de js/composition.js :
javascript
// Avant :
  const message = document.querySelector("#message-carte").value;
  options.message = options.carte ? message.slice(0, reglesOptions.carte.longueurMax) : "";
  document.querySelector("#zone-message").hidden = !options.carte;
javascript
// Après :
  const message = document.querySelector("#message-carte").value;
  options.message = options.carte ? message : "";
  document.querySelector("#zone-message").hidden = !options.carte;

Acceptez-vous que j'applique ce correctif dans js/composition.js ?

# Test correction Ticket 7
Résultat ok le message n'a plus de limite.


# Rédaction cliente

Bonjour Madame,

Merci pour votre retour, nous avons modifié la limite de texte.
Vous pouvez désormais écrire le message souhaité.

Cordialement,

Bonne journée.