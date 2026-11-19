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

# Prompt 1 Tache 2

Bonjour, quand une cliente veut plusieurs bouquets identiques et clique sur le +, le panier en ajoute
plusieurs d’un coup, et c’est de pire en pire à chaque clic. Une cliente s’est retrouvée avec une quinzaine
de bouquets. Heureusement qu’elle a vu le total avant de commander !
Voila le probleme.
Tum'aides à trouver la cause, je suppose que c'est soit dans prix.js ou panier.js mais je suis pas totalement sur si tu trouves pas dans ces 2 la regarde dans les autres.
1.Propose trois hypothèses au plus, classées.
2.Pour chacune : comment la vérifier dans le navigateur.
3.Aucun correctif à ce stade.

# Premmiere réponse

Voici l'analyse du problème pour ce ticket.

Après vérification du code, l'origine du comportement ne se trouve pas dans prix.js ni panier.js, mais dans affichage.js.

1. Trois hypothèses classées
Hypothèse 1 (La véritable cause — dans js/affichage.js) : Empilement exponentiel des écouteurs d'événements click (addEventListener)
Explication : Dans affichage.js, à la fin de la fonction afficherPanier (ligne 69), un écouteur liste.addEventListener("click", ...) est attaché à l'élément #panier-liste.
Or, #panier-liste est un conteneur statique de la page qui n'est jamais détruit (seuls ses enfants sont vidés). À chaque appel de rafraichir(), afficherPanier() attache un nouvel écouteur sans enlever le précédent.
Dès qu'on clique sur +, tous les écouteurs enregistrés se déclenchent les uns après les autres. Chacun incrémente la quantité et appelle rafraichir(), ce qui ajoute encore de nouveaux écouteurs en pleine réaction en chaîne. L'incrémentation s'emballe de façon exponentielle (1, 3, 7, 15 bouquets…), ce qui correspond exactement au symptôme « de pire en pire à chaque clic ».
Hypothèse 2 (dans js/panier.js) : Dysfonctionnement dans la logique de modification de quantité (changerQuantite)
Explication : On pourrait supposer que la fonction changerQuantite applique un facteur multiplicatif, possède un effet de bord ou modifie plusieurs fois la même ligne (par exemple une référence partagée ou un problème de boucle). En examinant panier.js, la ligne ligne.quantite += ecart; est pourtant saine (elle ajoute bien +1 par exécution).
Hypothèse 3 (dans js/prix.js) : Dérive cumulative des calculs de prix / sous-total
Explication : On pourrait supposer que le calcul du prix dans prixLigne ou sousTotal multiplie le montant ou réinjecte une valeur erronée dans l'affichage, donnant l'illusion d'une quantité qui explose.
2. Comment vérifier chaque hypothèse dans le navigateur
Pour vérifier l'Hypothèse 1 (Écouteurs multiples dans le navigateur)
Ouvrez le site dans Chrome, Edge ou Firefox et ouvrez les outils de développement (F12).
Ajoutez un bouquet au panier.
Allez dans l'onglet Éléments (Elements) et sélectionnez la balise <ul id="panier-liste">.
Dans le volet latéral droit, cliquez sur l'onglet Écouteurs d'événements (Event Listeners), puis dépliez l'événement click.
Cliquez une première fois sur le bouton + dans la boutique, puis réexaminez ce volet : vous verrez le nombre de fonctions attachées à l'événement click se multiplier (2, puis 4, puis 8, etc.).
Alternative via l'onglet Console : posez un point d'arrêt (breakpoint) dans l'onglet Sources sur la ligne 72 de js/affichage.js (surAction(...)). Vous constaterez qu'un seul clic physique sur le bouton + s'arrête de nombreuses fois d'affilée sur ce point d'arrêt.
Pour vérifier l'Hypothèse 2 (Vérification de panier.js)
Dans l'onglet Sources (F12), ouvrez js/panier.js et placez un point d'arrêt sur la ligne 34 : ligne.quantite += ecart;.
Cliquez une fois sur +.
Observez la valeur de ecart dans la pile des variables : elle vaut bien 1.
En cliquant sur Reprendre (F8 / Play), constatez que le code repasse immédiatement dans cette fonction sans que vous n'ayez recliqué, prouvant que panier.js n'est pas le responsable de l'appel multiple, mais seulement la victime des événements en chaîne.
Pour vérifier l'Hypothèse 3 (Vérification de prix.js vs Quantité réelle)
Observez directement le texte affiché entre le bouton − et le bouton + dans le panier (le <span> de la quantité).
En cliquant sur +, le chiffre affiché passe réellement de 1 à 3, puis 7, etc. Le problème affecte donc bien la variable quantite elle-même et non une anomalie de calcul mathématique dans prix.js.

