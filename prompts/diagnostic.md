## Fichiers des prompts

# Prompt 1

Bonjour, une cliente m’a appelée hier. Elle a voulu utiliser le code NOEL10 de ma lettre d’information
depuis son téléphone, et le site lui a répondu que ce code n’existe pas. Sur mon ordinateur, le code
marche très bien. Elle a fini par commander sans la réduction et elle n’était pas contente. Pouvez-vous
regarder ?
Voila le probleme.
Tum'aides à trouver la cause, je suppose que c'est soit dans promo.js ou promotions.js
1.Propose trois hypothèses au plus, classées.
2.Pour chacune : comment la vérifier dans le navigateur.
3.Aucun correctif à ce stade.

# Premiere réponse

Voici l'analyse du problème d'après l'examen de vos fichiers (promo.js, promotions.js et app.js).

Le message d'erreur retourné (« Ce code promo n'existe pas ») correspond à la ligne 17 de promo.js :

javascript
const regle = promos.find((promo) => promo.code === saisie);
if (!regle) {
  return { valide: false, remise: 0, message: "Ce code promo n'existe pas." };
}

Actuellement, app.js utilise promo.js, qui effectue une égalité stricte (===) sans aucun nettoyage préalable de la saisie.

1. Hypothèses classées par ordre de probabilité
Hypothèse 1 (la plus probable) : Ajout d'un espace parasite en fin de mot ("NOEL10 ")
Cause : Sur smartphone, le clavier virtuel (saisie prédictive / autocomplétion) ou la sélection par copier-coller depuis un e-mail ajoute très souvent un espace à la fin du mot. En l'absence de nettoyage (trim()), "NOEL10 " est comparé à "NOEL10", ce qui échoue.
Hypothèse 2 : Saisie en minuscules ou casse mixte ("noel10" ou "Noel10")
Cause : Sur mobile, les utilisateurs saisissent fréquemment les codes sans enclencher le verrouillage majuscule (ou le clavier n'applique que la majuscule initiale). En l'absence de conversion en majuscules (toUpperCase()), la comparaison stricte échoue.
Hypothèse 3 : Copie de caractères invisibles ou d'un saut de ligne depuis l'e-mail mobile
Cause : Lors d'un appui long / sélection sur smartphone dans l'application de messagerie, un caractère invisible (espace insécable, retour chariot \n, ponctuation collée) a pu être copié avec le code promo.
2. Comment vérifier chaque hypothèse dans le navigateur
Pour vérifier l'Hypothèse 1 (espace parasite)
Ouvrez le site dans votre navigateur et ajoutez un bouquet au panier.
Dans le champ « Code promo », saisissez le code suivi d'une espace : "NOEL10 ".
Cliquez sur Appliquer.
Résultat attendu pour valider l'hypothèse : Le message d'erreur « Ce code promo n'existe pas. » s'affiche immédiatement.
Pour vérifier l'Hypothèse 2 (casse / minuscules)
Toujours avec un article dans le panier, saisissez le code entièrement en minuscules ("noel10") ou avec seulement l'initiale majuscule ("Noel10").
Cliquez sur Appliquer.
Résultat attendu pour valider l'hypothèse : Le message d'erreur « Ce code promo n'existe pas. » s'affiche.
Pour vérifier l'Hypothèse 3 (diagnostic technique via les Outils de développement)
Ouvrez les DevTools du navigateur (F12 ou clic droit > Inspecter), puis basculez sur l'onglet Console ou Sources.
Dans l'onglet Sources, ouvrez js/promo.js et placez un point d'arrêt (breakpoint) sur la ligne 15 :
javascript
const regle = promos.find((promo) => promo.code === saisie);
Reproduisez la saisie depuis un mobile (ou en mode émulation mobile du navigateur avec Ctrl+Maj+M) en copiant-collant le code.
Dans le panneau des variables lors de l'arrêt, observez la valeur exacte de la variable saisie (vous pourrez visualiser la présence d'espaces, de minuscules ou de caractères d'échappement invisibles).

