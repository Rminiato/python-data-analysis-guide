# Seaborn — Visualizzazione statistica con Python

## Introduzione

**Seaborn** è una libreria Python basata su matplotlib, progettata per rendere semplici e accattivanti le visualizzazioni statistiche.  
Offre grafici già ottimizzati per l’analisi dei dati e integra perfettamente i DataFrame di Pandas.

---

## Sommario rapido

- [Import e setup](#import-e-setup)
- [Grafici di base](#grafici-di-base)
- [Grafici avanzati e multivariati](#grafici-avanzati-e-multivariati)
- [Personalizzazione dei grafici](#personalizzazione-dei-grafici)
- [Esempi pratici](#esempi-pratici)
- [Errori frequenti e consigli](#errori-frequenti-e-consigli)
- [Risorse utili](#risorse-utili)

---

## Import e setup

```python
import seaborn as sns
import matplotlib.pyplot as plt

# Imposta uno stile globale (opzionale)
sns.set_theme(style="whitegrid") 
```

---

## Grafici di base

| Tipo          | Quando usarlo                                 | Esempio Seaborn                              |
|---------------|-----------------------------------------------|----------------------------------------------|
| scatterplot   | Relazione tra 2 variabili numeriche           | `sns.scatterplot(x="x", y="y", data=df)`     |
| histplot      | Distribuzione di una variabile numerica       | `sns.histplot(data=df["col"], bins=20, kde=True)` |
| barplot       | Confronto tra categorie (medie, conteggi)     | `sns.barplot(x="cat", y="val", data=df)`     |
| boxplot       | Distribuzione e outlier tra gruppi            | `sns.boxplot(x="cat", y="val", data=df)`     |
| lineplot      | Serie temporali o dati ordinati               | `sns.lineplot(x="time", y="val", data=df)`   |

---

## Grafici avanzati e multivariati

- **pairplot:** Scatter matrix per tutte le combinazioni di colonne numeriche  
  ```python
  sns.pairplot(df, hue="species")
  ```
- **heatmap:** Visualizza matrici di correlazione o tabelle pivot  
  ```python
  sns.heatmap(df.corr(), annot=True, cmap="vlag")
  ```
- **violinplot:** Distribuzione dettagliata (unisce boxplot e KDE)  
  ```python
  sns.violinplot(x="cat", y="val", data=df)
  ```
- **swarmplot:** Punti non sovrapposti per categoria  
  ```python
  sns.swarmplot(x="cat", y="val", data=df)
  ```
- **regplot:** Scatter + linea di regressione  
  ```python
  sns.regplot(x="x", y="y", data=df)
  ```

---

## Personalizzazione dei grafici

```python
sns.set_palette("pastel")   # Cambia palette colori globale
plt.title("Titolo del grafico")
plt.xlabel("Etichetta asse X")
plt.ylabel("Etichetta asse Y")
plt.xticks(rotation=45)     # Rotazione delle etichette X
plt.yticks(fontsize=12)
plt.legend(title="Legenda")
plt.tight_layout()          # Migliora la disposizione degli elementi
plt.show()                  # Visualizza il grafico
```

**Consiglio:**  
Dopo aver creato un grafico, puoi modificarlo con comandi matplotlib (`plt.*`).

---

## Esempi pratici

### Scatterplot con colore per categoria
```python
sns.scatterplot(x="altezza", y="peso", hue="sesso", style="sport", data=df)
plt.title("Altezza vs Peso per sesso e sport")
plt.show()
```

### Boxplot + overlay swarmplot
```python
sns.boxplot(x="gruppo", y="valore", data=df, showfliers=False)
sns.swarmplot(x="gruppo", y="valore", data=df, color=".25")
plt.show()
```

### Heatmap delle correlazioni
```python
corr = df.corr()
sns.heatmap(corr, annot=True, cmap="coolwarm")
plt.show()
```

---

## Errori frequenti e consigli

- **Dataset non pulito**: Se hai valori NaN o tipi errati, Seaborn può lanciare errori.  
  *Soluzione*: assicurati che le colonne siano numeriche/categorical e senza NaN.
- **Assi invertiti**: In grafici come boxplot/barplot, ricorda che x=colonna categoriale, y=valore numerico.
- **Grafici sovrapposti**: Chiama `plt.clf()` tra un grafico e l’altro per evitare sovrapposizioni in notebook.
- **Palette colori**: Se hai molte categorie, scegli palette adatte (es. `sns.color_palette("tab20")`).

---

## Risorse utili

- [Documentazione ufficiale Seaborn](https://seaborn.pydata.org/)
- [Gallery con esempi](https://seaborn.pydata.org/examples/index.html)
- [Cheat sheet Seaborn (DataCamp)](https://www.datacamp.com/cheat-sheet/seaborn-cheat-sheet)
