# Ilmanlaadun Ennustaminen Helsingissä (Harjoitusprojekti)

Tämä on harjoitusprojekti, jonka tavoitteena on tutkia ja kehittää malleja ilmanlaadun ennustamiseksi Helsingissä. Projekti on erillinen 
virallisesta Silo.ai/Ericsson koulutusohjelmasta ja käyttää julkisesti saatavilla olevia tietoja Helsingin alueelta.

## Projektin Tavoite

Kehittää koneoppimismalli ennustamaan tiettyjen ilmansaasteiden (esim. PM2.5, PM10, NO2, O3) pitoisuuksia Helsingissä tulevaisuudessa (esim. 
seuraavan 1-24 tunnin aikana) käyttäen historiallista ilmanlaatu-, sää- ja mahdollisesti liikennedataa.

## Tietolähteet (Esimerkkejä / Tutkittavia)

* **Ilmatieteen laitos (FMI):** Avoimen datan rajapinnat tarjoavat historiallista säädataa ja ilmanlaatuhavaintoja.
    * [FMI Avoin Data](https://ilmatieteenlaitos.fi/avoin-data)
* **Helsingin seudun ympäristöpalvelut (HSY):** Tarjoaa tietoa pääkaupunkiseudun ilmanlaadusta ja mittausasemien dataa.
    * [HSY Ilmanlaatu](https://www.hsy.fi/ilmanlaatu-ja-ilmasto/ilmanlaatu/)
* **Fintraffic / Digitraffic:** Mahdollisesti liikennedataa.
    * [Digitraffic](https://www.digitraffic.fi/)

*(Tarkemmat datan URL-osoitteet ja kuvaukset lisätään myöhemmin)*

## Projektin Rakenne

/ilmanlaatu-ennuste-helsinki/
|
├── .gitignore
├── README.md
├── data/              # Data (raaka, prosessoitu)
├── notebooks/         # Jupyter/Databricks notebookit analyyseihin ja mallinnukseen
├── src/               # Uudelleenkäytettävä lähdekoodi (funktiot, luokat)
├── reports/           # Raportit, kuvaajat
└── requirements.txt   # Projektin riippuvuudet

## Asennus

1.  Kloonaa repositorio:
    ```bash
    git clone <repository-url>
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

## Käyttö

* Tutkimusnotebookit löytyvät `notebooks/`-kansiosta.
* Aloita esimerkiksi `notebooks/01-eda.ipynb`.

## Kontribuutio

Tämä on henkilökohtainen harjoitusprojekti. Ehdotukset ja kommentit ovat tervetulleita issueiden kautta.

