---
layout: post
title: AirAction n°1
subtitle: 3 étapes pour concevoir un test de personnalité avec Airtable
cover-img: /assets/img/path.jpg
thumbnail-img: /assets/img/thumb.png
share-img: /assets/img/path.jpg
tags: airaction airtable test
comments: true
---

Voici un tutoriel pour élaborer un test de personnalité avec Airtable. L'usage d'Airtable permet une grande flexibilité dans la configuration du test et le traitement des données. Ce travail s'inspire d'une vraie mission Freelance. Le temps nécessaire à la réalisation d'un test dépend du nombre de questions/réponses, comptez :
- une bonne demi-journée si vous partez de zéro.
- 1h/1h30 si vous partez du template (dispo à la fin de l'article).
Je remercie [**Florian Verdonck**](https://www.linkedin.com/in/florian-verdonck-52342030/) et [**Menda Konate**](https://www.linkedin.com/in/menda-konate-014748187/) pour les échanges sur ce sujet.

# Introduction

## Objectif

Créer un test de personnalité sur Airtable comprenant :

- Un formulaire pour collecter les informations

![Baptiste Promis Juré](baptiste-pj.png )

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/cdb5b9c7-a387-41e4-a95b-ad5e79d39c69/Formulaire-Personnallite.gif]({{site.url}}/assets/img/page-clients/Formulaire-Personnallite.gif)

Formulaire du test de personnalité

- Un système pour envoyer par email automatisé le résultat du test à l'utilisateur.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3444508c-49e4-4184-aff0-92c559bb8697/chrome-capture_(1).gif](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3444508c-49e4-4184-aff0-92c559bb8697/chrome-capture_(1).gif)

Email reçu par le répondant avec le résultat

Vous pouvez, dès à présent, tester [**le formulaire**](https://airtable.com/shrrjjmWl3unrUXN1). L'email de réponse arrive bien ?

## Domaine d'application de l'outil

- Test de personnalité.
- Conception d'un QCM pour un **examen**.
- Toute situation où vous souhaitez faire correspondre à chaque réponse, un ou plusieurs chiffres, un ou plusieurs mot clefs.

## Concepts abordés

C'est un article avancé. Les concepts abordés sont nombreux :

- Les fonctions **SWITCH** et **CONCATENATE** et **LEN**
- Les types de champ "**Roll Up**", "**Count**" et "**Look Up**"
- Les liaisons à une vue spécifique d'une table
- Les automatisations et la temporalité dans les processus d'automatisation.

## Cahier des charges de l'outil

Ce cahier des charges est issu d'une vraie prestation.

Le test de personnalité devait répondre aux critères suivants :

- Un formulaire est envoyé aux clients potentiels.
- Il contient 20 questions.
- Chaque question propose 7 réponses.
- Chaque proposition correspond à une personnalité particulière.

Ex : Une réponse telle que "Donner l'essentiel de son argent à des organisations qui peuvent rendre le monde meilleur" est associé à la Personnalité "Moine".

- Lorsque le formulaire est validé, le nombre de réponses pour chaque "personnalité" est calculé.
- La synthèse est envoyée automatiquement par email à la personne interrogée sous ce format :

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ca28ed71-cea9-45bb-90be-d5f4c20502c1/Capture_decran_2020-12-29_a_10.58.32.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ca28ed71-cea9-45bb-90be-d5f4c20502c1/Capture_decran_2020-12-29_a_10.58.32.png)

La réalisation de cet outil a nécessité 3 sous-modules :

- la création du formulaire.
- la création de la synthèse à partir des résultats du formulaire.
- le système d'envoi de l'email.

# Etape 1/3 - Création du formulaire

Pour créer le formulaire, 2 tables sont requises :

- Une table "Propositions" listant l'ensemble des propositions du quizz.
- Une table "Quizz" pour générer le formulaire.

## Création de la table "Propositions"

Créer un table contenant 3 colonnes :

- Dans le champ 1 (type :  "Long Text"), lister les propositions de toutes les questions.
- Dans le champ 2 (type : "Single line text"), associer à chaque proposition sa personnalité.
- Dans le champ 3 (type : "Long Text"), associer à chaque proposition sa question.

On obtient un tableau ressemblant à celui-ci.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c5e6b1d6-6606-4f30-a76b-1fa8077d1604/Capture_decran_2020-12-29_a_14.41.50.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c5e6b1d6-6606-4f30-a76b-1fa8077d1604/Capture_decran_2020-12-29_a_14.41.50.png)

