# Optimiser-la-gestion-nettoyez-les-donnees-du-stock-d-une-boutique

Analyse des produits, des ventes et des stocks de BottleNeck.

## Présentation du projet
BottleNeck est un marchand de vins et spiritueux dont les données sont réparties entre un ERP, un site e-commerce et un fichier de correspondance manuel. Les identifiants produits diffèrent entre les systèmes et les extractions comportent des anomalies.
Ma mission est de rapprocher ces trois sources, documenter et traiter les problèmes de qualité, puis analyser les ventes, les prix, les marges et les stocks pour fournir au comité de direction des indicateurs utiles au pilotage de l’activité.

## Problématique
Comment fiabiliser les données de produits, de ventes et de stocks pour éclairer les décisions commerciales et préparer l’évolution vers un système de gestion intégré ?

## Jeu de données
| Source | Contenu | Nombre de lignes initiales |
| --- | --- | ---: |
| `erp.xlsx` | Identifiants produits, prix de vente et d’achat, quantités et statut du stock | 825 |
| `web.xlsx` | SKU, ventes et informations des produits issus du site WordPress | 1 513 |
| `liaison.xlsx` | Correspondance entre `product_id` et `id_web` | 825 |

Les sorties enregistrées du notebook présentent 714 lignes dans le jeu final analysé.
L’analyse porte sur les ventes du 1er au 31 octobre et sur l’état du stock au 31 octobre.

## Outils et méthodes
- Python et Jupyter Notebook : préparation, analyse et documentation.
- pandas et NumPy : contrôles, jointures et calculs des indicateurs.
- Matplotlib, Seaborn et Plotly Express : visualisations.
- SciPy : calcul du Z-score.
- PyGWalker : exploration interactive utilisée dans le notebook.
Méthodes mobilisées : analyse exploratoire, contrôle des doublons et valeurs manquantes, rapprochement des identifiants, statistiques descriptives, Z-score, intervalle interquartile, analyse de concentration et corrélations de Pearson.

## Démarche d’analyse
1. Explorer séparément les trois fichiers et identifier les anomalies.
2. Documenter les traitements : prix négatifs, stocks négatifs, statuts incohérents, identifiants et doublons techniques du site web.
3. Rapprocher les sources et isoler les références sans correspondance.
4. Analyser les prix, le chiffre d’affaires, les volumes vendus, les stocks et les marges.
5. Formuler des recommandations pour fiabiliser les échanges entre les systèmes.

Analyses métier — exemples
1. Mesurer les ventes et leur concentration
Besoin : connaître le chiffre d’affaires du périmètre rapproché et identifier les références qui y contribuent.
```python
df_final["ca_par_article"] = df_final["price"] * df_final["total_sales"]
ca_total = df_final["ca_par_article"].sum()
```
Résultats enregistrés :
- Chiffre d’affaires calculé : 143 745,40 €.
- 435 références, soit 60,92 % des 714 lignes du catalogue analysé, atteignent 80 % du chiffre d’affaires cumulé.
- 434 références, soit 60,78 %, atteignent 80 % des quantités vendues cumulées.

2. Identifier les prix atypiques
Besoin : distinguer les prix qui méritent une vérification des prix élevés pouvant correspondre à une gamme premium.

| Méthode | Résultat enregistré |
| ---| ---|
| Z-score supérieur à 3 |	13 prix atypiques ; premier prix observé à 116,40 € |
| IQR, borne haute d’environ 84,09 € | 31 prix atypiques, soit 4,34 % du catalogue analysé |

On retient ici l’IQR comme la méthode la mieux adaptée à l’asymétrie des prix. La comparaison avec les prix d’achat apporte un élément d’interprétation commerciale, sans prouver à elle seule que chaque prix est correct.

3. Analyser les stocks et les marges
Besoin : repérer les références à écoulement lent et les incohérences de prix.
- Quantité en stock sur le périmètre analysé : 16 731 unités.
- Valorisation calculée au prix de vente : 494 433,90 €.
- Certaines références présentent une couverture estimée de 20 à 30 mois, au rythme des ventes observées.
- Une référence du périmètre final présente une marge négative calculée, avec un prix d’achat supérieur au prix de vente ; aucune vente n’est enregistrée pour cette référence.
La couverture est calculée à partir du stock et des ventes mensuelles. Elle constitue une estimation à rythme constant, et non une prévision de demande.

## Limites
- Les résultats portent sur les lignes rapprochées ; ils ne couvrent pas automatiquement les 825 références ERP.
- La période d’un mois ne permet pas d’établir une saisonnalité annuelle.
- La valorisation au prix de vente n’est pas une valorisation comptable au coût d’achat.
- L’indicateur nommé taux_marge dans le notebook utilise le prix de vente HT comme dénominateur : il correspond à un taux de marque.

   [Notebook complet](Zootyam_Michelle_1_Notebook_042026.ipynb)
