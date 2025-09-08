# Pandas — Analisi dati in Python

## Introduzione

**Pandas** è la libreria Python di riferimento per la manipolazione, analisi e pulizia di dati strutturati (tabelle, CSV, Excel, database).  
Le sue strutture principali sono **DataFrame** (tabella) e **Series** (vettore).

---

## Sommario rapido

- [Strutture dati principali](#strutture-dati-principali)
- [Lettura e scrittura di file](#lettura-e-scrittura-di-file)
- [Esplorazione dei dati](#esplorazione-dei-dati)
- [Accesso ai dati](#accesso-ai-dati)
- [Indicizzazione: loc, iloc, at, iat](#indicizzazione)
- [Filtraggio e selezione condizionale](#filtraggio-e-selezione-condizionale)
- [Ordinamento, reindicizzazione e rinominazione](#ordinamento-reindicizzazione-e-rinominazione)
- [Aggiunta e modifica dei dati](#aggiunta-e-modifica-dei-dati)
- [Gestione dell'indice](#gestione-dellindice)
- [Gestione delle date](#gestione-delle-date)
- [Gestione valori mancanti](#gestione-valori-mancanti)
- [Raggruppamento dati (groupby)](#raggruppamento-dati-groupby)
- [Combinazione DataFrame: merge, concat](#combinazione-dataframe)
- [Pivot Table e MultiIndex](#pivot-table-e-multiindex)
- [Best practices & errori comuni](#best-practices-errori-comuni)
- [Risorse utili](#risorse-utili)

---

## Strutture dati principali

### **Series**
```python
import pandas as pd
s = pd.Series([10, 20, 30], index=["a", "b", "c"])
```

### **DataFrame**
```python
data = {
    "name": ["Alice", "Bob", "Charlie"],
    "age": [25, 30, 35]
}
df = pd.DataFrame(data)
```

---

## Lettura e scrittura di file

### **CSV**
```python
df = pd.read_csv("dati.csv")
df.to_csv("output.csv", index=False)
```

### **Excel**
```python
df = pd.read_excel("dati.xlsx", sheet_name="Foglio1")
df.to_excel("output.xlsx", index=False)
```

---

## Esplorazione dei dati

```python
df.head()     # Prime 5 righe
df.tail(3)    # Ultime 3 righe
df.info()     # Info su colonne, tipi, nulli
df.describe() # Statistiche descrittive
df.shape      # (righe, colonne)
df.columns    # Nomi colonne
df.index      # Indici
df.dtypes     # Tipi di dato di ogni colonna
```
**Consiglio:**  
Dai sempre un’occhiata a `df.head()` e `df.info()` dopo ogni operazione importante.

---

## Accesso ai dati

```python
df["name"]              # Una colonna (Series)
df[["name", "age"]]     # Più colonne (DataFrame)
```

---

## Indicizzazione

### **loc[]** — per etichette
```python
df.loc[0]                        # Prima riga
df.loc[0, "name"]                # Valore 'name' nella riga 0
df.loc[0:2, ["name", "age"]]     # Righe 0-2, colonne specifiche
```
### **iloc[]** — per posizione numerica
```python
df.iloc[0]                # Prima riga
df.iloc[0, 1]             # Prima riga, seconda colonna
df.iloc[:3, :2]           # Prime 3 righe, prime 2 colonne
```
### **at[] / iat[]**
```python
value_at = df.at[1, "name"]    # Accesso rapido etichette
value_iat = df.iat[0, 0]       # Accesso rapido posizioni
```

**Errori frequenti:**  
- confondere `.loc` (etichette) con `.iloc` (posizioni)
- usare liste con slice nelle chiavi (`df.loc[[1,2:5]]` è errato, va bene `df.loc[1:5]` o `df.loc[[1,2,3,4,5]]`)

---

## Filtraggio e selezione condizionale

```python
df[df["age"] > 30]
df[(df["age"] > 25) & (df["name"] == "Alice")]
df[df["name"].isin(["Alice", "Bob"])]
df[df["age"].between(25, 30)]
```
**Nota:**  
Le condizioni multiple devono essere tra parentesi.

---

## Ordinamento, reindicizzazione e rinominazione

### **Ordinamento dei dati**

#### **sort_values()**
Ordina il DataFrame in base ai valori di una o più colonne.

```python
# Ordinamento crescente
df_sorted = df.sort_values(by="age")
# Ordinamento decrescente
df_sorted_desc = df.sort_values(by="age", ascending=False)

# Ordinamento su più colonne, con ordini diversi
df.sort_values(by=["department", "salary"], ascending=[True, False])
```
**Quando usarlo:**  
- Per trovare top performer, record più recenti, classificare dati ecc.

**Errori comuni:**  
- Dimenticare `ascending=` su più colonne (l’ordine deve essere una lista).
- Ordinare senza assegnare il risultato (`sort_values` non modifica il DataFrame originale a meno che non usi `inplace=True`).

#### **sort_index()**
Ordina secondo l’indice (righe o colonne).
```python
# Ordinamento delle righe in base all’indice
df.sort_index()

# Ordinamento delle colonne per nome
df.sort_index(axis=1)
```

#### **reset_index()**
Torna all’indice numerico standard, utile dopo filtri o raggruppamenti.
```python
df_reset = df.reset_index()  # L’indice vecchio diventa colonna
df_reset_drop = df.reset_index(drop=True)  # L’indice vecchio viene eliminato
```
**Quando usarlo:**  
- Dopo filtri, raggruppamenti, merge, quando l’indice non ha più senso.

#### **rename()**
Rinomina colonne o indici.
```python
df_renamed = df.rename(columns={"A": "Alpha", "B": "Beta"})
df_renamed_index = df.rename(index={0: "Primo", 1: "Secondo"})
```
**Errori comuni:**  
- Cambiare solo una colonna: attenzione a passare il parametro `columns={...}`.
- Rinominare indici stringa e numerici: controlla sempre tipo e valori dell’indice.

#### **drop()**
Elimina righe o colonne.
```python
# Elimina colonna "age"
df_drop_col = df.drop("age", axis=1)

# Elimina righe con indici 0 e 2
df_drop_rows = df.drop([0, 2], axis=0)

# Modifica in-place
df.drop("salary", axis=1, inplace=True)
```
**Attenzione:**  
- `axis=0`: righe, `axis=1`: colonne.
- `inplace=True` modifica direttamente il DataFrame.

#### **duplicated() & drop_duplicates()**
Identifica e rimuove duplicati.
```python
# Trova righe duplicate (tutte le colonne)
df.duplicated()

# Trova duplicati su una colonna
df.duplicated(subset="name")

# Rimuovi duplicati mantenendo la prima occorrenza
df_clean = df.drop_duplicates()

# Rimuovi duplicati mantenendo solo l’ultima occorrenza
df_clean_last = df.drop_duplicates(keep="last")
```
**Quando usarlo:**  
- Pulizia dati importati da fonti diverse.

---

## Aggiunta e modifica dei dati

```python
df["salary"] = [50000, 60000, 55000]    # Nuova colonna
df.loc[0, "age"] = 26                   # Modifica valore
df.at[0, "age"] = 27                    # Alternativa rapida
# Nuova riga
new_row = {"name": "Diana", "age": 28, "salary": 58000}
df.loc[len(df)] = new_row

# Eliminazione di righe o colonne
df.drop("salary", axis=1, inplace=True)  # Elimina la colonna 'salary'
df.drop(1, axis=0, inplace=True)         # Elimina la riga con indice 1
```
**Attenzione:**  
L’aggiunta di righe con `.loc[len(df)]` può non essere efficiente per DataFrame grandi.

---

## Gestione dell'indice

```python
df.set_index("name", inplace=True)
df.reset_index(drop=True, inplace=True)
```

**Note:**  
- `drop=True` elimina l’indice precedente.
- `drop=False` lo mantiene come nuova colonna nel DataFrame.

---

## Gestione delle date

```python
df["date"] = pd.to_datetime(df["date"])
df.set_index("date", inplace=True)
df.loc["2023-01":"2023-03"]  # Selezione di un intervallo temporale
```
- Estrazione di componenti:
```python
df["year"] = df["date"].dt.year
df["month"] = df["date"].dt.month
df["weekday"] = df["date"].dt.day_name()
```
- Filtraggio per intervallo:
```python
df[(df["date"] >= "2022-01-01") & (df["date"] <= "2022-12-31")]
```

---

## Gestione valori mancanti

### **Individuazione**
```python
df.isnull()       # DataFrame booleano
df.isnull().sum() # Conteggio per colonna
df.notnull()      # Inverso
```

### **Rimozione**
```python
# Rimuove tutte le righe con almeno un valore mancante
df_no_na = df.dropna(axis=0, how='any')

# Rimuove colonne in cui TUTTI i valori sono NaN
df_no_col = df.dropna(axis=1, how='all')

# Tiene solo righe con almeno 2 valori non nulli
df_thresh = df.dropna(thresh=2)
```
**Quando usarlo:**  
- Quando i dati nulli non sono recuperabili o disturbano l’analisi.

**Errori comuni:**  
- Usare `dropna()` senza capire che elimina anche dati utili.

### **Sostituzione**
```python
# Sostituzione con valore fisso
df_fill = df.fillna(0)

# Sostituzione con media di colonna
df['val'] = df['val'].fillna(df['val'].mean())

# Sostituzione diversa per ogni colonna
df_fill_dict = df.fillna({"A": 0, "B": df["B"].mean(), "C": 99})

# Propagazione valori validi (forward/backward fill)
df_ffill = df.fillna(method='ffill')
df_bfill = df.fillna(method='bfill')
```

### **Interpolazione**
```python
df.interpolate(method='linear')   # Interpolazione lineare
```
**Quando usarlo:**  
- Serie temporali, dati continui.

**Attenzione:**  
- Capisci perché i dati sono mancanti prima di sostituirli o eliminarli!

### **Altri trucchi**
```python
# Sostituzione valori anomali
df.replace(-999, np.nan)

# Mascheramento condizionale
df.where(df > 0, np.nan)
```

---

## Raggruppamento dati (groupby)

Il metodo `.groupby()` consente di suddividere i dati in gruppi in base ai valori di una o più colonne e applicare funzioni di aggregazione.

```python
# Media per gruppo
df.groupby("gender")["salary"].mean()

# Statistiche multiple
df.groupby("department")["salary"].agg(["mean", "max", "min"])

# Raggruppamento su più colonne
df.groupby(["gender", "department"])["salary"].mean()

# Uso di aggregazioni personalizzate
df.groupby("department").agg({"salary": "mean", "age": "max"})
```

**Funzioni di aggregazione principali:**  
- `sum()`, `min()`, `max()`, `mean()`, `median()`, `var()`, `std()`, `count()`
- `describe()`: statistiche descrittive per gruppo

**Errori comuni:**  
- Dimenticare di azzerare l'indice dopo un groupby (`reset_index()`)
- Non specificare correttamente le colonne su cui applicare le funzioni

---

## Combinazione DataFrame

### **merge(): unione tipo SQL**

#### **Inner Join**
```python
merged = pd.merge(df1, df2, on="id")
```
**Solo valori presenti in entrambi i DataFrame.**

#### **Left Join**
```python
merged = pd.merge(df1, df2, on="id", how="left")
```
**Tutti i valori di df1, NaN se non presenti in df2.**

#### **Right e Outer Join**
```python
merged = pd.merge(df1, df2, on="id", how="right")
merged = pd.merge(df1, df2, on="id", how="outer")
```

#### **Chiavi con nomi diversi**
```python
merged = df1.merge(df2, left_on="idA", right_on="idB")
```

#### **Join su indici**
```python
df1_indexed = df1.set_index("colA")
df2_indexed = df2.set_index("colA")
merged_idx = pd.merge(df1_indexed, df2_indexed, left_index=True, right_index=True)
```

#### **Gestione suffissi colonne duplicate**
```python
merged = pd.merge(df1, df2, on="id", suffixes=('_left', '_right'))
```

**Errori comuni:**  
- Dimenticare `how=`, usare join su colonne con nomi diversi senza specificarlo.
- Merge che crea troppe righe (duplicazione dati per chiavi non uniche).

---

### **concat(): aggiunta di righe o colonne**

#### **Aggiunta verticale (stesso schema)**
```python
concat_v = pd.concat([df1, df2], axis=0, ignore_index=True)
```
#### **Aggiunta orizzontale (stesso indice)**
```python
concat_h = pd.concat([df1, df2], axis=1)
```
#### **MultiIndex con keys**
```python
concat_multi = pd.concat([df1, df2], keys=['df1', 'df2'])
```

**Quando usarlo:**  
- Unire dati da file simili, append, dati di periodi diversi.

**Errori comuni:**  
- Indici duplicati o non coerenti.
- Unire DataFrame con colonne diverse senza gestire NaN.

---

## Pivot Table e MultiIndex

### **Pivot Table**
Le pivot table aggregano dati secondo righe e colonne specificate.
```python
pd.pivot_table(df, values="sales", index="store", columns="month", aggfunc="sum")
```

### **MultiIndex**
Un indice a più livelli, utile per rappresentare dati ad alta dimensionalità.
```python
df = df.set_index(["country", "city"])
df.loc[("Italy", "Rome")]
df = df.reset_index()
df = df.sort_index(level=["country", "city"])
```

---

## Best practices & errori comuni

- Usa sempre `.copy()` quando vuoi duplicare un DataFrame senza collegamento all’originale.
- Attenzione all’uso errato di `.loc[]` e `.iloc[]` (vedi sintassi corretta sopra).
- Gestisci valori mancanti e duplicati prima dell’analisi.
- Standardizza i nomi delle colonne:
  ```python
  df.columns = df.columns.str.strip().str.lower().str.replace(' ', '_')
  ```
- Dopo merge o concat, controlla sempre duplicati e valori mancanti.
- Stampa spesso `df.head()` dopo ogni operazione di combinazione o pulizia!
- In caso di errore, leggi il traceback: spesso indica una chiave mancante o indici non allineati.
- Se non sei sicuro, lavora sempre su una copia:
  ```python
  df2 = df.copy()
  ```

---

## Tabella riassuntiva rapida

| Funzione     | Uso principale            | Errori tipici                |
|--------------|--------------------------|------------------------------|
| sort_values  | Ordina per colonne       | inplace, ordini multipli     |
| sort_index   | Ordina per indice        | axis                         |
| reset_index  | Reimposta indice         | drop=True/False              |
| merge        | Join come SQL            | chiavi, how, duplicati       |
| concat       | Append, affianca         | indici, colonne disallineate |
| dropna       | Elimina nulli            | elimina troppo               |
| fillna       | Sostituisce nulli        | valori non coerenti          |

---

## Risorse utili

- [Documentazione ufficiale Pandas](https://pandas.pydata.org/docs/)
- [10 minuti a Pandas (tutorial)](https://pandas.pydata.org/pandas-docs/stable/user_guide/10min.html)
