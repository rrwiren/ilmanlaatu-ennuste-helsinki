# Ilmanlaadun Ennustaminen Helsingissä (harjoitusprojekti)

Tämä on harjoitusprojekti, jonka tavoitteena on tutkia ja kehittää malleja ilmanlaadun ennustamiseksi Helsingissä.

## Projektin Tavoite

Kehittää koneoppimismalli ennustamaan tiettyjen ilmansaasteiden (esim. PM2.5, PM10, NO2, O3) pitoisuuksia Helsingissä tulevaisuudessa (esim. 
seuraavan 1-24 tunnin aikana) käyttäen historiallista ilmanlaatu-, sää- ja mahdollisesti liikennedataa. (tällä hetkellä vain FMI dataa..)

## Tietolähteet (Esimerkkejä / Tutkittavia)

* **Ilmatieteen laitos (FMI):** Avoimen datan rajapinnat tarjoavat historiallista säädataa ja ilmanlaatuhavaintoja.
    * [FMI Avoin Data](https://ilmatieteenlaitos.fi/avoin-data)
	* [FMI havaintojen lataus](https://www.ilmatieteenlaitos.fi/havaintojen-lataus)

* Ladattuna dataa tällä hetkellä **data/raw** kansiossa Helsingin Kaisaniemestä ajalta 1.4.2000 - 1.4.2025

* **Helsingin seudun ympäristöpalvelut (HSY):** Tarjoaa tietoa pääkaupunkiseudun ilmanlaadusta ja mittausasemien dataa.
    * [HSY Ilmanlaatu](https://www.hsy.fi/ilmanlaatu-ja-ilmasto/ilmanlaatu/)
      * [Ilmanlaatu PK-seudulla](https://www.hsy.fi/ilmanlaatu-ja-ilmasto/ilmanlaatu-paakaupunkiseutu/ilmansaasteiden-pitoisuudet/)
	* [HSY avoin data](https://www.hsy.fi/ymparistotieto/avoindata/avoin-data---sivut/paakaupunkiseudun-ilmansaastepitoisuudet/)

* **Fintraffic / Digitraffic:** Mahdollisesti liikennedataa.
    * [Digitraffic](https://www.digitraffic.fi/)

*(Tarkemmat datan URL-osoitteet ja kuvaukset lisätään myöhemmin)*

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

## Käyttö

* Tutkimusnotebookit löytyvät `notebooks/`-kansiosta.

## Kontribuutio

Tämä on henkilökohtainen harjoitusprojekti. Ehdotukset ja kommentit ovat tervetulleita, kiitos.

