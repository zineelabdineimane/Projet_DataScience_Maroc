# Prévision des exportations marocaines de légumes (HS 07)

**Projet 01 — Data Science appliquée au commerce international**
Binôme : Zine El Abdine Imane et Driouich Ichrak — Responsable : A. Larhlimi — ENCG Settat

## Objectif

Peut-on prévoir la valeur annuelle des exportations marocaines de légumes (chapitre HS 07) de l'année t+1 à partir des valeurs observées jusqu'à l'année t ?

Nous comparons deux modèles de référence (persistance, et persistance + croissance moyenne) avec deux modèles d'apprentissage (Ridge et Gradient Boosting, intégrés dans des pipelines scikit-learn), avec une validation chronologique sans fuite temporelle.

## Données

- **Source** : UN Comtrade, API publique « preview » (sans clé) — https://uncomtrade.org/docs/un-comtrade-api/
- **Extraction** : Maroc (504), exportations (X), partenaire Monde (0), chapitre HS 07, années 2010 à 2024 (15 observations annuelles), extraites le 14/11/2026.
- **Unité** : dollars américains courants (champ `primaryValue`).
- **Conditions d'utilisation** : https://comtrade.un.org/licenseagreement.html — les données sont utilisées uniquement dans le cadre de ce projet universitaire.

## Structure du dossier

```
├── Projet01_Exports_Maroc.ipynb   Notebook complet (15 sections)
├── README.md                   Ce fichier
├── requirements.txt            Bibliothèques et versions exactes
├── journal_contributions.md    Qui a fait quoi
├── data/                       Données brutes, journaux et tableaux de résultats
│   ├── comtrade_maroc_HS07_X_2010_2024_brut.csv   Données brutes de l'API
│   ├── journal_requetes.csv                       Date, paramètres et statut de chaque requête
│   ├── donnees_propres.csv                        Jeu nettoyé (15 lignes)
│   ├── journal_nettoyage.csv                      Transformations appliquées
│   ├── variables_modele.csv                       Variables explicatives et cibles
│   ├── recherche_hyperparametres.csv              Les 34 réglages testés
│   ├── comparaison_modeles.csv                    Scores de tous les modèles
│   ├── sensibilite.csv                            Tests de robustesse
│   └── prevision_2025.csv                         Prévision 2025 et scénarios
├── figures/                    Toutes les figures en PNG (300 dpi)
├── Rapport/                    Rapport LaTeX (.tex) et PDF
└── soutenance/                 Support de présentation
```

## Exécution

**En local**

```bash
pip install -r requirements.txt
```

Ouvrir `Projet01_Exports_Maroc.ipynb` dans Jupyter ou VS Code, puis « Restart » et « Run All ».

**Sur Google Colab** : téléverser le notebook **et** le dossier `data/` dans l'espace de fichiers de Colab, puis « Exécution » → « Tout exécuter ».

**Important :** le dossier `data/` doit rester à côté du notebook. Le notebook relit alors les données sauvegardées au lieu d'interroger l'API, ce qui garantit des résultats identiques. Pour refaire l'extraction depuis l'API, mettre `FORCER_TELECHARGEMENT = True` dans la section 3 (environ une minute, avec des erreurs 429 temporaires gérées automatiquement).

**Reproductibilité** : graine aléatoire fixée (`GRAINE_ALEATOIRE = 42`), découpage chronologique, chemins relatifs. Exécution complète vérifiée le 14/11/2026 avec Python 3.13.3.

## Résultats principaux

| Modèle | WAPE validation croisée (2017–2021) | WAPE test (2022–2024) |
|---|---|---|
| Persistance + croissance moyenne | 6,9 % | **3,1 %** |
| Ridge (croissance), alpha = 100 | 7,5 % | 4,3 % |
| Gradient Boosting (croissance) | 7,8 % | 7,6 % |
| Ridge (niveau) | 11,6 % | 10,0 % |
| Persistance | 10,5 % | 11,4 % |
| Gradient Boosting (niveau) | 11,4 % | 23,9 % |

- La meilleure méthode est une règle simple : prolonger la valeur de l'année au rythme de la croissance moyenne passée. Les modèles d'apprentissage ne font pas mieux.
- Prévoir le **taux de croissance** plutôt que la valeur est décisif : le Gradient Boosting ne peut pas prévoir au-delà des valeurs vues à l'entraînement.
- Le classement des modèles est identique dans tous les tests de sensibilité (sans 2017, sans 2020, fenêtre glissante de 5 ans).
- **Prévision 2025** : environ 2,34 milliards de dollars (+10 %), fourchette indicative à 80 % de 2,09 à 2,64 milliards.

## Répartition du travail

- **Ichrak** : sections 1 à 8 du notebook (problématique, extraction, dictionnaire, nettoyage, analyse exploratoire, construction des variables, découpage et validation) ; parties « Données » et « Analyse exploratoire » du rapport.
- **Imane** : sections 9 à 15 (modèles de référence, modèles alternatifs, hyperparamètres, évaluation, sensibilité, conclusion, reproductibilité) ; parties « Méthodologie », « Résultats », « Discussion » et « Conclusion » du rapport, assemblage du rapport et README.

Le détail des contributions figure dans `journal_contributions.md`.

## Déclaration d'usage de l'IA

Un assistant d'IA a été utilisé pour l'analyse des consignes, l'aide à la structuration du code et la reformulation de certains textes. Toutes les données proviennent de l'API UN Comtrade, aucun résultat n'a été inventé, et l'ensemble du code a été exécuté, vérifié et compris par les deux membres du binôme.

## Références

- Nations Unies, *UN Comtrade API — Documentation* : https://uncomtrade.org/docs/un-comtrade-api/
- Nations Unies, *UN Comtrade — Usage Agreement* : https://comtrade.un.org/licenseagreement.html
- Pedregosa et al. (2011), *Scikit-learn: Machine Learning in Python*, Journal of Machine Learning Research, 12, 2825–2830.