![UBMATF](https://img.shields.io/badge/UBMATF-Astrostatistics_2026-blue)

# Fotometrijska klasifikacija TDE metodom Random Forest

## Opis projekta

U ovom radu pravimo model masinskog učenja koji fotometrijski identifikuje Tidal Distruption Events (TDE) trenirajuci nad simuliranim MALLORN (Many Artificial LSST Lightcurves based on Observations of Real Nuclear transients) podacima, nastalih na osnovu pravih [Zwicky Transient Facility (ZTF)](https://www.ztf.caltech.edu/) posmatarnja. 

Kvalitet modela ocenjuje se $F_1$ skorom. On je definisan kao

#### $F_1 = \frac{2 \ Precision  \ * \ Recall}{Precision \ + \ Recall}$, $\quad$ $Precision = \frac{TP}{TP + FP}$, $\quad$  $Recall = \frac{TP}{TP + FN}$

gde su TP, FP i FN, respektivno, broj istinito pozitivnih, lažno pozitivnih i lažno negativnih.

F1 rezultat je poželjniji od jednostavne tačnosti u ovom zadatku jer je skup podataka veoma neuravnotežen, pri čemu su TDE znatno ređi od drugih klasa. Ova metrika pruža uravnoteženu meru učinka, nagrađujući modele koji postižu dobar kompromis između recall-a (detektovanja što je moguće više tačnih TDE) i precision-a (izbegavanja prekomernih lažno pozitivnih rezultata).

Dobijeni rezultat: Uspešnost klasifikatora procenjena je pomoću petostruke stratifikovane unakrsne validacije. Dobijen je prosečan F1 skor od približno 0.395, uz standardnu devijaciju od približno 0.056. Rezultati pokazuju da je moguće razlikovati TDE od ostalih klasa koristeći samo informacije izvedene iz fotometrijskih posmatranja, ali i da klasifikacija predstavlja težak problem, između ostalog zbog izrazite neuravnoteženosti skupa podataka, u kojem TDE objekti čine mali deo ukupnog broja objekata.


## Struktura repozitorijuma
```
├── Notebook.ipynb            # Glavni Jupyter notebook — pokreće se od početka do kraja
├── requirements.txt          # Spisak potrebnih Python paketa za pokretanje koda
├── .gitignore                # Fajl koji definiše šta Git ignoriše 
├── README.md                 # Opis i dokumentacija projekta (ovaj fajl)
└── data/
    └── split_[1-20]/                       # train i test lightcurve fajlovi - pojedinačna fotometrijska merenja objekata
        └── test_full_lightcurves.csv
        └── train_full_lightcurves.csv
    └── test_log.csv                        
    └── train_log.csv                      # sadrži po jedan red za svaki objekat: dodatne informacije o njemu i target value 0 ili 1
```
## Uputstvo za pokretanje

Notebook je u potpunosti automatizovan. Prilikom pokretanja nisu potrebne nikakve ručne intervencije, unošenje putanja niti preuzimanje dodatnih paketa unutar samih ćelija.

### Korak 1: Kloniranje repozitorijuma
Otvorite Vaš terminal i klonirajte projekat sledećom komandom:  
git clone https://github.com/sofijamath-pixel/Astrostatistika---MALLORN  
cd Astrostatistika---MALLORN

### Korak 2: Instalacija potrebnih paketa
Instalirajte sve zavisnosti navedene u konfiguracionom fajlu unutar Vašeg radnog okruženja komandom:  
pip install -r requirements.txt

### Korak 3: Pokretanje Jupyter Notebook-a
Notebook otvorite komandom:  
jupyter notebook Notebook.ipynb
Unutar notebooka izvršite komandu:
Kernel → Restart & Run All

## Podaci

Podaci korišćeni u ovom projektu potiču iz javno dostupnog skupa podataka [MALLORN Astronomical Classification Challenge](https://www.kaggle.com/competitions/mallorn-astronomical-classification-challenge), namenjenog fotometrijskoj klasifikaciji astronomskih tranzijenata. Skup sadrži simulirana fotometrijska posmatranja u šest LSST filtera (*u, g, r, i, z, y*), zajedno sa informacijama o tipu posmatranog objekta.

U analizi su korišćena ukupno **3.043 astronomska objekta**, od kojih je **148 klasifikovano kao TDE**, dok preostalih **2.895 objekata** pripada drugim klasama tranzijenata i promenljivih izvora. Cilj projekta je binarna klasifikacija objekata na **TDE** (`target = 1`) i **non-TDE** (`target = 0`).

Podaci su organizovani u dve osnovne vrste CSV fajlova:

### Lightcurve fajlovi

`*_full_lightcurves.csv` fajlovi sadrže pojedinačna fotometrijska merenja svetlosnih krivih. Za svaki objekat postoji više merenja u različitim trenucima i fotometrijskim filterima.

| Kolona       | Opis atributa                                       | Jedinica |
| ------------ | --------------------------------------------------- | -------- |
| `object_id`  | Jedinstveni identifikator astronomskog objekta      | —        |
| `Time (MJD)` | Vreme posmatranja izraženo kao Modified Julian Date | dan      |
| `Flux`       | Izmereni fluks objekta                              | μJy      |
| `Flux_err`   | Greška merenja fluksa                               | μJy      |
| `Filter`     | LSST fotometrijski filter (*u, g, r, i, z, y*)      | —        |

Kompletan skup sadrži približno **479.000 pojedinačnih fotometrijskih merenja**.

### Log fajlovi

`*_log.csv` fajlovi sadrže metapodatke i poznatu klasifikaciju astronomskih objekata.

| Kolona      | Opis atributa                                             |
| ----------- | --------------------------------------------------------- |
| `object_id` | Jedinstveni identifikator objekta                         |
| `Z`         | Crveni pomak (*redshift*) objekta                         |
| `Z_err`     | Greška crvenog pomaka                                     |
| `EBV`       | Galaktičko crvenjenje E(B−V)                              |
| `EBV_err`   | Greška vrednosti E(B−V)                                   |
| `SpecType`  | Spektroskopski tip objekta (TDE, AGN, SN Ia, SN II, itd.) |
| `target`    | Ciljna klasa: 1 za TDE, 0 za non-TDE                      |
| `split`     | Oznaka dela skupa podataka kojem objekat pripada          |

Pre primene modela izvršeno je pretprocesiranje podataka koje uključuje uklanjanje merenja bez vrednosti fluksa, korekciju za galaktičku ekstinkciju i izdvajanje statističkih i vremenskih karakteristika svetlosnih krivih u svakom od šest fotometrijskih filtera.

## Zavisnosti i automatsko testiranje

Projekat se oslanja na standardne naučne biblioteke za analizu podataka (numpy, pandas, scikit-learn) i vizuelizaciju (matplotlib). 

U skladu sa praksama kursa, provera izvršavanja i validacija stila koda na repozitorijumu vrši se pokretanjem sledećih komandi:

# Provera izvršavanja kompletnog notebook-a od prve do poslednje ćelije
pytest --nbmake --nbmake-timeout=60 Notebook.ipynb  
ili alternativno:  
python3 -m pytest --nbmake --nbmake-timeout=60 Notebook.ipynb

# Statička analiza i provera stila koda unutar ćelija notebook-a
nbqa pylint Notebook.ipynb
