# Identification des utilisateurs de Copilote

Projet réalisé dans le cadre du BE du cours **MOD 7.2 – Introduction à la science des données** (Centrale Lyon), à partir du challenge Kaggle « Qui utilise mon appli ? ».

## Problème

Copilote est un progiciel de gestion édité par Infologic pour l'agro-alimentaire. Chaque session d'utilisation laisse une trace : la suite des actions effectuées (création d'un écran, saisie dans un champ, raccourci clavier…), avec l'écran, la configuration d'écran et la catégorie de fiche concernés.

L'objectif est de retrouver **quel utilisateur a produit une session** à partir de cette trace. C'est une classification supervisée à **247 classes** : 3 279 sessions d'apprentissage étiquetées, 324 sessions de test.

## Démarche

1. **Exploration** : chaque utilisateur utilise toujours le même navigateur ; les sessions ont des longueurs très variables ; aucune ligne n'est supprimée (les sessions très longues sont aussi présentes dans le test).
2. **Deux représentations des sessions** :
   - **A** : 49 caractéristiques tabulaires (volume, durée, rythme, fréquence des 35 types d'actions, écrans / configurations / chaînes, navigateur en one-hot) ;
   - **B** : la session vue comme un texte, pondérée en **TF-IDF** : actions complètes, types d'actions, écrans / configurations / chaînes, paires d'actions consécutives, triplets de types consécutifs, plus le navigateur (57 038 dimensions).
3. **Comparaison de 13 modèles** par validation croisée stratifiée à 5 plis (accuracy et F1 macro).
4. **Évaluation détaillée**, **interprétation** et **prédiction du jeu de test**.

## Résultats

| Modèle | Représentation | Accuracy (CV 5 plis) | F1 macro |
|--|--|--|--|
| **SVM linéaire, C = 3** (retenu) | B (TF-IDF) | **0,977** | **0,969** |
| Passive-Aggressive | B (TF-IDF) | 0,976 | 0,969 |
| kNN cosinus, k = 1 | B (TF-IDF) | 0,951 | 0,942 |
| Extra Trees (200 arbres) | A (tabulaire) | 0,873 | 0,843 |
| Random Forest (200 arbres) | A (tabulaire) | 0,857 | 0,827 |

Sur 20 % de l'apprentissage mis de côté, le modèle retenu obtient une accuracy de 0,962, un F1 macro de 0,950 et une accuracy top-3 de 0,991. La représentation des sessions compte bien plus que le choix du modèle.

## Contenu du dépôt

| Fichier | Rôle |
|--|--|
| `BE_CS1_indications_rendu.ipynb` | notebook complet : exploration, caractéristiques, comparaison des modèles, évaluation, interprétation, prédiction |
| `requirements.txt` | bibliothèques nécessaires |
| `submission.csv` | prédictions sur le jeu de test (format Kaggle : `RowId,prediction`) |

Les fichiers de données (`train.csv`, `test.csv`) ne sont pas inclus.

## Exécution

Testé avec Python 3.14.

```bash
git clone https://github.com/<votre-compte>/be-cs1-copilote.git
cd be-cs1-copilote
python -m venv .venv
source .venv/bin/activate          # Windows : .venv\Scripts\activate
python -m pip install -r requirements.txt
```

1. Télécharger `train.csv` et `test.csv` depuis la page Kaggle du challenge et les placer dans ce dossier.
2. Ouvrir `BE_CS1_indications_rendu.ipynb` et exécuter toutes les cellules (*Restart & Run All*). Durée : 10 à 15 minutes, essentiellement pour la comparaison des 13 modèles.
3. Le fichier `submission.csv` est régénéré à la fin du notebook.

Les résultats sont reproductibles (graine aléatoire fixée à 42).
