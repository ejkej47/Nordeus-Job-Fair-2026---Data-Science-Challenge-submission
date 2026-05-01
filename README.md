# Nordeus Job Fair 2026 - Data Science Challenge

Ovo je moje rešenje za Nordeus Job Fair 2026 Data Science Challenge.

Projekat je organizovan u dva glavna Jupyter Notebook fajla kako bi se jasno razdvojila faza istraživanja od finalnog rešenja:

1. **pokusaji.ipynb** – Istraživački fajl (EDA, eksperimenti, mikro-modelovanje i odbačene ideje).
2. **final.ipynb** – Završni fajl sa optimizovanim XGBoost modelom i kodom za Smart Assistant bota.

### Pokretanje projekta
Da biste pokrenuli kod, potrebno je da instalirate biblioteke iz `requirements.txt` fajla. Preporučujem korišćenje virtuelnog okruženja:
```bash
pip install -r requirements.txt
```

### 1. Faza Istraživanja (pokusaji.ipynb)
Ovaj fajl sadrži analitičke korake, vizualizacije i eksperimentalne pristupe:
* **EDA (Istraživanje podataka):** Primetio sam bimodalnu raspodelu kod kvaliteta igrača (zvezdica). Ovo verovatno ukazuje na default jačinu timova na samom početku igre u poređenju sa onima koji zapravo igraju i planski nadograđuju svoj tim.
* **Pokušaj mikro-modelovanja (Reverse Engineering):** Umesto da predviđam pobednika na nivou klana, hteo sam da predvidim rezultat svakog individualnog duela (1 na 1). Napisao sam algoritam baziran na kombinatorici koji traži koje su to kombinacije pobeda/poraza mogle da daju ukupan broj poena klana. Od ovog pristupa sam odustao jer je bilo previše šuma. Menadžeri menjaju redosled igranja, a klasifikacija je bila previše disbalansirana, što je tačnost zadržalo na oko 50%.
* **Feature Graveyard:** Ovde sam ispisao i testirao preko 60 različitih feature-a (razne sinergije, efikasnosti, varijanse unutar klana). Ispostavilo se da ubacivanje svega toga samo unosi šum i buni model, pa sam za finalno rešenje napravio strogu selekciju.

### 2. Finalni Model (final.ipynb)
Završni pristup se fokusira na modelovanje direktno na nivou celog klana:
* **Makro pristup (Diff & Ratio):** Umesto suvih statistika, fokusirao sam se isključivo na razliku (`_diff`) i količnik (`_ratio`) ključnih metrika između dva klana.
* **Selekcija feature-a:** Zadržao sam samo ono što stvarno radi i pravi razliku – minimumi i proseci trening bonusa.
* **Sample Weighting:** Dao sam modelu veće težine (weights) za mečeve gde je razlika u poenima bila ogromna. Ideja je da model više uči iz ubedljivih pobeda nego iz onih gde je presudio jedan nasumičan gol.
* **Optimizacija i Evaluacija:** Koristio sam Optuna biblioteku za traženje najboljih hiperparametara za XGBoost. Evaluaciju sam izvršio pomoću rigorozne StratifiedKFold validacije (5 foldova) kako bih bio siguran da model ne overfituje.

**Najbolji rezultat modela (Srednji Accuracy): 0.5882 (+/- 0.0066)**

### 3. Bonus Task: Smart Assistant
Kao dodatak, napravio sam alat koji ML predikcije pretvara u konkretnu poslovnu vrednost (Business Value).
* **Simulator Treninga:** Umesto generičkih saveta poput "trenirajte više", bot koristi istrenirani ML model kao simulator da igračima ispiše tačan i najjeftiniji plan treninga potreban za pobedu.
* **Ciljana Logika (>50% Win Rate):** Kako se ispostavilo da je najbitniji parametar `training_bonus`, fokus je na njemu. Ako klan gubi, bot pronalazi optimalan način da prebaci 50% šanse (spašavanje jednim igračem ili mobilizacija celog klana).
* **Greedy Algoritam i "Shifting Minimum":** Bot rešava problem pomerajućeg minimuma tako što iterativno pronalazi trenutno najgoreg igrača, dodaje mu samo +1% bonusa, preračunava novu snagu klana i pita model za nove šanse. Teret treninga se tako savršeno i ravnomerno raspoređuje na najslabije karike.
* **Prepoznavanje "Nemoguće misije":** Bot automatski iščitava apsolutni maksimum iz podataka. Ako shvati da čak ni maksimalan trening ne donosi pobedu zbog lošijih osnovnih atributa (zvezdica), savetuje igračima da čuvaju resurse za sledeći meč.
