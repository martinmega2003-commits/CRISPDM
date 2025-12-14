# CRISP-DM analýza krajů ČR

Projekt kombinuje data o cenách nemovitostí, kriminalitě a mzdách v krajích České republiky (2019–2023) a pomocí jednoduché prognózy do roku 2027 hledá „nejvýhodnější“ kraj pro život. Analýza probíhá v notebooku `T00276589.ipynb` podle kroků CRISP-DM.

## Obsah repozitáře
- `T00276589.ipynb` — kompletní datová analýza, čištění, vizualizace, tvorba indexů a predikce.
- `T00276589.docx` — doprovodná zpráva/odůvodnění.
- `DATA/` — přiložené zdrojové XLSX soubory (viz níže).

## Struktura dat (přiloženo v repu)
- `DATA/Ceny nemovitostí/` — realitní data; 2019–2023 po jednom souboru na rok + historické intervaly (2006–2018) pro případné rozšíření.
- `DATA/Kriminalita/` — kriminalita po krajích, jeden XLSX na kraj (2019+).
- `DATA/Mzdy/`
  - `Do roku 2010/` — starší mzdové řady.
  - `od roku 2011/` — mzdové řady 2011+, jeden soubor na kraj.

### Poznámka k cestám v notebooku
Notebook byl původně psaný pro Google Colab (`/content/drive/MyDrive/DATA/...`). Pokud spouštíte lokálně, upravte na relativní cesty k tomuto repu, např.:
- Realitní soubory: `DATA/Ceny nemovitostí/*.xlsx`
- Kriminalita: `DATA/Kriminalita/*.xlsx`
- Mzdy: `DATA/Mzdy/od roku 2011/*.xlsx` (případně `Do roku 2010` pro starší období)

## Postup analýzy (CRISP-DM)
- **Business understanding:** Najít kraj s nejlepší kombinací dostupnosti bydlení, bezpečnosti a mezd.
- **Data understanding:** XLSX z ČSÚ s různými záhlavími/metadata; kontrola struktury a chybějících hodnot.
- **Data preparation:** Automatické nalezení skutečné hlavičky, odfiltrování metadat a řádků mimo kraje, převod názvů sloupců na jednotné tvary, sloučení realitných, mzdových a krimi dat do `big_df`.
- **Modeling & Evaluation:**
  - Normalizace (MinMax/StandardScaler) a základní quality index.
  - Lineární regrese pro predikce 2024–2027 (ceny domů/bytů, mzdy); kriminalita drží poslední známou hodnotu.
  - **Index minulosti a budoucnosti:** váhy 0.5 (mzdy), -0.2 (domy), -0.2 (byty), -0.1 (kriminalita); kombinace do `INDEX_OVERALL`.
  - Doplňkově hierarchická klasifikace regionů a mapová vizualizace (GeoJSON z GitHubu).

## Klíčové výsledky (`INDEX_OVERALL`)
- Nejlepší kraj: Útecký (0.0479); následují Plzeňský (0.0375), Královéhradecký (0.0273), Středočeský (0.0215), Vysočina (0.0206).
- Nejslabší kraj: Liberecký (-0.0232); před ním Pardubický (-0.0158) a Moravskoslezský (-0.0141).

## Jak spustit
1) Nainstalujte závislosti (Python 3.10+):  
   `python -m pip install pandas numpy matplotlib scikit-learn scipy geopandas openpyxl`
2) Upravte cesty k datům v notebooku na lokální `DATA/...`.
3) Spusťte `jupyter lab` (nebo nahrajte notebook do Colab) a projděte buňky v `T00276589.ipynb`.

## Poznámky
- Mapová část stahuje GeoJSON (`https://raw.githubusercontent.com/siwekm/czech-geojson/master/kraje.json`); při offline běhu nahraďte lokálním souborem.
- Váhy v indexu lze snadno měnit v sekci výpočtu `INDEX_PAST`/`INDEX_FUTURE`.
