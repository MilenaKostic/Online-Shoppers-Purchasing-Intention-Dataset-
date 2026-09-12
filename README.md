# Projekat iz predmeta Mašinsko učenje, školska 2025/2026
# Predviđanje namere kupovine korisnika na internetu - Milena Kostić 4012/2024 

## Opis projekta 
Cilj ovog projekta je analiza ponašanja korisnika na sajtu internet prodavnice i razvoj modela mašinskog učenja za predviđanje da li će korisnik tokom posete sajtu izvršiti kupovinu ili ne.

## Projekat obuhvata:
- analizu i pripremu podataka
- treniranje i evaluaciju tri različita modela: logističke regresije, Random Forest i potpuno povezane neuronske mreže
- rešavanje problema neuravnoteženosti ciljne promenljive primenom SMOTE algoritma
- poređenje performansi svih modela na osnovu metrika poput preciznosti, odziva, F1 mere i ROC-AUC.

## Opis skupa podataka 
Korišćen je javno dostupan skup podataka Online Shoppers Purchasing Intention Dataset, preuzet sa: https://archive.ics.uci.edu/dataset/468/online+shoppers+purchasing+intention+dataset 

Skup podataka sadrži 12330 zapisa, pri čemu svaki zapis predstavlja jednu sesiju korisnika na sajtu tokom perioda od godinu dana. Skup ima ukupno 18 atributa, od kojih je 10 numeričkih i 8 kategoričkih.

- atributi Administrative, Administrative_Duration, Informational, Informational_Duration, ProductRelated, ProductRelated_Duration predstavljaju broj i trajanje poseta različitim tipovima stranica u okviru sesije.
- BounceRates, ExitRates, PageValues predstavljaju Google metrike ponašanja korisnika na sajtu 
- SpecialDay je atribut koji predstavlja blizinu sesije određenom posebnom datumu poput Danu zaljubljenih ili Danu majku
- Month, OperatingSystems, Browser, Region, TrafficType, VisitorType, Weekend su kategorički atributi vezani za vreme posete i tehničke i demografske karakteristike sesije.
- Revenue je ciljna binarna promenljiva koja označava da li je sesija rezultovala kupovinom.

Ciljna promenljiva je izraženo neuravnotežena, odnosno oko 85% sesija ne rezultuje kupovinom dok je samo oko 15% sesija završeno kupovinom. 

## Priprema podataka
Skup podataka je proveren i pripremljen za dalju analizu i modelovanje kroz sledeće korake:
- Provera nedostajućih vrednosti i duplikata
- Transformacija binarnih promenljivih Revenue i Weekend iz True/False u numerički oblik 1/0
- One-hot encoding kategoričke promenljive Month i VisitorType. Rezultat su binarne (dummy) kolone.
- Analiza ekstremnih vrednosti. IQR metodom identifikovane su ekstremne vrednosti kod numeričkih atributa, ali je utvrđeno da ove vrednosti nose korisnu prediktivnu informaciju, odnosno višu stopu kupovine, te nisu uklonjene
- Standardizacija je primenjena za modele osetljive na skalu ulaznih podataka, odnosno na modele logističke regresije i neuronske mreže. Scaler je treniran na trening skupu. Model Random Forest korišćen je bez standardizacije s obzirom na to da stabla odlučivanja nisu osetljiva na skalu atributa.
- Podela podataka je izvršena na skup za trening, validaciju i test, uz korišćenje parametra stratify radi očuvanja odnosa klasa ciljne promenljive u svim podskupovima.

## Korišćeni modeli 
U projektu su implementirana i upoređena tri klasifikaciona modela 

1. Logistička regresija 
Koriščena je kao osnovni linearni model. Hiperparametar regularizacije C podešen je pretragom preko ROC-AUC mere na validacionom skupu. Dodatno je testirana i primena SMOTE algoritma za balansiranje trening skupa.
2. Random Forest 
Ansambl model zasnovan na stablima odlučivanja, korišćen zbog otpornosti na asimetriju i ekstremne vrednosti u podacima. Podešavani su hiperparametri n_estimators i max_depth.
3. Potpuno povezana mreža
Testirane su tri arhitekture različite dubine i složenosti, od jednostavne, sa jednim skrivenim slojem, do dublje arhitekture sa Dropout regularizacijom, radi ispitivanja da li dodatna kompleksnost modela donosi poboljšanje performansi.

Svi modeli evaluirani su na istom test skupu koristeći identičan skup metrika - preciznost, odziv, F1-meru, ROC-AUC, radi obezbeđivanja fer i konzistentnog poređenja. 

## Zaključak, poređenje modela 
Na osnovu evaluacije na test skupu, ostvareni s
|Model| ROC-AUC | Precision | Recall | F1-Score|
|-----|---------|-----------|--------|---------|
|Logisticka regresija (SMOTE)| 0.892 | 0.72| 0.37 | 0.49|
|Random Forest| 0.921| 0.74 | 0.51| 0.60|
|Neuronska mreza| 0.908 | 0.71| 0.52 | 0.60|

Random Forest je pokazao najbolje sveukupne performanse, sa najvišim ROC-AUC i najboljim odzivom, uz zadržavanje visoke preciznosti. To znači da najpouzdanije prepoznaje stvarne kupce bez velikog broja pogrešnih pozitivnih predikcija. 
Dodatno, mera važnosti atributa (feature_importances_) dobijena ovim modelom u potpunosti se poklopila sa nezavisnom korelacionom analizom sprovedenom u uvodu, čime je potvrđena njena pouzdanost.

Neuronska mreža sa najjednostavnijom arhitekturom, sa jednim skrivenim slojem, ostvarila je rezultat vrlo blizak modelu Random Forest. Testiranjem tri arhitekture različite dubine utvrđeno je da dodatna složenost mreže ne donosi značajno poboljšanje performansi, već pre svega povećava rizik od preprilagođavanja, koji je delimično ublažen primenom Dropout regularizacije.

Logistička regresija je kao linearni model, pokazala primetno slabije rezultate od druga dva pristupa, čak i uz primenu SMOTE tehnike za balansiranje klasa. Analiza koeficijenata dodatno je pokazala da je njihova interpretacija otežana usled multikolinearnosti između atributa, što ukazuje da linearni model nije u potpunosti dovoljan da opiše nelinearne veze prisutne u podacima. 

Na osnovu sprovedene analize, Random Forest je izabran kao najpogodniji model za predviđanje namere kupovine korisnika na ovom skupu podataka, uzimajući u obzir kako prediktivne performanse, tako i jednostavnost pripreme podataka. 

## Literatura 
[1] Online Shoppers Purchasing Intention Dataset: https://archive.ics.uci.edu/dataset/468/online+shoppers+purchasing+intention+dataset 
[2] Mašinsko učenje, Mladen Nikolić i Anđelka Zečević, Beograd 2019.
[3] Mašinsko učenje, Izvodi sa vežbi 
[4] Pang-Ning Tan, Michael Steinbach, Anuj Karpatne, Vipin Kumar, Introduction to Data Mining, Second Edition, 2019