Table "Propositions" contenant les propositions associées à leur question et leur personnalité.

- Pourquoi, dans l'image c-dessous, les champs "Personnalité" et "Questions" ne sont pas au format "texte" mais des liens vers d'autres tables ?

## Création des vues "Question X" dans la table "Propositions".

- Dans la table "Propositions", créer autant de vues que de questions.
- Dans notre cas, 20.
- Dans chaque vue, ajouter un filtre concernant le champ "questions" pour ne sélectionner qu'une question.
- Exemple pour la vue "Q1" ne seront gardés que les records ayant un champ "Question" contenant "1- Si" (ce qui est le début du texte de la question 1)

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a794aa1b-379b-44d1-a842-77c602f254d9/Capture_decran_2020-12-29_a_14.50.40.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a794aa1b-379b-44d1-a842-77c602f254d9/Capture_decran_2020-12-29_a_14.50.40.png)

## Création de la table "Quizz"

Créer une table "Quizz" avec les champs suivants :

- Email (type : email)
- Prénom (type : single line text)
- Question 1
- Question 2
- Question 3
- Question 4
- ...
- Question 20

Chaque champ "Question X" est une liaison avec la table "Propositions" **mais elle se restreint à la vue "QX"**.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fdddbca9-c797-44c5-b821-02969b0dcf0b/Capture_decran_2020-12-29_a_15.20.11.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fdddbca9-c797-44c5-b821-02969b0dcf0b/Capture_decran_2020-12-29_a_15.20.11.png)

Le champ de la question 1 ("1 - Si ....implusion") est lié à la vue **Q1** de la table "Propositions".

Attention, Il est important de **ne pas permettre** plusieurs liens à la vue Q1 ("Allow linking to multiple records → OFF) sinon le répondant pourra choisir plusieurs propositions pour chaque question !

Dans la table "Quizz", créer une vue "formulaire" en rendant l'ensemble des champs obligatoire.

Le formulaire est maintenant prêt !

Cliquer sur "Open Form" pour ouvrir le formulaire dans le navigateur

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/cdb5b9c7-a387-41e4-a95b-ad5e79d39c69/Formulaire-Personnallite.gif](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/cdb5b9c7-a387-41e4-a95b-ad5e79d39c69/Formulaire-Personnallite.gif)

Formulaire du test de personnalité

- Et pourquoi ne pas avoir utilisé le type de champ **Single select** ?
- Pourquoi ne pas utiliser un autre outil comme TypeForm, c'est plus joli !
- Comment afficher les personnalités dans la table Quizz.

---

# Etape 2/3 - Création de la synthèse à partir des résultats du formulaire.

Pour rappel, l'objectif est d'obtenir ceci.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ca28ed71-cea9-45bb-90be-d5f4c20502c1/Capture_decran_2020-12-29_a_10.58.32.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ca28ed71-cea9-45bb-90be-d5f4c20502c1/Capture_decran_2020-12-29_a_10.58.32.png)

A partir des éléments stockés dans la table Quizz, Il va nous falloir :

- Faire le calcul de points.
- Formater la synthèse texte.

## Calcul des points

Vous allez vite voir qu'Airtable n'est pas bien adapté à ce type de calcul.

Les 2 méthodes proposées sont assez laborieuses. Si vous avez une autre solution, je suis preneur !

### Méthode 1 (pas optimal  mais sans automatisation) - utiliser une fonction "test"

- Dans la table Quizz, pour chaque [Personnalité] créer un nouveau champ intitulé "Nb de [nom de la personnalité]" de type **FUNCTION**. Dans notre cas, cela fait 7 nouveaux champs.
- La fonction doit tester pour chaque question, si la réponse correspond à la personnalité considéré dans ce champ. Si la réponse est "OUI", c'est 1 point, sinon 0 point. La fonction fait la somme des résultats de ces tests. Voici la fonction utilisé dans le champ "Nb de Amasseur" (type : FUNCTION) :

