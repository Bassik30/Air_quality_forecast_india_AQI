# 🌍 Analyse temporelle et prédictive de la qualité de l’air en Inde (2015–2020)

**Objectif:** Étudier l’évolution quotidienne de la pollution atmosphérique (AQI) dans les grandes villes indiennes (2015–2020) et **prédire** l’AQI à court/moyen terme pour **anticiper les risques sanitaires et environnementaux**.

**Problématiques**
1) Comment l’évolution quotidienne de la pollution a-t-elle varié entre 2015 et 2020 ?  
2) Dans quelle mesure peut-on **prédire l’AQI** (court/moyen terme) pour alerter plus tôt les décideurs et le public ?

---

## 🔎 Résumé exécutif

- **EDA** : pics hivernaux récurrents (nov–jan), amélioration nette pendant la mousson (juin–sept).  
- **Variabilité spatiale** : Delhi ≫ Kolkata/Guwahati ≫ villes du sud (Coimbatore, Amaravati).  
- **Modélisation** (Random Forest) : **R² = 0.903**, MAE = 18.96, RMSE = 33.78.  
- **Prévision** (Prophet – Delhi) : légère **tendance baissière** depuis ~2017, **saisonnalité persistante** (pics hivernaux).  
- **Polluants clés** : **PM₂.₅** et **PM₁₀** dominent l’explication de l’AQI.

---

## 🧰 Stack & méthodes

- **Langages** : Python (pandas, numpy)
- **ML / Time series** : scikit-learn (RandomForestRegressor), Prophet.  
- **Dataviz** : matplotlib, seaborn, **Folium** (carte).  
- **Repro** : notebooks `.ipynb`, environnement conda/venv, `requirements.txt`.

---

## 📦 Données

- **Source** : *Central Pollution Control Board (CPCB, India)* — fichier `city_day.csv` (agrégation quotidienne par ville).  
- **Variables clés** : `City, Date, PM2.5, PM10, NO, NO2, NOx, NH3, CO, SO2, O3, Benzene, Toluene, Xylene, AQI, AQI_Bucket`.  
- **OMS (seuils recommandés)** : PM₂.₅ 15 µg/m³ (24h), PM₁₀ 45 µg/m³ (24h), NO₂ 25 µg/m³ (24h), O₃ 100 µg/m³ (8h) [(WHO, 2021)](https://www.who.int/publications/i/item/9789240034228).

➡️ **Prétraitements** :  
- Interpolation **linéaire par ville** des valeurs manquantes, suppression de `Xylene` (>60% NA).  
- Création : `Year`, `Month`, `Season` (Winter, Summer, Monsoon, Autumn), moyennes mobiles (7/30 j).  
- Sélection **Top-10 villes** (moins de NA AQI) : Delhi, Amritsar, Jorapokhar, Amaravati, Brajrajnagar, Kolkata, Guwahati, Coimbatore, Shillong, Chandigarh.

---

## 📁 Structure du repo

Voici l’organisation du projet :

```text
air_quality_forecast_india/
├─ data/
│  └─ city_day.csv
├─ notebooks/
│  ├─ 01_eda_cleaning.ipynb
│  ├─ 02_model_random_forest.ipynb
│  └─ 03_forecast_prophet_delhi.ipynb
├─ figs/
│  ├─ output_24_0.png
│  ├─ output_36_0.png
│  ├─ output_36_2.png
│  ├─ output_40_0.png
│  ├─ output_44_0.png
│  ├─ output_45_0.png
│  ├─ output_45_1.png
│  ├─ output_47_0.png
│  ├─ output_58_1.png
│  ├─ output_59_0.png
│  ├─ output_68_0.png
│  └─ output_68_1.png
├─ requirements.txt
└─ README.md
```


## 📊 Résultats clés (aperçu visuel)

**Valeurs manquantes (avant interpolation)**  
![missing](figs/output_24_0.png)
Certaines variables présentent de fortes proportions de valeurs manquantes (jusqu’à 60 % pour *Xylene*). Cela justifie la suppression de cette variable et l’application d’une interpolation linéaire par ville afin de préserver la cohérence temporelle du jeu de données.


**Distribution AQI & classes**  
![dist](figs/output_36_0.png)  
La distribution de l’AQI est très asymétrique avec une longue queue à droite, indiquant des épisodes fréquents de pollution extrême.  

![classes](figs/output_36_2.png)
La majorité des jours se situent entre les catégories *Moderate* et *Poor*, illustrant une pollution chronique dans la plupart des villes étudiées.


**Variabilité inter-villes (boxplot)**  
![boxplot](figs/output_40_0.png)
Delhi se distingue nettement avec les niveaux d’AQI les plus élevés et les plus volatils. Shillong et Coimbatore affichent les indices les plus faibles, ce qui traduit l’impact de la densité urbaine et du trafic routier sur la qualité de l’air.

**Évolution temporelle dans 3 villes (30j)**  
![evo-3villes](figs/output_44_0.png)
Toutes les séries présentent un cycle saisonnier marqué. La pollution atteint son maximum pendant l’hiver (novembre–janvier) et diminue pendant la mousson (juin–septembre). Delhi conserve des niveaux systématiquement plus élevés que Kolkata et Guwahati.

**Mensuel & saisonnier**  
![month](figs/output_45_0.png) ![season](figs/output_45_1.png)

Les concentrations de polluants augmentent fortement à la fin de l’automne et en hiver, probablement à cause des inversions thermiques et du brûlage agricole.  
La saison hivernale est la période la plus critique, tandis que la mousson améliore nettement la qualité de l’air grâce au lessivage atmosphérique.

**Carte Folium — AQI moyen (2015–2020)**  
![map](figs/output_47_0.png)

La carte met en évidence un gradient géographique nord–sud. Le nord (Delhi, Amritsar, Kolkata, Guwahati) est le plus pollué, tandis que le sud (Coimbatore, Amaravati) affiche des niveaux d’AQI plus faibles grâce à une meilleure dispersion des particules.

**Random Forest — importance & perf**  
![rf-imp](figs/output_58_1.png) ![rf-scatter](figs/output_59_0.png)

Les particules fines PM₂.₅ et PM₁₀ sont les variables les plus déterminantes dans la prédiction de l’AQI. Les gaz tels que NO₂ et CO contribuent également mais dans une moindre mesure.  
Les valeurs prédites s’alignent étroitement sur la diagonale, avec un R² ≈ 0.90. Le modèle reproduit fidèlement les niveaux de pollution, sauf lors des épisodes extrêmes où il tend à sous-estimer.

**Prophet — prévision & composantes (Delhi)**  
![prophet-fore](figs/output_68_0.png)  
![prophet-comp](figs/output_68_1.png)

Prophet détecte une légère tendance à la baisse de l’AQI moyen depuis 2017, suggérant un effet des politiques environnementales et du ralentissement industriel en 2020.  
La composante saisonnière montre des pics récurrents entre octobre et janvier. La pollution reste fortement saisonnière malgré une amélioration progressive du niveau de fond.

---

## 📄 Rapport complet

👉 **[Télécharger le rapport complet (PDF)](docs/rapport_AQI_Inde.pdf)**  