(Aucun correctif n'a été appliqué conformément à votre demande).

# Prompt 1 Tache 3:
Bonjour, quand on choisit l’occasion « Merci », le bouquet « Le petit mot » n’apparaît pas. C’est pourtant
celui que je vends le plus pour dire merci ! Il est bien là quand on affiche tous les bouquets.
Voila le probleme.
Tum'aides à trouver la cause, je suppose que c'est soit dans composition.js ou dans affichage.js, je ne suis pas sur si tu trouves pas dans ces 2 la cherche dans les autres.
1.Propose trois hypothèses au plus, classées.
2.Pour chacune : comment la vérifier dans le navigateur.
3.Aucun correctif à ce stade.

# Premiere réponse Prompt 1:

Voici l'analyse du problème pour ce ticket, en tenant compte de votre piste sur composition.js et affichage.js.

1. Trois hypothèses classées
Hypothèse 1 (La véritable cause — dans data/catalogue.json) : Erreur de casse (majuscule) sur l'occasion du bouquet « Le petit mot »
Explication : Dans data/catalogue.json, le bouquet « Le petit mot » possède la configuration suivante :
json
"occasions": ["Merci", "plaisir"]
L'occasion y est inscrite avec un M majuscule ("Merci"), alors que le filtre transmet l'identifiant technique en minuscules ("merci"). Comme la méthode .includes() est sensible à la casse, "Merci" n'est pas reconnu comme "merci".
En revanche, lorsque le filtre est sur « Toutes », la condition occasion === "toutes" est vraie pour tous les bouquets sans tester le tableau occasions, ce qui explique pourquoi le bouquet est bien présent dans la liste globale.
Hypothèse 2 (dans js/affichage.js) : Filtrage strict sans tolérance à la casse
Explication : À la ligne 11 de js/affichage.js, la fonction afficherCatalogue filtre avec :
javascript
bouquet.occasions.includes(occasion)
Le filtre ne normalise pas les chaînes (par exemple via .toLowerCase()) avant comparaison. Il dépend donc à 100 % d'une correspondance exacte de casse entre les identifiants de filtres et les données JSON.
Hypothèse 3 (dans js/composition.js) : Fausse piste — composition.js n'intervient pas dans le filtrage
Explication : On pourrait penser que composition.js filtre ou masque des bouquets. En réalité, ce fichier ne s'occupe que du panneau d'options d'un bouquet déjà sélectionné (choix de la taille, ajout du vase/carte et ajout au panier) ; il ne participe ni à l'affichage ni au filtrage de la liste des bouquets.
2. Comment vérifier chaque hypothèse dans le navigateur
Pour vérifier l'Hypothèse 1 (Inspection des données JSON)
Ouvrez le site dans le navigateur et ouvrez la console des outils de développement (F12).
Collez la commande suivante dans la console :
javascript
fetch("data/catalogue.json")
  .then(res => res.json())
  .then(data => {
    const bouquet = data.bouquets.find(b => b.id === "petit-mot");
    console.log("Occasions de 'Le petit mot' :", bouquet.occasions);
  });
Résultat : La console affiche ["Merci", "plaisir"]. On constate directement la majuscule sur "Merci", contrairement aux autres bouquets qui ont "merci".
Pour vérifier l'Hypothèse 2 (Comportement du filtre dans la console)
Dans la console (F12), reproduisez l'instruction exacte exécutée par affichage.js :
javascript
["Merci", "plaisir"].includes("merci")
Résultat : La console renvoie false.
Testez ensuite avec une conversion en minuscules :
javascript
["Merci", "plaisir"].some(occ => occ.toLowerCase() === "merci")
Résultat : La console renvoie true.
Pour vérifier l'Hypothèse 3 (Rôle de composition.js)
Dans l'onglet Sources des DevTools (F12), ouvrez le fichier js/composition.js.
Faites une recherche de texte (Ctrl+F) sur le mot occasion ou filter.
Résultat : Aucun résultat trouvé. Cela confirme que ce fichier ne gère aucun filtrage de la liste du catalogue.

(Aucun correctif n'a été appliqué à ce stade).

# Deuxieme prompt Tache 3

fetch("data/catalogue.json")
  .then(res => res.json())
  .then(data => {
    const bouquet = data.bouquets.find(b => b.id === "petit-mot");
    console.log("Occasions de 'Le petit mot' :", bouquet.occasions);
  });
donne en moi un autre qui apparait dans merci pour voir la différence

# Deuxieme réponse 

Voici le code à coller dans la console pour comparer directement « Le petit mot » avec le bouquet « Champêtre » (qui, lui, s'affiche correctement quand on sélectionne « Merci ») :

javascript
fetch("data/catalogue.json")
  .then(res => res.json())
  .then(data => {
    const champetre = data.bouquets.find(b => b.id === "champetre");
    const petitMot = data.bouquets.find(b => b.id === "petit-mot");
    console.log("Champêtre :", champetre.occasions);
    console.log("Le petit mot :", petitMot.occasions);
  });
Ce que la console affichera :
Champêtre : ["plaisir", "merci"] (avec un m minuscule, le filtre fonctionne)
Le petit mot : ["Merci", "plaisir"] (avec un M majuscule, le filtre échoue)