```jsx
IF({Présonnalité Q1}="Amasseur", 1,0) + IF({Présonnalité Q2}="Amasseur", 1,0) +
IF({Présonnalité Q3}="Amasseur", 1,0) + IF({Présonnalité Q4}="Amasseur", 1,0) +
IF({Présonnalité Q5}="Amasseur", 1,0) + IF({Présonnalité Q6}="Amasseur", 1,0) +
IF({Présonnalité Q7}="Amasseur", 1,0) + IF({Présonnalité Q8}="Amasseur", 1,0) +
IF({Présonnalité Q9}="Amasseur", 1,0) + IF({Présonnalité Q10}="Amasseur", 1,0) +
IF({Présonnalité Q11}="Amasseur", 1,0) + IF({Présonnalité Q12}="Amasseur", 1,0) +
IF({Présonnalité Q13}="Amasseur", 1,0) + IF({Présonnalité Q14}="Amasseur", 1,0) +
IF({Présonnalité Q15}="Amasseur", 1,0) + IF({Présonnalité Q16}="Amasseur", 1,0) +
IF({Présonnalité Q17}="Amasseur", 1,0) + IF({Présonnalité Q18}="Amasseur", 1,0) +
IF({Présonnalité Q19}="Amasseur", 1,0) + IF({Présonnalité Q20}="Amasseur", 1,0)
```

- On voit rapidement la lourdeur de cette méthode et surtout la manutention si l'on souhaite changer les personnalités.
- Par contre comme il n'y a pas besoin d'automatisation, cela peut fonctionner sur un compte Gratuit.

### Méthode 2 - Reformatage des réponses dans une nouvelle table grâce à une automatisation.

- Créer **une nouvelle table** "Réponses Unitaires" avec 4 champs :
    - Dans le champ 2, créer une liaison avec la table "Quizz" que l'on appellera "Répondant" puisque la clef primaire de cette table est l'email du répondant.
    - Dans la champ 3, créer un liaison avec la table "Propositions" que l'on appellera "Réponses"
    - Dans le champ 4, créer un champ Lookup pour récupérer la personnalité associée à la "Réponse".
    - Dans la champ 1, créer une fonction de concatenation intégrant "un morceau" des champs 2 et 3. Ce champ n'est pas important ici. j'ai choisi dans notre exemple : LEFT({Répondant},5)&"-"&LEFT({Réponses},5)

Pour l'instant cette table est vide. Il va falloir l'alimenter à partir des informations du Quizz. C'est le rôle de l'automatisation qui suit.

- Créer **une automatisation nommée** "Générer Réponses Unitaires".
- L'automatisation se lancera dès qu'un nouveau quizz est validé ("When a record is created").

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1e7685ee-3c6d-4eeb-bb11-49f663c9925e/Capture_decran_2020-12-29_a_16.48.51.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1e7685ee-3c6d-4eeb-bb11-49f663c9925e/Capture_decran_2020-12-29_a_16.48.51.png)

- Chaque réponse à une question doit correspondre un nouvel enregistrement dans la table "Réponses Unitaires".
- Cela fait donc **20 actions de création de record** dans la table "Réponses Unitaires" par quizz rempli (autant vous dire que la version gratuite d'Airtable ne suffit pas !).
- Voici la configuration de chaque action (ici pour la question 2)

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/90779456-dcec-4bf0-b976-e3b057d9ea57/Enregistrement_de_lecran_2020-12-31_a_16.02.45.gif](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/90779456-dcec-4bf0-b976-e3b057d9ea57/Enregistrement_de_lecran_2020-12-31_a_16.02.45.gif)

- Une fois l'automatisation activée, voici le résultat de la validation d'un premier formulaire par mottet.julien123@gmail.com puis d'un second par julien.mottet.pro@gmail.com dans la table "Réponses Unitaires" (voir la transition entre les 2 entre le record 80 et 81).

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e7634625-2f48-46e2-95b4-f13e550e7ab0/Capture_decran_2020-12-29_a_17.40.17.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e7634625-2f48-46e2-95b4-f13e550e7ab0/Capture_decran_2020-12-29_a_17.40.17.png)

enregistrements dans la table "Réponses Unitaires" de la fin du questionnaire de Mr mottet.julien123 et du début de celui de Mr [julien.mottet.pro](http://julien.mottet.pro) (transition à la ligne 80)

- Dans la table Quizz, pour chaque [Personnalité K] créer un nouveau champ intitulé "Nb de [Personnalité K]" de type **Count.**

    La configuration du champ est :

    - Table de référence : "Réponses Unitaires"
    - Filtre : Champ "Personnalité" contains  [Personnalité K]

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/39448760-9c57-400c-a60a-098aa84a03d1/Capture_decran_2020-12-29_a_21.05.30.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/39448760-9c57-400c-a60a-098aa84a03d1/Capture_decran_2020-12-29_a_21.05.30.png)

On récupère ainsi **le compte** de chaque personnalité dans la table Quizz à partir des informations générées dans la table Réponses Unitaires.

## Synthèse écrite

