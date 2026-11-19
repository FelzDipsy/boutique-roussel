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


# Prompt 1 Tache 4:

Bonjour, une cliente voulait offrir deux Pivoine poudrée : une petite pour sa sœur et une grande pour
sa mère. Elle a composé la petite et l’a ajoutée au panier, puis elle a choisi « Grand » et a cliqué à
nouveau sur « Ajouter au panier ». Dans son panier : deux grandes ! Elle n’a pas compris, et moi non
plus.
Voila le probleme.
Tum'aides à trouver la cause, je suppose que c'est soit dans affichage.js ou panier.js mais je ne suis pas sur si tu trouves rien regarde dans les autres.
1.Propose trois hypothèses au plus, classées.
2.Pour chacune : comment la vérifier dans le navigateur.
3.Aucun correctif à ce stade.

# Reponse prompt:

Voici l'analyse détaillée du problème rencontré par la cliente.

Après examen des fichiers du projet, l'origine du problème se situe dans js/composition.js (avec un impact direct sur la logique de js/panier.js).

1. Trois hypothèses classées
Hypothèse 1 (La véritable cause — dans js/composition.js) : Mutation par référence d'un objet unique options partagé
Explication : Dans js/composition.js (ligne 7), il n'existe qu'une seule et unique variable globale pour les options :
javascript
const options = { taille: "petit", vase: false, carte: false, message: "" };
Lors du premier ajout (« Petit »), c'est une référence directe vers cet objet options qui est transmise à ajouterAuPanier et stockée dans la première ligne du panier.
Quand la cliente coche ensuite « Grand », la fonction lireFormulaire() s'exécute et modifie directement l'objet en mémoire : options.taille = "grand". Comme la ligne déjà présente dans le panier pointe vers cet exact même objet, le premier bouquet au panier est immédiatement transformé en « Grand » en mémoire.
Quand elle clique sur « Ajouter au panier », ajouterAuPanier (js/panier.js) compare les options de la ligne existante avec les nouvelles via memesOptions. Comme les deux pointent vers le même objet (qui vaut désormais « Grand »), la condition est vraie : au lieu de créer une deuxième ligne, le panier incrémente la quantité de la première (quantite += 1). Résultat : une seule ligne de 2 grands bouquets.
Hypothèse 2 (dans js/panier.js) : Problème de discrimination des lignes dans memesOptions / ajouterAuPanier
Explication : On pourrait supposer que la logique de regroupement dans panier.js n'est pas capable de différencier deux tailles différentes d'un même bouquet (par exemple si elle ignorait la propriété taille ou ne comparait que l'identifiant bouquetId). Cependant, l'examen de panier.js montre que memesOptions teste explicitement a.taille === b.taille. La comparaison est correcte sur le papier, mais elle est faussée par le fait que a et b désignent la même case mémoire.
Hypothèse 3 (dans js/affichage.js) : Bogue d'affichage ou fusion visuelle dans afficherPanier
Explication : On pourrait supposer que le panier contient bien deux lignes distinctes en mémoire (une petite et une grande), mais que la fonction afficherPanier ou decrireOptions afficherait par erreur les caractéristiques du formulaire actuel ou masquerait la première ligne. Néanmoins, le panier affiche le chiffre 2 pour la quantité et un sous-total doublé, ce qui indique que l'anomalie réside bien dans les données du panier et non dans le gabarit d'affichage HTML.
2. Comment vérifier chaque hypothèse dans le navigateur
Pour vérifier l'Hypothèse 1 (Mise en évidence de la référence partagée)
Ouvrez le site dans votre navigateur et ouvrez la console des outils de développement (F12).
Cliquez sur « Composer » sur le bouquet Pivoine poudrée. Laissez la taille sur « Petit » et cliquez sur « Ajouter au panier ».
Dans le panier, la ligne affiche bien un bouquet avec l'option « Petit ».
Sans recharger la page, dans le formulaire de composition toujours affiché, cochez simplement « Grand », mais SANS cliquer sur « Ajouter au panier ».
Ouvrez la console (F12) et collez :
javascript
JSON.parse(localStorage.getItem("panier-roussel")).lignes[0].options.taille
(Ou observez l'affichage après avoir provoqué un rendu, par exemple en saisissant un chiffre dans le code postal).
Résultat attendu pour valider l'hypothèse : Vous constaterez que l'article déjà présent dans le panier a été altéré et est devenu "grand" avant même d'avoir cliqué sur le bouton d'ajout.
Pour vérifier l'Hypothèse 2 (Vérification de la fonction de comparaison)
Ouvrez la console (F12).
Simulez deux objets distincts pour vérifier si la logique de memesOptions sait différencier deux tailles :
javascript
const bouquet1 = { taille: "petit", vase: false, carte: false, message: "" };
const bouquet2 = { taille: "grand", vase: false, carte: false, message: "" };
bouquet1.taille === bouquet2.taille;
Résultat : La console renvoie false. Cela confirme que deux objets indépendants ne seraient pas fusionnés par memesOptions.
Testez maintenant avec une référence partagée :
javascript
const panier = [];
const options = { taille: "petit" };
panier.push({ options, quantite: 1 });
options.taille = "grand"; // l'utilisateur change la taille
console.log("Taille de l'élément au panier :", panier[0].options.taille);
Résultat : Affiche "grand". Cela démontre que le problème provient de la référence partagée et non de la fonction de comparaison elle-même.
Pour vérifier l'Hypothèse 3 (Données réelles vs affichage)
Reproduisez le parcours de la cliente : composez un premier bouquet « Petit », cliquez sur « Ajouter au panier », cochez « Grand », puis cliquez à nouveau sur « Ajouter au panier ».
Dans la console (F12), inspectez les données brutes enregistrées :
javascript
JSON.parse(localStorage.getItem("panier-roussel")).lignes
Résultat : La console affiche un tableau ne contenant qu'un seul objet avec quantite: 2 et options: { taille: "grand", ... }. Cela confirme que les données elles-mêmes ont été fusionnées en amont, écartant l'hypothèse d'une simple erreur d'affichage.

(Conformément à vos consignes, aucun correctif n'a été appliqué à ce stade.)

# Prompt 1 ticket 5

Bonjour, une cliente avait mis trois bouquets différents dans son panier. Elle a voulu retirer le premier
avec le bouton « Supprimer » : les trois ont disparu. Elle a cru à une panne et elle est partie sans
commander.
Voila le probleme.
Tu m'aides à trouver la cause, je suppose que c'est soit dans affichage.js ou panier.js j'ai un doute si tu ne trouves pas regarde dans les autres.
1.Propose trois hypothèses au plus, classées.
2.Pour chacune : comment la vérifier dans le navigateur.
3.Aucun correctif à ce stade.

# Reponse prompt

Voici l'analyse du problème d'après l'examen de vos fichiers (js/panier.js, js/affichage.js et js/app.js).

1. Trois hypothèses classées
Hypothèse 1 (La véritable cause — dans js/panier.js) : Omission du paramètre deleteCount dans lignes.splice(index)
Explication : À la ligne 42 de js/panier.js, la fonction de suppression est écrite ainsi :
javascript
export function supprimerLigne(index) {
  lignes.splice(index);
}
En JavaScript, la méthode Array.prototype.splice(start, deleteCount) prend deux arguments principaux. Si le second argument (deleteCount) est omis, splice supprime tous les éléments à partir de start jusqu'à la fin du tableau. Lorsque la cliente clique sur le bouton « Supprimer » du premier bouquet, index vaut 0. L'appel lignes.splice(0) supprime donc l'élément à l'index 0 ainsi que tous les suivants : l'ensemble des 3 bouquets est immédiatement effacé du panier.
Hypothèse 2 (dans js/affichage.js / js/app.js) : Transmission d'une action erronée ou appel involontaire à viderPanier
Explication : On pourrait supposer que le bouton « Supprimer » déclenche par erreur l'action de vidage complet (viderPanier(), normalement réservé à la confirmation de commande dans app.js), ou que l'attribut data-action ou data-index extrait dans affichage.js n'est pas transmis correctement au gestionnaire d'événements.
Hypothèse 3 (dans js/affichage.js) : Bogue d'affichage masquant visuellement les éléments restants
Explication : On pourrait envisager que le tableau lignes contienne toujours les deux bouquets restants en mémoire, mais qu'une anomalie dans afficherPanier (par exemple la gestion de l'attribut hidden sur #panier-vide ou une mauvaise réinitialisation du conteneur #panier-liste) donne l'illusion visuelle que tout le panier a disparu.
2. Comment vérifier chaque hypothèse dans le navigateur
Pour vérifier l'Hypothèse 1 (Comportement de splice et test en conditions réelles)
Démonstration dans l'interface :
Ajoutez 3 bouquets différents au panier.
Au lieu de supprimer le premier, cliquez sur le bouton « Supprimer » du dernier bouquet (le 3ᵉ) : seul celui-ci disparaît (car splice(2) ne supprime qu'à partir de l'index 2, où il n'y a qu'un élément).
Remettez 3 bouquets, puis cliquez sur « Supprimer » sur le deuxième : le 2ᵉ et le 3ᵉ disparaissent, ne laissant que le premier. Ce comportement prouve que splice(index) supprime tout depuis l'index sélectionné jusqu'au bout.
Vérification dans la console (F12) :
Ouvrez la console et testez directement l'instruction :
javascript
const panierTest = ["Bouquet 1", "Bouquet 2", "Bouquet 3"];
panierTest.splice(0);
console.log(panierTest); // Affiche [] : le panier est entièrement vidé
Comparez avec un splice(0, 1) :
javascript
const panierTest2 = ["Bouquet 1", "Bouquet 2", "Bouquet 3"];
panierTest2.splice(0, 1);
console.log(panierTest2); // Affiche ["Bouquet 2", "Bouquet 3"]
Pour vérifier l'Hypothèse 2 (Vérification des paramètres envoyés par l'événement)
Ouvrez les Outils de développement (F12), onglet Sources.
Ouvrez le fichier js/affichage.js et placez un point d'arrêt (breakpoint) sur la ligne 78 :
javascript
actionPanierActuelle(cible.dataset.action, Number(cible.dataset.index));
Cliquez sur « Supprimer » sur le premier bouquet.
Lors de l'arrêt du script, inspectez les valeurs :
cible.dataset.action vaut bien "supprimer".
cible.dataset.index vaut bien "0".
Cela confirme que l'interface transmet la bonne action et le bon index, et qu'il n'y a pas d'appel parasite à viderPanier.
Pour vérifier l'Hypothèse 3 (Données en mémoire vs affichage visuel)
Mettez 3 bouquets dans le panier.
Cliquez sur « Supprimer » sur le premier bouquet.
Dans la console (F12), inspectez directement le contenu du stockage local :
javascript
JSON.parse(localStorage.getItem("panier-roussel")).lignes
Résultat : La console renvoie un tableau vide []. Cela prouve que les bouquets ont été supprimés des données elles-mêmes, écartant l'hypothèse d'un simple problème d'affichage HTML.

(Conformément à votre demande, aucun correctif n'a été appliqué à ce stade.)

# Prompt 1 ticket 6

Bonjour, sur le site il est écrit que la livraison est offerte dès 60 € d’achat. Une cliente de Tours a
commandé un Blanc pur en taille moyenne, avec le vase. Son sous-total affichait bien 60,00 €, mais on
lui a compté 5,90 € de livraison. Je l’ai remboursée, mais cela ne doit pas se reproduire.
Voila le probleme.
Tu m'aides à trouver la cause, je suppose que c'est soit dans prix.js, promo.js ou promotions.js j'ai un doute si tu ne trouves pas regarde dans les autres.
1.Propose trois hypothèses au plus, classées.
2.Pour chacune : comment la vérifier dans le navigateur.
3.Aucun correctif à ce stade.

# Reponse prompt

Voici l'analyse du problème d'après l'examen de vos fichiers (js/prix.js, js/livraison.js, data/catalogue.json et data/livraison.json).

L'origine du problème se situe bien dans js/prix.js.

1. Trois hypothèses classées
Hypothèse 1 (La véritable cause — dans js/prix.js) : Imprécision de calcul en virgule flottante (IEEE 754) non arrondie

Explication : D'après data/catalogue.json, le détail du prix pour cette commande est :

Bouquet « Blanc pur » : 38.9 €
Supplément taille « Moyen » : 8.2 €
Option « Vase en verre » : 12.9 €

En JavaScript (comme dans la plupart des langages suivant la norme IEEE 754), les additions de nombres décimaux à virgule flottante ne sont pas exactes : 38.9 + 8.2 + 12.9 donne 59.99999999999999 et non 60.0.

Dans js/prix.js, les fonctions prixUnitaire et sousTotal calculent les sommes brutes sans utiliser la fonction d'arrondi au centime arrondir() (pourtant disponible dans js/outils.js).

À l'affichage : la fonction formaterPrix() utilise toLocaleString(), qui arrondit automatiquement à deux décimales, affichant ainsi innocemment 60,00 €.
Pour la livraison : dans js/livraison.js, le test vérifie si montant >= regles.seuilGratuite (60). Or, 59.99999999999999 >= 60 est faux (false). Le site considère donc que le seuil de 60 € n'est pas atteint et facture les frais de livraison de 5,90 €.
Hypothèse 2 (dans data/livraison.json ou js/livraison.js) : Erreur sur la valeur du seuil ou opérateur d'inégalité strict (>)
Explication : On pourrait penser que le seuil de gratuité dans data/livraison.json a été fixé au-delà de 60 € (par exemple 60.01 ou 65), ou que livraison.js utilisait une inégalité stricte (montant > seuilGratuite au lieu de >=). Cependant, data/livraison.json indique bien "seuilGratuite": 60 et livraison.js utilise bien >=.
Hypothèse 3 (dans js/promo.js ou js/promotions.js) : Déduction cachée ou interférence d'une réduction fantôme
Explication : On pourrait envisager qu'une règle de code promotionnel réduise silencieusement le montant total éligible sous les 60 € avant le calcul des frais de livraison. Cependant, en l'absence de code saisi par la cliente, la remise est à 0 et promo.js ne modifie en rien le sous-total transmis à fraisLivraison.
2. Comment vérifier chaque hypothèse dans le navigateur
Pour vérifier l'Hypothèse 1 (Imprécision virgule flottante dans la console)
Ouvrez votre navigateur et les outils de développement (touche F12), puis allez dans l'onglet Console.
Tapez directement l'opération pour observer le résultat machine :
javascript
38.9 + 8.2 + 12.9
Résultat : La console affiche 59.99999999999999.
Testez ensuite la condition d'éligibilité à la gratuité :
javascript
(38.9 + 8.2 + 12.9) >= 60
Résultat : La console renvoie false.
Observez pourquoi l'écran affichait pourtant 60,00 € :
javascript
(38.9 + 8.2 + 12.9).toLocaleString("fr-FR", { style: "currency", currency: "EUR" })
Résultat : La console affiche bien "60,00 €".
Enfin, vérifiez qu'un arrondi au centime corrigerait le test :
javascript
(Math.round((38.9 + 8.2 + 12.9) * 100) / 100) >= 60
Résultat : La console renvoie true.
Pour vérifier l'Hypothèse 2 (Vérification des règles de livraison)
Dans la console (F12), chargez les données de livraison :
javascript
fetch("data/livraison.json").then(r => r.json()).then(d => console.log("Seuil de gratuité :", d.seuilGratuite));
Résultat : La console affiche Seuil de gratuité : 60.
Dans l'onglet Sources, ouvrez js/livraison.js à la ligne 27 : constatez que la ligne est bien if (montant >= regles.seuilGratuite) return 0; (l'opérateur est bien >=).
Pour vérifier l'Hypothèse 3 (Absence d'impact d'une promotion)
Dans la boutique, composez le bouquet « Blanc pur » en taille « Moyen » avec « Vase », et indiquez le code postal 37000.
Laissez le champ de code promo vide.
Inspectez la ligne de remise dans le récapitulatif : elle indique 0,00 €.
Dans la console (F12), vérifiez la variable d'état :
javascript
JSON.parse(localStorage.getItem("panier-roussel")).codePromo
Résultat : La valeur est une chaîne vide "", confirmant qu'aucune promotion n'est en cause.

(Conformément à vos consignes, aucun correctif n'a été appliqué à ce stade.)

# Prompt ticket 7

Bonjour, une cliente voulait écrire un long message sur la carte, pour un mariage. Le site l’a bloquée au
milieu de sa phrase. Pouvez-vous enlever cette limite ? Mes clientes doivent pouvoir écrire ce qu’elles
veulent.
Voila le probleme.
Tu m'aides à trouver la cause, je suppose que c'est dans composition.js j'ai un doute si tu ne trouves pas regarde dans les autres.
1.Propose trois hypothèses au plus, classées.
2.Pour chacune : comment la vérifier dans le navigateur.
3.Aucun correctif à ce stade.

# Reponse prompt

Voici l'analyse du problème d'après l'examen de vos fichiers (js/composition.js, data/catalogue.json et index.html).

Votre intuition était la bonne : le comportement provient bien de js/composition.js, configuré à partir des données de data/catalogue.json.

1. Trois hypothèses classées
Hypothèse 1 (La véritable cause — dans data/catalogue.json et js/composition.js) : Restriction native par la propriété maxLength et découpage slice
Explication :
Dans data/catalogue.json (ligne 60), l'option carte comporte un paramètre de limitation :
json
"carte": { "libelle": "Carte avec votre message", "prix": 2.5, "longueurMax": 150 }
Dans js/composition.js, cette limite est appliquée au champ de texte à deux niveaux :
À la ligne 36 : document.querySelector("#message-carte").maxLength = reglesOptions.carte.longueurMax; En HTML, lorsqu'un élément <textarea> reçoit la propriété maxLength = 150, le navigateur bloque physiquement la saisie au clavier dès que le 150ᵉ caractère est atteint. La cliente a donc été bloquée net en pleine rédaction.
À la ligne 75 : options.message = options.carte ? message.slice(0, reglesOptions.carte.longueurMax) : ""; Le script tronque également tout message qui dépasserait cette longueur.
Hypothèse 2 (dans index.html) : Attribut maxlength codé en dur dans le balisage HTML d'origine
Explication : On pourrait penser que le fichier index.html définissait déjà un attribut maxlength="150" en dur sur la balise <textarea id="message-carte">. L'examen de index.html montre que la balise est neutre (<textarea id="message-carte" rows="3" ...></textarea>). La limitation ne vient donc pas du HTML statique, mais bien de son injection par le script JavaScript au chargement.
Hypothèse 3 (dans js/composition.js) : Interception de la saisie clavier par un écouteur d'événement (keydown / keypress)
Explication : On pourrait imaginer qu'un script surveillait chaque touche tapée et bloquait l'événement via un evenement.preventDefault() dès le dépassement d'une certaine longueur. L'examen de composition.js montre qu'aucun événement clavier bloquant n'est utilisé ; c'est le comportement par défaut imposé par le navigateur via l'attribut maxLength.
2. Comment vérifier chaque hypothèse dans le navigateur
Pour vérifier l'Hypothèse 1 (Vérification de maxLength dans la console et test de frappe)
Ouvrez le site dans le navigateur, cliquez sur « Composer » pour n'importe quel bouquet et cochez « Carte avec votre message ».
Ouvrez les Outils de développement (F12), onglet Console.
Inspectez la valeur de la propriété appliquée au champ :
javascript
document.querySelector("#message-carte").maxLength
Résultat : La console renvoie 150.
Essayez de taper ou de coller un long texte dans le champ : la saisie s'arrête exactement au 150ᵉ caractère.
Toujours dans la console, retirez temporairement cette limite pour constater la différence :
javascript
document.querySelector("#message-carte").removeAttribute("maxlength");
Tapez à nouveau : le blocage de saisie disparait immédiatement dans le champ.
Pour vérifier l'Hypothèse 2 (Vérification du code source HTML initial)
Sur la page, faites un clic droit puis « Afficher le code source de la page » (ou Ctrl+U).
Recherchez <textarea id="message-carte"> :
html
<textarea id="message-carte" rows="3" class="mt-1 w-full rounded border border-roussel-gris bg-white p-2"></textarea>
Résultat : Il n'y a aucun attribut maxlength dans le code source original, confirmant qu'il est injecté dynamiquement par JavaScript.
Pour vérifier l'Hypothèse 3 (Vérification des écouteurs d'événements)
Dans les DevTools (F12), onglet Éléments (Elements), sélectionnez le <textarea id="message-carte">.
Dans le panneau de droite, cliquez sur l'onglet Écouteurs d'événements (Event Listeners).
Déroulez la liste : vous constaterez qu'il n'y a aucun écouteur keydown, keyup ou keypress, écartant l'hypothèse d'un script d'interception du clavier.

(Conformément à vos consignes, aucun correctif n'a été appliqué à ce stade.)