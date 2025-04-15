## Helsingin ilmanlaadun analysointi ja otsonipiikkien ennustaminen

Tämä projekti keskittyy Helsingin kaupunki-ilmanlaadun analysointiin, erityisesti korkeiden maanpinnan otsonipitoisuuksien ([O₃]) eli "otsonipiikkien" ennustamiseen. Tavoitteena on tutkia sääolosuhteiden (lämpötila, tuulen nopeus, 
ilmanpaine) vaikutusta otsonitasoihin ja kehittää sekä arvioida erilaisia aikasarja- ja koneoppimismalleja piikkien ennustamiseksi. Tarkat ennusteet ovat tärkeitä kansanterveydellisten varoitusten antamiseksi ja 
päästövähennysstrategioiden tehokkuuden arvioimiseksi.

Projektissa hyödynnetään Ilmatieteen laitoksen avointa dataa Helsingin Kallio 2 (otsoni) ja Kaisaniemi (sää) mittausasemilta noin aikaväliltä 1.4.2020 - 1.4.2025.

## Sisällys

* [Datalähteet](#datalähteet)
* [Projektin rakenne](#projektin-rakenne)
* [Asennus ja käyttöönotto](#asennus-ja-käyttöönotto)
* [Käyttö](#käyttö)
* [Metodologia](#metodologia)
* [Tulokset ja nykytila](#tulokset-ja-nykytila)
* [Seuraavat askeleet](#seuraavat-askeleet)

---

## Pipeline-pohjainen kehitys (v0.7 asti)

**Päivitys 15.4.2025:** Projektiin on kehitetty yhtenäistetty pipeline-rakenne datan käsittelyyn ja eri ennustemallien kokeiluun ja vertailuun. Tämä parantaa 
työnkulun toistettavuutta ja hallintaa.

**Pipeline-koodi:**

Tähänastiset pipeline-vaiheet ja mallikokeilut löytyvät notebookeista `pipeline_notebooks`-kansiosta:
* **v0.5:** [Peruspipeline (Esikäsittely, EDA, Piirteet, Baseline 
LR)](https://github.com/rrwiren/ilmanlaatu-ennuste-helsinki/blob/main/pipeline_notebooks/PIPELINE_v0.5_ESIK%C3%84SITTELY%2C_EDA_PIIRTEET_BASELINE.ipynb)
* **v0.6:** [XGBoost-malli (Koulutus & Evaluointi, 
FI)](https://github.com/rrwiren/ilmanlaatu-ennuste-helsinki/blob/main/pipeline_notebooks/PIPELINE_v0.6_XGBOOST_fixed_no_es.ipynb)
* **v0.7:** [Prophet-malli (Koulutus & 
Evaluointi)](https://github.com/rrwiren/ilmanlaatu-ennuste-helsinki/blob/main/pipeline_notebooks/PIPELINE_v0.7_PROPHET_TRAINING_fixed_version.ipynb)

Pipeline kattaa tyypillisesti seuraavat vaiheet: data -> esikäsittely -> EDA -> piirteet -> jako -> malli -> evaluointi -> (tallennus).

Tässä on projektin hakemistorakenne, yksinkertaistettu:

```text
ilmanlaatu-ennuste-helsinki/
├── data/
│   ├── processed/  # Käsitelty data (.parquet)
│   └── raw/        # Alkuperäiset ladatut .csv
├── images/         # Tallennetut kuvaajat ja visualisoinnit
├── models/         # Tallennetut koulutetut mallit (.joblib)
├── notebooks/      # Vanhemmat, erilliset kokeilut (voi olla vanhentunutta!)
├── pipeline_notebooks/ # UUDET PIPELINE-NOTEBOOKIT (v0.5 ->) - NYKYINEN KEHITYS
├── .gitignore
└── README.md       # Tämä tiedosto
```


**Tulokset: Mallien Vertailu**
Suorituskykymetriikat (Testisetti)
Tähän mennessä (15.4.2025) testattujen mallien suorituskyky testidatalla (noin Nov 2024 - Huhti 2025).

| Malli                          | MAE    | RMSE   | R²     | Kommentti                                    |
| :----------------------------- | :----- | :----- | :----- | :------------------------------------------- |
| Baseline (LR v0.5, aika)       | 12.804 | 16.872 | 0.331  | Käyttää vain aikaan perustuvia piirteitä.     |
| XGBoost (v0.6, kaikki piirteet)| 8.807  | 10.764 | 0.565  | Merkittävä parannus baselineen.             |
| Prophet (v0.7, ei regressoreita)| 24.935 | 30.108 | -2.402 | Suoriutui erittäin heikosti (vuositt. kausiv. puuttui). |

**Analyysi Tuloksista**
Baseline (Lineaarinen Regressio): Toimii perustasona. Selittää noin 33% otsonin vaihtelusta pelkän ajan avulla.
XGBoost (v0.6): Hyödyntämällä kaikkia piirteitä saavutti merkittävästi paremman suorituskyvyn (R² ≈ 0.57). Piirteiden tärkeysanalyysi (notebook v0.6) nosti esiin vuoden- ja vuorokaudenajat sekä NO2/NO-pitoisuudet.
Prophet (v0.7): Perus-Prophet epäonnistui tällä datalla (negatiivinen R²), todennäköisesti liian lyhyen datahistorian (&lt; 2v) vuoksi, jolloin vuosittainen kausivaihtelu jäi mallintamatta.
Johtopäätös: Tähän mennessä XGBoost on ollut selvästi tarkin malli hyödyntäessään kaikkia saatavilla olevia piirteitä.


Status (15.4.2025): Toimiva pipeline datan esikäsittelystä baseline-, XGBoost- ja Prophet-mallien ajoon on valmis. Käsitelty data ja paras malli (XGBoost v0.6) on tallennettu.

**Kehitysideat:**

(Tärkein) Hanki Lisää Historiallista Dataa: Useamman vuoden (3-5+) data parantaisi mallien luotettavuutta ja mahdollistaisi paremman kausivaihtelun mallintamisen.
Toteuta LSTM-malli: Seuraavaksi kokeillaan LSTM-neuroverkkoa pipeline-rakenteessa (esim. Pipeline_v0.8_LSTM_Training.ipynb).
Paranna Olemassaolevia: Kokeile Prophetia regressoreilla ja viritä XGBoostin hyperparametreja.
Kehitä Piirteitä: Luo lag-piirteitä ja liukuvia keskiarvoja.
Kokeile Muita Malleja: Esim. SARIMA, LightGBM.


**Valmiiksi Käsitelty Data (v0.5):**

Esikäsittelyn, EDA:n ja peruspiirteiden muokkauksen tuloksena syntynyt data on tallennettu ja sitä **suositellaan käytettäväksi mallinnuksen lähtökohtana**:
* **Tiedosto:** `Helsinki_Data_With_Features_Pipeline_v0.5.parquet`
* **Sijainti:** [`data/processed/`](https://github.com/rrwiren/ilmanlaatu-ennuste-helsinki/tree/main/data/processed)
* **Suora linkki:** 
[Helsinki_Data_With_Features_Pipeline_v0.5.parquet](https://github.com/rrwiren/ilmanlaatu-ennuste-helsinki/blob/main/data/processed/Helsinki_Data_With_Features_Pipeline_v0.5.parquet)

Lataus Pandasilla:
```python
import pandas as pd
df = pd.read_parquet("data/processed/Helsinki_Data_With_Features_Pipeline_v0.5.parquet")

```




## Pipeline-lähestymistapa (Versio 0.5)

**Päivitys 12.4.2025:** Projektiin on luotu uusi, yhtenäistetty pipeline-rakenne (versio 0.5) datan käsittelyyn ja perusmallinnukseen. Tämä parantaa työnkulun toistettavuutta ja hallintaa.

**Pipeline-koodi:**

Viimeisin versio (v0.5) pipeline-skriptistä/notebookista löytyy `pipeline_notebooks`-kansiosta:

* [PIPELINE_v0.5_ESIKÄSITTELY, EDA_PIIRTEET_BASELINE.ipynb](https://github.com/rrwiren/ilmanlaatu-ennuste-helsinki/blob/main/pipeline_notebooks/PIPELINE_v0.5_ESIK%C3%84SITTELY%2C_EDA_PIIRTEET_BASELINE.ipynb)

Tämä pipeline kattaa seuraavat vaiheet:
1.  Raakadatan lataus (FMI Kaisaniemi sää & Kallio 2 ilmanlaatu).
2.  Esikäsittely ja yhdistäminen.
3.  EDA (sis. visualisoinnit, korrelaatiot, siivous).
4.  Aikaan perustuvien piirteiden luonti (lineaariset & sykliset).
5.  Datan kronologinen jako opetus-/testijoukkoihin.
6.  Baseline-mallin (Lineaarinen Regressio aika-piirteillä) koulutus & evaluointi.

**Valmiiksi Käsitelty Data:**

Pipeline (v0.5) tuottaa Parquet-tiedoston, joka sisältää täysin käsitellyn, siivotun ja piirteillä täydennetyn tuntitason datan (noin huhtikuu 2023 - huhtikuu 2025).

* **Tiedosto:** `Helsinki_Data_With_Features_Pipeline_v0.5.parquet`
* **Sijainti:** [`data/processed/`](https://github.com/rrwiren/ilmanlaatu-ennuste-helsinki/tree/main/data/processed)
* **Suora linkki tiedostoon:** [Helsinki_Data_With_Features_Pipeline_v0.5.parquet](https://github.com/rrwiren/ilmanlaatu-ennuste-helsinki/blob/main/data/processed/Helsinki_Data_With_Features_Pipeline_v0.5.parquet) (Huom: GitHub 
näyttää vain tiedoston tiedot, lataus vaatii muita työkaluja tai "Download"-napin käyttöä sivulla)

**Suositus jatkotyöskentelyyn:**

On **vahvasti suositeltavaa** käyttää yllä mainittua `.parquet`-tiedostoa lähtökohtana kaikessa jatkomallinnuksessa (esim. XGBoost, LSTM). Tämä nopeuttaa työtä merkittävästi.

Voit ladata datan Pandasilla esimerkiksi näin:

```python
import pandas as pd

parquet_path = "data/processed/Helsinki_Data_With_Features_Pipeline_v0.5.parquet"
# Varmista, että ajat koodin repositorion juuresta tai muokkaa polkua tarvittaessa
try:
    df = pd.read_parquet(parquet_path)
    print("Data ladattu onnistuneesti!")
    print(df.info())
except FileNotFoundError:
    print(f"Virhe: Tiedostoa ei löytynyt polusta '{parquet_path}'. Tarkista polku.")

```

## Päivitys 10.4.2025: Mallien vertailu ja Luokittelukokeilu

Tänään keskityttiin eri mallien testaamiseen ja GRU-mallin parantamiseen korkeiden otsonipitoisuuksien ennustamiseksi.

**1. Datan Päivitys:**
* Otettiin käyttöön uudempi data ajanjaksolta 1.4.2024 - 1.4.2025.
* Dataan lisättiin **Pilvisyys (okta)** -ominaisuus.
* Uusi käsitelty tiedosto: `data/processed/processed_Helsinki_O3_Weather_Cloudiness_2024_2025_v3.parquet`.
* Esikäsittelyskripti: `notebooks/Colab_Script_Datan_Esikäsittely_ja_Tallennus.ipynb` (tai päivitetty versio).

**2. Kokeillut Mallit:**
* **ARIMA(1,0,0):** Parempi kuin baseline (RMSE ~15.4), mutta ei tunnistanut yhtään korkeaa jaksoa (Recall=0 @ 85µg/m³). Ennusteet tuottivat NaN-arvoja `.get_forecast()`-metodilla, mutta `.predict()` toimi.
* **SARIMAX(1,0,1)x(1,1,1,24):** Tilastollisesti hyvä sovite harjoitusdataan (matala AIC/BIC, hyvät residuaalit), mutta testijakson yleistarkkuus heikompi kuin ARIMA(1,0,0) (RMSE ~19.7). Ei tunnistanut yhtään korkeaa jaksoa 
(Recall=0 @ 85µg/m³). Käytti `.predict()`-metodia.
* **GRU/LSTM (Regressio, perus FE):** GRU hieman LSTM:ää parempi yleistarkkuudessa (RMSE ~13.6). GRU tunnisti muutaman korkean jakson (Recall ~6.5% @ 85µg/m³), LSTM ei yhtään. Ennusteet melko sileitä.
* **GRU (Luokittelu, Laajennettu FE):** Ongelma muotoiltiin binääriluokitteluksi (ennustaako max 8h ka > 85 µg/m³ seuraavan 24h aikana). Käytettiin laajennettua ominaisuusmuokkausta (sykliset aika/tuuli, viiveet, liukuvat tilastot 
-> 44 ominaisuutta) ja luokkaepätasapainon painotusta.
    * **Paras Tulos:** Saavutettiin **Recall = 1.00 (100%)** ja **Precision = 0.50 (50%)** käyttämällä päätöskynnystä 0.95. Malli siis löysi kaikki testijakson ylitykset, mutta puolet sen antamista hälytyksistä oli vääriä. AUC ROC 
oli erinomainen (0.998).
    * **Notebook:** `notebooks/GRU_2025-04-11_0001.ipynb`

**3. Johtopäätökset:**
* Luokittelumalli GRU:lla ja laajennetuilla ominaisuuksilla on tähän mennessä lupaavin lähestymistapa varoitustavoitteen kannalta, saavuttaen täydellisen herkkyyden (Recall) 85 µg/m³ rajalle.
* Haasteena on edelleen väärien hälytysten vähentäminen (Precisionin nostaminen).
* Pelkät tilastolliset mallit (ARIMA/SARIMAX testatuilla asetuksilla) eivät tunnistaneet korkeita jaksoja.
* Laajennettu ominaisuusmuokkaus vaikutti positiivisesti Recalliin neuroverkolla, mutta heikensi yleistä RMSE/MAE-tarkkuutta regressiossa.

**4. Seuraavat Askeleet (Ehdotuksia):**
* **Paranna GRU-luokittelijaa:** Kokeile hyperparametrien viritystä tai ominaisuuksien karsintaa Precisionin parantamiseksi.
* **Kokeile LSTM:** Aja LSTM-malli samalla luokitteluasetelmalla ja laajennetuilla ominaisuuksilla vertailun vuoksi.
* **Testaa 120 µg/m³ raja:** Yritä kouluttaa luokittelija viralliselle raja-arvolle (tiedostaen äärimmäisen epätasapainon).
* **XGBoost/LightGBM:** Kokeile puupohjaisia malleja laajennetuilla ominaisuuksilla.
* **Sääennusteet:** Integroi *tulevaisuuden* sääennusteiden käyttö varsinaiseen ennusteprosessiin.
* **Tukholman Data:** Ota käyttöön ja sovella opittuja menetelmiä lopulliseen kohdedataan.

---




## Datalähteet

Projektissa käytetty data on peräisin Ilmatieteen laitoksen avoimen datan rajapinnasta ja ladattu tässä repositoriossa olevaan `/data/raw/`-kansioon.

1.  **Helsinki Kallio 2 (Otsoni):** Ilmanlaadun mittausaseman data (`Helsinki Kallio 2_... .csv`). Tästä datasta käytetään pääasiassa `Otsoni [µg/m³]` -aikasarjaa.
2.  **Helsinki Kaisaniemi (Sää):** Säähavaintoaseman data (`Helsinki Kaisaniemi_... .csv`). Tästä datasta käytetään ennustavia muuttujia: `Lämpötilan keskiarvo [°C]`, `Keskituulen nopeus [m/s]`, `Ilmanpaineen keskiarvo [hPa]` ja 
`Tuulen suunnan keskiarvo [°]`.

Aikaväli molemmille datoille on noin **1.4.2020 - 1.4.2025**.

Tähän asti käytetty data:

* `Helsinki Kallio 2_ 1.4.2020 - 1.4.2025_... .csv`
* `Helsinki Kaisaniemi_ 1.4.2020 - 1.4.2025_... .csv`


Löytyy dataa myös pidemmältä ajalta:

* `Helsinki Kallio 2_ 1.1.2013 - 31.12.2024_... .csv`  
* `Helsinki Kaisaniemi_ 1.1.2013 - 31.12..2024_... .csv`


## Tietolähteet (Esimerkkejä / Tutkittavia)

* **Ilmatieteen laitos (FMI):** Avoimen datan rajapinnat tarjoavat historiallista säädataa ja ilmanlaatuhavaintoja.
    * [FMI Avoin Data](https://ilmatieteenlaitos.fi/avoin-data)
	* [FMI havaintojen lataus](https://www.ilmatieteenlaitos.fi/havaintojen-lataus)


* **Helsingin seudun ympäristöpalvelut (HSY):** Tarjoaa tietoa pääkaupunkiseudun ilmanlaadusta ja mittausasemien dataa.
    * [HSY Ilmanlaatu](https://www.hsy.fi/ilmanlaatu-ja-ilmasto/ilmanlaatu/)
      * [Ilmanlaatu PK-seudulla](https://www.hsy.fi/ilmanlaatu-ja-ilmasto/ilmanlaatu-paakaupunkiseutu/ilmansaasteiden-pitoisuudet/)
	* [HSY avoin data](https://www.hsy.fi/ymparistotieto/avoindata/avoin-data---sivut/paakaupunkiseudun-ilmansaastepitoisuudet/)

* **Fintraffic / Digitraffic:** Mahdollisesti liikennedataa.
    * [Digitraffic](https://www.digitraffic.fi/)

*(Tarkemmat datan URL-osoitteet ja kuvaukset lisätään projektin edetessä.)*

## Projektin Rakenne

Tässä on projektin hakemistorakenne:

```text
/ilmanlaatu-ennuste-helsinki/
│
├── data/
│   ├── raw/              # Alkuperäiset raakadatatiedostot (CSV, XLSX)
│   └── processed/        # (Valinnainen) Puhdistetut, yhdistetyt, resamplatut datat
│
├── images/               # Tallennetut visualisointikuvat README:tä varten
│   ├── model_comparison_bars.png
│   ├── xgboost_tuned_pr_curve.png
│   └── xgboost_tuned_feature_importance.png
│
├── notebooks/            # Jupyter/Colab Notebookit analyysia ja mallinnusta varten
│   ├── EDA_Kallio_Otsoni.ipynb  	# Uudelleen nimeäminen vielä kesken
│   ├── EDA_Kaisaniemi_Sää.yn  b	# Uudelleen nimeäminen vielä kesken
│   ├── Mallinnus_ARIMA_SARIMA.i  pynb# Uudelleen nimeäminen vielä kesken
│   ├── Mallinnus_SARIMAX.ipynb    	# Uudelleen nimeäminen vielä kesken
│   ├── Mallinnus_LogReg_XGB_LGBM_Vertailu.ipynb # Yhdistetty vertailuajo
│   ├── Mallinnus_XGBoost_Viritys.ipynb       # XGBoostin viritys
│   └── ...(Tulevat LSTM/RNN-mallit)...
│
├── scripts/              # (Valinnainen) Python-skriptit
│
├── requirements.txt      # Projektin vaatimat Python-kirjastot
└── README.md             # Tämä tiedosto

```

## Asennus

1.  Kloonaa repositorio:
    ```bash
    git clone https://github.com/rrwiren/ilmanlaatu-ennuste-helsinki
    cd ilmanlaatu-ennuste-helsinki
    ```
2.  (Suositus) Luo virtuaaliympäristö:
    ```bash
    python -m venv venv
    source venv/bin/activate  # Linux/macOS
    # venv\Scripts\activate  # Windows
    ```
3.  Asenna riippuvuudet:
    ```bash
    pip install -r requirements.txt
    ```

**Tärkeimmät kirjastot (lisää `requirements.txt`-tiedostoon):**

* pandas
* numpy
* requests
* matplotlib
* seaborn
* statsmodels
* scikit-learn
* xgboost
* lightgbm
* *(Lisää tensorflow/pytorch, jos/kun käytät LSTM/RNN-malleja)*

*(Luo `requirements.txt`-tiedosto komennolla `pip freeze > requirements.txt` kun olet asentanut tarvittavat kirjastot)*

## Käyttö

Analyysi ja mallinnus on tehty pääasiassa Jupyter Notebookeissa (`/notebooks`-kansio).

1.  Avaa haluamasi notebook (esim. ` .ipynb`) Jupyter Notebookissa, JupyterLabissa tai Google Colabissa.
2.  Suorita solut järjestyksessä. Notebookit sisältävät datan latauksen, esikäsittelyn, analyysin, mallinnuksen ja visualisoinnin vaiheet.


## Asennus ja käyttöönotto

Projekti käyttää Python 3 -versiota ja useita datatieteen kirjastoja. Suositeltava tapa on luoda virtuaaliympäristö.

1.  **Kloonaa repositorio:**
    ```bash
    git clone [https://github.com/rrwiren/ilmanlaatu-ennuste-helsinki.git](https://github.com/rrwiren/ilmanlaatu-ennuste-helsinki.git)
    cd ilmanlaatu-ennuste-helsinki
    ```
2.  **(Suositus) Luo ja aktivoi virtuaaliympäristö:**
    ```bash
    python3 -m venv venv
    source venv/bin/activate  # Linux/macOS
    # venv\Scripts\activate  # Windows PowerShell
    # .\venv\Scripts\activate.bat # Windows Cmd
    ```
3.  **Asenna vaaditut kirjastot:**
    ```bash
    pip install -r requirements.txt
    ```

**Tärkeimmät kirjastot (`requirements.txt`):**

* pandas
* numpy
* requests
* matplotlib
* seaborn
* statsmodels
* scikit-learn
* xgboost
* lightgbm
* *(Lisää tensorflow/pytorch, jos/kun käytät LSTM/RNN-malleja)*

*(Muista luoda/päivittää `requirements.txt`-tiedosto: `pip freeze > requirements.txt`)*

## Käyttö

Analyysi ja mallinnus on toteutettu Jupyter Notebookeissa (`/notebooks`-kansio).

1.  Käynnistä Jupyter Notebook tai JupyterLab aktiivisessa virtuaaliympäristössäsi tai avaa notebookit Google Colaboratoryssa.
2.  Avaa haluamasi notebook (esim. `Mallinnus_LogReg_XGB_LGBM_Vertailu.ipynb`).
3.  Suorita solut järjestyksessä. Notebookit sisältävät vaiheet datan latauksesta mallien evaluointiin ja visualisointiin.

## Metodologia

Projekti etenee karkeasti CRISP-DM-mallin mukaisesti:

1.  **Datan ymmärtäminen:** Tutustuminen Kallion otsoni- ja Kaisaniemen säädataan. Eksploratiivinen data-analyysi (EDA) trendien, kausivaihteluiden, jakaumien ja muuttujien välisten korrelaatioiden tunnistamiseksi.
2.  **Datan valmistelu:**
    * Tiedostojen lukeminen ja peruspuhdistus (oikeat koodaukset, desimaalierottimet).
    * Aikaleimojen luonti ja asettaminen indeksiksi.
    * Otsoni- ja säädatan yhdistäminen aikaleiman perusteella (`inner join`).
    * Datan uudelleenotanta (`resample`) säännölliseen tuntitaajuuteen ('h') ja syntyneiden aukkojen täyttäminen interpoloimalla (`interpolate(method='time')`).
    * Otsonipiikkien määrittely binääriseksi kohdemuuttujaksi (`onko_piikki`) käyttäen 90. persentiilin kynnysarvoa.
    * Laajan piirrejoukon luonti (Feature Engineering):
        * Aika-piirteet (tunti, viikonpäivä, kuukausi, vuodenpäivä) syklisesti koodattuna (sin/cos).
        * Viiveistetyt arvot (lags 1-72h) otsonille ja säämuuttujille.
        * Liukuvat tilastot (keskiarvo, keskihajonta) otsonille ja osittain säämuuttujille (3-48h ikkunat).
        * Yhteisvaikutuspiirteet (esim. lämpötila * kellonaika).
    * Piirteiden nimien puhdistus (erikoismerkkien poisto).
    * Datan jako opetus- ja testijoukkoihin ajallisesti (viimeiset 15% testaukseen).
    * Piirteiden skaalaus (`StandardScaler`).
3.  **Mallinnus:** Eri mallien kokeilu piikkien ennustamiseen:
    * **Aikasarjamallit:** ARIMA, SARIMA, SARIMAX (osoittautuivat haastaviksi piikkien ennustamisessa tällä datalla/esikäsittelyllä).
    * **Koneoppimismallit (Luokittelu):**
        * Logistinen Regressio (perusmalli).
        * XGBoost (oletusparametreilla ja hyperparametreilla viritettynä).
        * LightGBM (oletusparametreilla).
        * **Suunnitteilla:** LSTM, RNN.
4.  **Evaluointi:** Luokittelumallien arviointi käyttäen:
    * Classification Report (Precision, Recall, F1-score erityisesti piikki-luokalle).
    * Confusion Matrix.
    * ROC AUC.
    * Precision-Recall AUC (erityisen relevantti epätasapainoiselle datalle).

## Tulokset ja nykytila

### Tulosten vertailu: Oletus vs. Viritetty XGBoost

Hyperparametrien viritys ajoi onnistuneesti läpi (~13 min Colabissa). Alla oleva taulukko vertaa viritetyn XGBoost-mallin (`v9`) suorituskykyä aiempaan oletusparametreilla ajettuun versioon (`v4`) testidatalla:

| Metriikka           | XGBoost (Oletus, v4) | XGBoost (Viritetty, v9) | Muutos          | Tulkinta                                                                 |
| :------------------ | :------------------- | :---------------------- | :-------------- | :----------------------------------------------------------------------- |
| Accuracy            | 0.9607               | 0.9720                  | +0.0113         | Kokonaistarkkuus parani hieman.                                          |
| **Precision (Spike)** | 0.6628               | **0.7886** | **+0.1258** | **Merkittävä parannus!** Viritetyn mallin piikkiennusteet ovat paljon useammin oikeita. |
| **Recall (Spike)** | **0.8862** | 0.8162                  | **-0.0700** | Lasku. Viritetty malli löytää vähemmän kaikista todellisista piikeistä.     |
| **F1-score (Spike)** | 0.7584               | **0.8022** | **+0.0438** | **Selvä parannus.** Precisionin nousu kompensoi Recallin laskun.           |
| ROC AUC             | 0.9881               | 0.9881                  | 0.0000          | Yleinen erottelukyky pysyi erinomaisena.                                 |
| PR AUC              | **0.8998** | 0.8977                  | -0.0021         | Pieni lasku, käytännössä sama. Kertoo hyvästä suorituskyvystä epätasapainossa. |

**Johtopäätökset parannuksista:**

* **Onnistunut viritys:** Hyperparametrien viritys oli ehdottomasti hyödyllistä!
* **Precisionin merkittävä kasvu:** Suurin parannus nähtiin Precisionissa piikeille. Tämä tarkoittaa, että kun viritetty malli ennustaa piikin, voit olla huomattavasti varmempi sen oikeellisuudesta kuin oletusmallilla.
* **Recallin kustannuksella:** Parantunut Precision tuli kuitenkin Recallin kustannuksella. Malli on nyt "varovaisempi" ennustamaan piikkejä, joten se löytää niistä hieman pienemmän osan kuin aiemmin.
* **Parempi tasapaino (F1):** F1-score, joka mittaa Precisionin ja Recallin tasapainoa, parani selvästi. Tämä viittaa siihen, että kokonaisuutena viritetty malli on parempi kompromissi piikkien tunnistamisessa.
* **PR AUC:** Pieni lasku PR AUC:ssa voi johtua siitä, että optimoimme F1-scorea ristiinvalidioinnissa, ja se painotti Precisionia hieman enemmän Recallin kustannuksella tässä tapauksessa.

---

* **EDA:** Tunnistettu selkeät vuorokausi- ja vuosittaiset syklit otsonipitoisuuksissa. Säämuuttujilla havaittu odotettuja korrelaatioita.
* **SARIMAX:** Vaikka datan uudelleenotanta mahdollisti mallien ajamisen ilman NaN-ennusteita, niiden ennustustarkkuus jäi heikoksi, eivätkä ne tunnistaneet piikkejä.
* **ML-luokittelijat (Yhteenveto):**
    * **Gradient Boosting -mallit (XGBoost, LightGBM) suoriutuivat selvästi parhaiten** piikkien ennustamisessa verrattuna Logistiseen Regressioon ja SARIMAX-malleihin.
    * **Hyperparametreilla viritetty XGBoost** antoi parhaan F1-scoren ja Precisionin piikeille, mutta hieman matalamman Recallin kuin oletusmallit.
    * **Kaikkien testattujen ML-mallien vertailutaulukko (Testidata):**

        | Malli                  |   Accuracy |   Precision (Spike) |   Recall (Spike) |   F1-score (Spike) |   ROC AUC |   PR AUC |
        |:-----------------------|-----------:|--------------------:|-----------------:|-------------------:|----------:|---------:|
        | **XGBoost (Tuned)** | **0.9720** |          **0.7886** |           0.8162 |         **0.8022** |    0.9881 |   0.8977 |
        | LightGBM (Default)     |     0.9631 |              0.6807 |           0.8862 |             0.77   |    0.9881 |   0.8975 |
        | XGBoost (Default)      |     0.9607 |              0.6628 |           0.8862 |             0.7584 |    0.9881 |   0.8998 |
        | Logistic Regression    |     0.9470 |              0.5726 |           0.9409 |             0.7119 |    0.9877 |   0.8849 |

    * **Visualisointeja:**

        * **Mallien vertailu (pylväskaavio, Default XGBoost):**
          ![Mallien vertailu (avainmetriikat)](images/model_comparison_bars.png)

        * **Precision-Recall -käyrä (Viritetty XGBoost):**
          ![Viritetty XGBoost Precision-Recall Curve](images/xgboost_tuned_pr_curve.png)

        * **Tärkeimmät piirteet (Viritetty XGBoost):**
          ![Viritetty XGBoost Feature Importance](images/xgboost_tuned_feature_importance.png)

* **Nykytila:** Viritetty XGBoost on tähän mennessä paras malli otsonipiikkien ennustamiseen tässä projektissa. Laaja piirrejoukko, joka sisältää viiveistettyjä arvoja, aika-piirteitä ja liukuvia tilastoja, osoittautui 
hyödylliseksi. Erityisesti edellisten tuntien otsonipitoisuudet ja niiden vaihtelu ovat tärkeitä ennustajia.

## Seuraavat askeleet

* **LSTM/RNN-mallien kokeilu:** Tutkia, pystyvätkö rekurrentit neuroverkot oppimaan aikariippuvuuksia eri tavalla ja saavuttamaan parempia tuloksia.
* **LightGBM:n viritys:** Voisiko LightGBM:n suorituskykyä parantaa hyperparametrien virityksellä XGBoostin tasolle tai jopa paremmaksi?
* **Piirteiden valinta/karsinta:** Analysoida tarkemmin piirteiden tärkeyttä ja kokeilla poistaa vähemmän tärkeitä piirteitä mallien yksinkertaistamiseksi.
* **Luokittelukynnysarvon optimointi:** Säätää todennäköisyyskynnystä parhaan Precision/Recall-tasapainon löytämiseksi viritetylle XGBoost-mallille.



## Kontribuutio

Tämä on henkilökohtainen harjoitusprojekti. Ehdotukset ja kommentit ovat tervetulleita, kiitos.

