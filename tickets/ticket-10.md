# Test hypothèse 1 ticket 10
En effectuant la demande on obtient le même problème que la cliente.
# 1eme Prompt:
En effet l'hyphothèse 1 ne marche pas peut tu me donner un correctif pour celle ci sans l'appliquer demande moi dabord si je l'accepte ou non

# Correction hypothèse 1 ticket 10

Voici le correctif proposé pour résoudre le problème directement dans js/stockage.js (Hypothèse 1).

Explication du correctif

Dans js/stockage.js, une constante VERSION = 2 est déjà définie, mais la fonction chargerPanier() ne contrôle jamais si les données lues dans le localStorage correspondent bien à cette version. Pire, l'instruction donnees.lignes || donnees accepte et charge directement les anciens formats de panier de la Version 1 (qui étaient stockés sous forme de tableau brut sans la propriété quantite).

Le correctif consiste à vérifier la version dès la lecture :

javascript
if (donnees.version !== VERSION) {
  return { lignes: [], codePromo: "" };
}

Si les données stockées dans le navigateur de la cliente datent de l'ancienne version (version absente ou différente de 2), elles sont considérées comme obsolètes et le site initialise un panier vide et propre. Dès que la cliente ajoutera un nouveau bouquet, son panier sera automatiquement écrasé et réenregistré au format valide de la Version 2.

Proposition de modification dans js/stockage.js

Aux lignes 13 à 20 de js/stockage.js :

javascript
// Avant :
// Relit le panier enregistré, ou un panier vide.
export function chargerPanier() {
  const brut = localStorage.getItem(CLE);
  if (!brut) {
    return { lignes: [], codePromo: "" };
  }
  const donnees = JSON.parse(brut);
  return { lignes: donnees.lignes || donnees, codePromo: donnees.codePromo || "" };
}
javascript
// Après :
// Relit le panier enregistré, ou un panier vide.
export function chargerPanier() {
  const brut = localStorage.getItem(CLE);
  if (!brut) {
    return { lignes: [], codePromo: "" };
  }
  const donnees = JSON.parse(brut);
  if (donnees.version !== VERSION) {
    return { lignes: [], codePromo: "" };
  }
  return { lignes: donnees.lignes || [], codePromo: donnees.codePromo || "" };
}

Acceptez-vous que j'applique ce correctif dans js/stockage.js ?

# Test correction Ticket 9
Résultat ok le problème n'apparaît plus du tout.


# Rédaction cliente

Bonjour Madame,

Votre problème est réglé, vous pouvez désormais commander ce que vous souhaitez.

Cordialement,

Bonne journée.