- Il suffit de concatener les différents "Nb de [Personnalité]".
- La fonction de synthèse est

```jsx
"- Amasseur : " & {Nb de Amasseur} & " Point(s) \n"&
"- Collectionneur : " & {Nb de Collectionneur} & " Point(s) \n"&
"- Dépensier : " & {Nb de Dépensier} & " Point(s) \n"&
"- Evitant : " & {Nb de Evitant} & " Point(s) \n"&
"- Moine : " & {Nb de Moine} & " Point(s) \n"&
"- Prudent : " & {Nb de Prudent} & " Point(s) \n"&
"- Risquetout : " & {Nb de Risquetout} & " Point(s)"
```

Voici le rendu dans la table Quizz.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/50b61a4e-2790-4780-a49d-cfb3d43423ec/Capture_decran_2020-12-29_a_23.43.33.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/50b61a4e-2790-4780-a49d-cfb3d43423ec/Capture_decran_2020-12-29_a_23.43.33.png)

Résultat !

# Etape 3/3 - Envoi de l'email

- Sur le principe cette étape est assez élémentaire.
- Une automatisation peut envoyer un email à partir de :
    - l'email renseigné par la personne
    - la synthèse obtenue ci-dessous.
- Mais quel Trigger utiliser ?

## Configuration de l'automatisation : quel  Trigger utiliser ?

### **Idée 1 - La validation du formulaire**

- Si l'on utilise comme Trigger la validation du formulaire, l'email sera vide.
- En effet, la création de la synthèse prend un certain temps.
- Si l'automatisation "envoi de l'email" se lance immédiatement à la validation du formulaire, la synthèse est vide et donc l'email est envoyé vide.

### **Idée 2 - La fin des calculs**

- On souhaite s'assurer que les calculs sont bien terminer.
- Ajouter un champ  "Nb de Points" qui somme l'ensemble des champs de type "Nb de [Personalité]".
- Ce nombre sera égal à 20 lorsque tous les points seront renseignés dans les champs "Nb de [Personalité]" puisque nous avons 20 questions.
- Donc une option de Trigger est : "Nb de Points" = 20.
- Ca marche !

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/31a11da7-5626-41f5-92bf-47ece22269cc/Capture_decran_2021-01-06_a_14.19.08.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/31a11da7-5626-41f5-92bf-47ece22269cc/Capture_decran_2021-01-06_a_14.19.08.png)

Enfin, voici la configuration de l'étape 2 de l'automatisation : la génération et l'envoi de l'email. L'ensemble des informations est issue de la table Quizz.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b7a0ce5a-ef5a-42db-a366-0264b524f41c/Capture_decran_2020-12-30_a_00.10.02.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b7a0ce5a-ef5a-42db-a366-0264b524f41c/Capture_decran_2020-12-30_a_00.10.02.png)

Configuration de l'email.

J'espère que cet article était clair.

Si certains points méritent plus d'explications, merci de m'en informer via le Slack Nocode fr ou LinkedIn.

# Etape 4/3 - Maintenant, à vous de jouer !

### → [**TÉLÉCHARGER LE TEMPLATE DE LA BASE**](https://hustling-writer-2516.ck.page/bae07738d3)  ←

## Comment adapter l'outil à ma situation ?

J'enverrai, la semaine prochaine, à toutes les personnes qui télécharge le template, **une vidéo** montrant comment adapter l'outil à votre situation :

- changement du nombre de questions.
- changement du nombre de réponses.
- changement des personnalités.

## Pour plus tard...

il est possible d'envoyer aux répondants, une description de la personnalité "majoritaire".

Cela nécessite un peu de travail puisque l'on doit identifer la personnalité ayant le plus de point.

Si cela vous intéresse, merci [de m'écrire](mailto:julien.mottet.pro@gmail.com) et j'écrirai le détail.

⭐️

# Vous souhaitez me soutenir ?

- Partagez ce post
- Inscrivez-vous à ma [nouvelle chaîne youtube](https://www.youtube.com/channel/UCCEAWvvFjcTuBx5F3izqXxQ)

# Bye ! 👋

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/786a0fae-b50e-49a8-a764-8ed5eaa729c0/Capture_decran_2021-01-03_a_23.46.15.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/786a0fae-b50e-49a8-a764-8ed5eaa729c0/Capture_decran_2021-01-03_a_23.46.15.png)

[https://hustling-writer-2516.ck.page/20a0d621b8](https://hustling-writer-2516.ck.page/20a0d621b8)
