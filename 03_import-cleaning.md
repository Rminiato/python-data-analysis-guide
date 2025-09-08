# Importazione, Pulizia e Reshape dei Dati

## Introduzione

Questa sezione raccoglie i metodi e i trucchi fondamentali per importare, pulire, trasformare e validare dati in Pandas.

---

## Sommario rapido

- [Importazione dati](#importazione-dati)
- [Conversione e gestione tipi di dato](#conversione-e-gestione-tipi-di-dato)
- [Standardizzazione e pulizia valori](#standardizzazione-e-pulizia-valori)
- [Gestione valori anomali e outlier](#gestione-valori-anomali-e-outlier)
- [Reshape dei dati (pivot, melt, transpose)](#reshape-dei-dati)
- [Verifica dati categorici e consistenza](#verifica-dati-categorici-e-consistenza)
- [Validazione](#validazione)
- [Errori tipici e consigli](#errori-tipici-e-consigli)

---

## Importazione dati

### CSV
```python
df = pd.read_csv("data.csv", sep=",", header=0, index_col=None, dtype={"id": int})
```
**Parametri utili:**  
- `sep`, `header`, `index_col`, `dtype`, `parse_dates`, `skiprows`, `usecols`, `nrows`, `encoding`, `na_values`

### Excel
```python
df = pd.read_excel("data.xlsx", sheet_name="Sheet1", header=0, index_col="ID")
```

### HTML
```python
table_list = pd.read_html("https://esempio.com/dati.html")
df_html = table_list[0]
```

### SQL
```python
import sqlite3
conn = sqlite3.connect("database.db")
df_sql = pd.read_sql("SELECT * FROM tabella", conn, index_col="ID")
```

### Clipboard
```python
df = pd.read_clipboard(sep="\t")
```

### API/JSON
```python
import requests
response = requests.get("https://api.example.com/data")
data = response.json()
df = pd.DataFrame(data)
```

---

## Conversione e gestione tipi di dato

```python
df["col"] = df["col"].astype(int)             # Conversione tipo
df["data"] = pd.to_datetime(df["data"])       # Conversione data
df["categoria"] = df["categoria"].astype("category")
```

**Attenzione:**  
Colonne importate da Excel/CSV possono avere spazi, simboli o formattazioni strane!

---

## Standardizzazione e pulizia valori

```python
df.columns = df.columns.str.strip().str.lower().str.replace(" ", "_")
df["prezzo"] = df["prezzo"].str.strip("€").str.replace(".", "").astype(float)
df["nome"] = df["nome"].str.title().str.strip()
```

**Esempio:**  
Rimozione di spazi e simboli, uniformazione maiuscole/minuscole.

---

## Gestione valori anomali e outlier

```python
df = df[df["valore"] < 1000]  # Filtra outlier
df.loc[df["punteggio"] > 5, "punteggio"] = 5  # Limita valori massimi
```
**Date fuori range:**
```python
df["data"] = pd.to_datetime(df["data"])
oggi = pd.Timestamp.today()
df.loc[df["data"] > oggi, "data"] = oggi
```

---

## Reshape dei dati

### Transpose
```python
df.T
```

### Melt (wide → long)
```python
df_long = pd.melt(df, id_vars=["id"], var_name="variabile", value_name="valore")
```

### Pivot (long → wide)
```python
df_pivot = df.pivot_table(values="vendite", index="mese", columns="prodotto", aggfunc="sum")
```

---

## Verifica dati categorici e consistenza

```python
categorie = ["A", "B", "C"]
df[~df["categoria"].isin(categorie)]  # Trova valori non validi

# Uniforma nomi simili (mapping)
mapping = {"eur":"europe", "us":"usa"}
df["regione"] = df["regione"].replace(mapping)
```

**Verifica valori unici:**
```python
df["col"].unique()
```

---

## Validazione

```python
assert df["voto"].max() <= 10
assert df["categoria"].isin(["A", "B", "C"]).all()
```

---

## Errori tipici e consigli

- **Problemi di encoding:** Prova `encoding="utf-8"` o `"latin1"` se hai errori di caratteri.
- **Spazi nascosti:** Usa `.str.strip()` su colonne testuali.
- **Valori anomali:** Controlla sempre `df.describe()` e `df.info()`.
- **Tipi errati:** Dopo l’import, verifica i tipi con `df.dtypes`.
- **Non fidarti mai dei dati importati!** Ispeziona, pulisci e valida sempre.

---

## Risorse utili

- [Pandas IO tools](https://pandas.pydata.org/docs/reference/io.html)
- [Guida Data Cleaning con Pandas (KDnuggets)](https://www.kdnuggets.com/2020/01/pandas-data-cleaning.html)
