# Ilmanlaadun ennustaminen (harjoitusprojekti Helsingistä)

Tämä on harjoitusprojekti, jonka tavoitteena on tutkia ja kehittää malleja ilmanlaadun ennustamiseksi (Helsingissä).

## Projektin Tavoite

Tämä projekti keskittyy Helsingin kaupunki-ilmanlaadun analysointiin ja erityisesti maanpinnan otsonipitoisuuksien ([O₃]) ennustamiseen. 
Projektissa hyödynnetään FMI:n (Ilmatieteen laitos) avointa dataa Helsingin Kallio 2 (otsoni) ja Kaisaniemi (sää) mittausasemilta aikaväliltä 
2020-2025.

Projektin tavoitteena on tutkia otsonipitoisuuksiin vaikuttavia tekijöitä, erityisesti sääolosuhteita (lämpötila, tuulen nopeus, ilmanpaine), ja 
kokeilla erilaisia aikasarja- ja koneoppimismalleja otsonin tai sen piikkien ennustamiseksi.

## Sisällys

* [Datalähteet](#datalähteet)
* [Projektin rakenne](#projektin-rakenne)
* [Käyttö](#käyttö)
* [Metodologia](#metodologia)
* [Tulokset ja nykytila](#tulokset-ja-nykytila)


## Datalähteet

Projektissa käytetty data on peräisin Ilmatieteen laitoksen avoimen datan rajapinnasta ja kattaa seuraavat asemat ja aikavälin:

1.  **Helsinki Kallio 2:** Ilmanlaadun mittausasema. Tästä datasta käytetään erityisesti `Otsoni [µg/m³]` -sarjaa.
2.  **Helsinki Kaisaniemi:** Säähavaintoasema. Tästä datasta käytetään muuttujia kuten `Lämpötilan keskiarvo [°C]`, `Keskituulen nopeus [m/s]` ja 
`Ilmanpaineen keskiarvo [hPa]`.

Aikaväli molemmille datoille on noin **1.4.2020 - 1.4.2025**.

Raakadata löytyy tämän repositorion `/data/raw/` -kansiosta.

* `Helsinki Kallio 2_ 1.4.2020 - 1.4.2025_... .csv`
* `Helsinki Kaisaniemi_ 1.4.2020 - 1.4.2025_... .csv`


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
├── .gitignore          # Gitille ohjeistetut tiedostot, joita ei seurata
├── README.md           # Tämä tiedosto: projektin kuvaus ja ohjeet
├── data/               # Data (esim. raaka, prosessoitu)
│   ├── raw/            # Alkuperäinen data
│   └── processed/      # Käsitelty data
├── notebooks/          # Jupyter/Databricks notebookit analyyseihin ja mallinnukseen
├── src/                # Uudelleenkäytettävä lähdekoodi (funktiot, luokat)
├── reports/            # Raportit, kuvaajat yms.
│   └── figures/        # Tallennetut kuvaajat
└── requirements.txt    # Projektin Python-riippuvuudet

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

## Metodologia

Projekti noudattaa karkeasti CRISP-DM-metodologian vaiheita:

1.  **Datan ymmärtäminen:** Datalähteisiin tutustuminen ja eksploratiivinen data-analyysi (EDA) otsonin ja säämuuttujien käyttäytymisen 
ymmärtämiseksi (trendi, kausivaihtelu, korrelaatiot).
2.  **Datan valmistelu:** Datan puhdistus, yhdistäminen aikaleiman perusteella, puuttuvien arvojen käsittely, datan uudelleenotanta (`resample`) 
säännölliseen tuntitaajuuteen ja piirteiden muokkaus (viiveistetty data, aika-piirteet).
3.  **Mallinnus:** Eri ennustusmenetelmien kokeilu:
    * **Aikasarjamallit:** ARIMA, SARIMA, SARIMAX (säämuuttujilla).
    * **Koneoppimismallit (Suunnitteilla/Kokeilussa):** Logistinen Regressio (baseline), XGBoost, LightGBM, RNN, LSTM. Tavoitteena erityisesti 
otsonipiikkien ennustaminen (luokittelu).
4.  **Evaluointi:** Mallien suorituskyvyn arviointi käyttäen sopivia metriikoita (RMSE, MAE regressioon; Classification Report, Confusion Matrix, 
ROC AUC luokitteluun).

## Tulokset ja nykytila

* **EDA:** Alustava analyysi osoittaa selvää vuorokausi- ja vuosittaista kausivaihtelua otsonipitoisuuksissa. Korrelaatioita säämuuttujien kanssa 
on havaittu (yksityiskohdat EDA-notebookeissa).
* **Aikasarjamallinnus (SARIMAX):**
    * Datan uudelleenotanta (`resample`) säännölliseen tuntitaajuuteen mahdollisti SARIMAX-mallien ajamisen ilman `NaN`-ennusteita.
    * Kokeillut SARIMAX-mallit (myös säämuuttujilla) tuottivat numeerisia ennusteita, mutta niiden tarkkuus (RMSE/MAE) ei ollut optimaalinen 
verrattuna jopa yksinkertaisempaan SARIMA-malliin ilman resamplea.
    * Mallit eivät onnistuneet ennustamaan otsonipiikkejä (korkeita pitoisuuksia > 90. persentiili).
* **Nykytila:** Projekti on siirtymässä koneoppimismallien kokeiluun (XGBoost, LightGBM, RNN, LSTM) keskittyen erityisesti piikkien ennustamiseen 
luokittelutehtävänä. Aikasarjamallien (SARIMAX) jatkokehitys vaatisi todennäköisesti tarkempaa mallin järjestyksen viritystä ja mahdollisesti eri 
lähestymistapaa datan esikäsittelyyn.


## Tulokset ja nykytila (päivitetty)

* **EDA:** Alustava analyysi osoittaa selvää vuorokausi- ja vuosittaista kausivaihtelua otsonipitoisuuksissa. Korrelaatioita säämuuttujien 
(lämpötila, tuulen nopeus, ilmanpaine) kanssa on havaittu.
* **Aikasarjamallinnus (SARIMAX):**
    * Datan uudelleenotanta (`resample`) säännölliseen tuntitaajuuteen mahdollisti SARIMAX-mallien ajamisen ilman `NaN`-ennusteita.
    * Kokeillut SARIMAX-mallit (myös säämuuttujilla ja eri järjestysluvuilla) tuottivat kuitenkin heikon ennustustarkkuuden (korkea RMSE/MAE) 
verrattuna jopa perus-SARIMA-malliin ilman resamplea.
    * SARIMAX-mallit **eivät onnistuneet ennustamaan otsonipiikkejä** (arvoja > 90. persentiili).
* **Koneoppimismallit (Luokittelu):**
    * Logistinen Regressio (baseline), XGBoost ja LightGBM koulutettiin ennustamaan suoraan piikkejä (`onko_piikki`-muuttuja) käyttäen laajaa 
joukkoa muokattuja piirteitä (viiveet, aika-piirteet, liukuvat tilastot).
    * Sekä **XGBoost että LightGBM suoriutuivat merkittävästi Logistista Regressiota ja SARIMAX-malleja paremmin** piikkien tunnistamisessa. Ne 
saavuttivat korkean Recall-arvon (löysivät n. 89% todellisista piikeistä) ja kohtuullisen Precision-arvon (n. 66-68% piikkiennusteista oli oikeita) 
testidatalla. PR AUC -arvot olivat myös hyviä (~0.90).
    * Tärkeimmiksi piirteiksi molemmissa malleissa nousivat edellisten tuntien otsonipitoisuudet ja niiden vaihtelu (liukuvat keskiarvot/hajonnat), 
mutta myös säämuuttujilla (lämpötila, tuuli, ilmanpaine) ja aika-piirteillä oli vaikutusta.
* **Nykytila:** Gradient boosting -mallit (XGBoost, LightGBM) vaikuttavat lupaavimmilta tähän mennessä kokeilluilta menetelmiltä otsonipiikkien 
ennustamiseen. Seuraavaksi suunnitelmissa on kokeilla rekurrentteja neuroverkkoja (LSTM, RNN) ja mahdollisesti syventyä XGBoost/LightGBM-mallien 
hyperparametrien viritykseen.


## Kontribuutio

Tämä on henkilökohtainen harjoitusprojekti. Ehdotukset ja kommentit ovat tervetulleita, kiitos.

