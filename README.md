![UBMATF](https://img.shields.io/badge/UBMATF-Astrostatistics_2026-blue)

# Fotometrijska klasifikacija TDE metodom Random Forest

## Opis projekta

U ovom radu pravimo model masinskog ucenja koji fotometrijski identifikuje Tidal Distruption Events (TDE) trenirajuci nad simuliranim MALLORN (Many Artificial LSST Lightcurves based on Observations of Real Nuclear transients) podacima, nastalih na osnovu pravih https://www.ztf.caltech.edu/ posmatarnja. 

Kvalitet modela ocenjuje se $F_1$ skorom. On je definisan kao

#### $F_1 = \frac{2 \ Precision  \ * \ Recall}{Precision \ + \ Recall}$, $\quad$ $Precision = \frac{TP}{TP + FP}$, $\quad$  $Recall = \frac{TP}{TP + FN}$

gde su TP, FP i FN, respektivno, broj istinito pozitivnih, lazno pozitivnih i lazno negativnih.

F1 rezultat je poželjniji od jednostavne tačnosti u ovom zadatku jer je skup podataka veoma neuravnotežen, pri čemu su TDE znatno ređi od drugih klasa. Ova metrika pruža uravnoteženu meru učinka, nagrađujući modele koji postižu dobar kompromis između recall-a (detektovanja što je moguće više tačnih TDE) i precision-a (izbegavanja prekomernih lažno pozitivnih rezultata).


## Struktura repozitorijuma
```
├── projekat_notebook.ipynb   # Glavni Jupyter notebook — pokreće se od početka do kraja
├── requirements.txt          # Spisak potrebnih Python paketa za pokretanje koda
├── .gitignore                # Fajl koji definiše šta Git ignoriše (sirovi TNG podaci, venv)
├── README.md                 # Dokumentacija projekta (ovaj fajl)
└── data/
    └── mw_analogs.csv        # Pre-procesirani uzorak podataka (1703 MW analoga izvučenih iz TNG100-1)
```
## Uputstvo za pokretanje

Notebook je u potpunosti automatizovan. Prilikom pokretanja nisu potrebne nikakve ručne intervencije, unošenje putanja niti preuzimanje dodatnih paketa unutar samih ćelija.

### Korak 1: Kloniranje repozitorijuma
Otvorite Vaš terminal i klonirajte projekat sledećom komandom:
git clone https://github.com/arekysa/Galakticka-nastanjivost-i-procena-broja-terestrijalnih-planeta-u-analozima-Mlecnog-puta.git
cd Galakticka-nastanjivost-i-procena-broja-terestrijalnih-planeta-u-analozima-Mlecnog-puta

### Korak 2: Instalacija potrebnih paketa
Instalirajte sve zavisnosti navedene u konfiguracionom fajlu unutar Vašeg radnog okruženja:
pip install -r requirements.txt

### Korak 3: Pokretanje Jupyter Notebook-a
Pokrenite Jupyter okruženje, otvorite fajl projekat_notebook.ipynb i izvršite komandu:
Kernel → Restart & Run All

*Napomena za ocenjivanje:* Unutar notebook-a implementirana je automatska provera postojanja sirovih simulacionih podataka na putanji data/TNG100-1. Ukoliko ti podaci nisu lokalno dostupni (što je podrazumevano na računaru ispitivača), kod bez prekida i grešaka prebacuje fokus na učitavanje pre-procesiranog uzorka iz fajla data/mw_analogs.csv. Time je omogućena trenutna reprodukcija svih grafikona, statističkih testova i rezultata.

## Podaci

Uzorak podataka u priloženom CSV fajlu generisan je filtriranjem i obradom javno dostupnog [IllustrisTNG TNG100-1](https://www.tng-project.org/) subhalo kataloga (snapshot=99, crveni pomak z=0). Selekcija je izvršena tako da zadovolji opseg zvezdanih masa Mlečnog puta (10^10.4 - 10^11.0 M☉).

Fajl data/mw_analogs.csv sadrži sledeće atribute za svaku od 1703 selektovane galaksije:

| Kolona | Opis atributa | Jedinica |
|---|---|---|
| SubhaloID | Jedinstveni identifikator subhaloa unutar TNG100 simulacije | — |
| StellarMass | Ukupna zvezdana masa galaksije | M☉ |
| GasMass | Ukupna masa gasne komponente unutar galaksije | M☉ |
| SFR | Trenutna brzina formiranja zvezda (Star Formation Rate) | M☉/yr |
| GasMetallicity | Metaličnost gasne komponente (Z_G) | — |
| StarMetallicity | Metaličnost zvezdane komponente (Z_*) | — |

## Opciono: Preuzimanje sirovih podataka



## Zavisnosti i automatsko testiranje

Projekat se oslanja na standardne naučne biblioteke za analizu podataka (numpy, pandas, scipy) i vizuelizaciju (matplotlib). 

U skladu sa praksama kursa, provera izvršavanja i validacija stila koda na repozitorijumu vrši se pokretanjem sledećih komandi:

# Provera izvršavanja kompletnog notebook-a od prve do poslednje ćelije
pytest --nbmake --nbmake-timeout=60 projekat_notebook.ipynb

# Statička analiza i provera stila koda unutar ćelija notebook-a
nbqa pylint projekat_notebook.ipynb
