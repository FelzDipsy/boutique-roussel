# Test hypothèse 1 tache 2
En effet le nombre de fonctions attachées à l'événement click se multiplie bien, le problème vient de ça

# 2eme Prompt:
En effet l'hyphothèse 1 ne marche pas peut tu me donner un correctif pour celle ci sans l'appliquer demande moi dabord si je l'accepte ou non

# Correction hypothèse 1 tache 1

Voici le correctif proposé pour résoudre le problème de l'accumulation des clics (Hypothèse 1).

Explication du correctif

Dans affichage.js, la fonction afficherPanier est appelée à chaque mise à jour du panier (au démarrage, à l'ajout d'un bouquet, et à chaque clic sur + ou −).

Comme la ligne liste.addEventListener("click", ...) est placée à l'intérieur de afficherPanier, un nouvel écouteur d'événement est greffé sur #panier-liste à chaque rafraîchissement.

Pour corriger cela tout en conservant le fonctionnement actuel de l'application, il suffit de s'assurer que l'écouteur n'est attaché qu'une seule fois (à l'aide d'un simple drapeau / booléen), tout en mettant à jour la fonction de rappel (callback).

Proposition de modification dans js/affichage.js

Au début de affichage.js et dans afficherPanier :

javascript
// Variables pour ne brancher l'écouteur du panier qu'une seule fois
let actionPanierActuelle = null;
let ecouteurPanierInstalle = false;
export function afficherPanier(lignes, surAction) {
  actionPanierActuelle = surAction;
  const liste = document.querySelector("#panier-liste");
  liste.replaceChildren();
  document.querySelector("#panier-vide").hidden = lignes.length > 0;
  lignes.forEach((ligne, index) => {
    // ... (création des éléments inchangée) ...
  });
  // Les boutons + , − et Supprimer de toutes les lignes (attaché une seule fois).
  if (!ecouteurPanierInstalle) {
    liste.addEventListener("click", (evenement) => {
      const cible = evenement.target.closest("button[data-action]");
      if (!cible || !actionPanierActuelle) return;
      actionPanierActuelle(cible.dataset.action, Number(cible.dataset.index));
    });
    ecouteurPanierInstalle = true;
  }
}

Acceptez-vous que j'applique ce correctif dans affichage.js ?

# Test correction Tache 1 Prompt 2
Résultat ok nous pouvons donc ajouter 1 par 1 le même bouquet.
J'ai vérifié le prompt 1 aucun soucis rencontré.


# Rédaction cliente

Bonjour Madame,

Merci pour votre retour, nous avons bien régler ce problème,
vous pouvez donc maintenant acheter plusieurs fois le même bouquet sans problème.
Toutes nos excuses pour le problème rencontré.

Cordialement,

Bonne journée.