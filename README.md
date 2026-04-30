Moje resenje za Nordeus Job Fair 2026 Data Science Challenge.

Kod je podeljen na 2 dela:

- pokusaji.ipynb gde su prikazane sve moje ideje i pokusaji koji nisu zavrsili u finalnom kodu i modelu. Tu se nalaze razliciti pristupi i vizualizacije kako bih se sto bolje upoznao sa podacima i modelima. 

- final.ipynb je zavrsan fajl u kojem se samo nalazi finalni model XGBoost.



1. pokusaji.ipynb

- EDA (Istraživanje podataka): Primetio sam zanimljivu bimodalnu raspodelu kod kvaliteta igrača (zvezdica), što verovatno ukazuje na default jačinu timova na početku igre u poređenju sa onima koji zapravo igraju i budže tim.

- Pokušaj mikro-modelovanja (Reverse Engineering): Umesto da predviđam pobednika na nivou klana, hteo sam da predvidim rezultat svakog individualnog duela (1 na 1). Napisao sam algoritam koji preko kombinatorike traži koje su to kombinacije pobeda/poraza mogle da daju ukupan broj poena klana. Zašto sam odustao od ovoga? Bilo je previše šuma. Menadžeri menjaju redosled igranja, a klasifikacija je bila previše disbalansirana. Tačnost je bila oko 50%.

- Feature Graveyard: Ovde sam ispisao i testirao preko 60 različitih feature-a (razne sinergije, efikasnosti, varijanse unutar klana). Ispostavilo se da ubacivanje svega toga samo buni model, pa sam za finalno rešenje napravio strogu selekciju.

2. final.ipynb

- Makro pristup: Prešao sam na modelovanje direktno na nivou klanova.

- Diff i Ratio: Umesto suvih statistika, fokusirao sam se na razliku (_diff) i količnik (_ratio) ključnih metrika između dva klana.

- Sample Weighting: Dao sam modelu veće težine (weights) za mečeve gde je razlika u poenima bila ogromna. Ideja je da model više uči iz ubedljivih pobeda nego iz onih gde je presudio jedan gol.

- Selekcija feature-a: Zadržao sam samo ono što stvarno radi – minimumi i proseci trening bonusa.

- Optimizacija: Koristio sam Optuna biblioteku za traženje najboljih hiperparametara za XGBoost i StratifiedKFold (5 foldova) kako bih bio siguran da model ne overfituje.