# Pump Station Control System - LabVIEW & cRIO

## O projektu
Ovaj projekat predstavlja dizajn i implementaciju upravljačkog sistema pumpne stanice razvijenog u programskom okruženju **LabVIEW**. Upravljački algoritam je realizovan u vidu automata stanja (State Machine) koji se izvršava u realnom vremenu na **CompactRIO (cRIO)** platformi, dok se nadzor i upravljanje procesom vrši putem **SCADA** aplikacije na PC računaru. 

Projekat simulira realne industrijske uslove upravljanja rezervoarom uz pomoć Hardware-in-the-Loop (HIL) uređaja koji generiše dotok tečnosti.

## Ključne funkcionalnosti
Sistem obezbeđuje bezbedan i autonoman rad pumpne stanice kroz sledeće mehanizme:
* **Višestruki režimi rada:** Podrška za automatski (AUTO) i ručni (MANUAL) režim rada, uz mogućnost kontrolisanog gašenja (AUTO_SHUTDOWN) za automatsko pražnjenje rezervoara.
* **Zaštita od rada na suvo:** Automatsko isključivanje pumpi ukoliko nivo tečnosti padne ispod minimalnog nivoa senzora (B1 i B4).
* **Motorna zaštita:** Hardverska blokada rada pumpi usled aktivacije signala motorne zaštite (F1 i F2).
* **Hitno zaustavljanje (Emergency Stop):** Momentalni prelazak u havarijsko stanje (FAULT_EMERGENCY) uz prekid rada svih aktuatora i aktivaciju zvučno-svetlosne signalizacije iz bilo kog operativnog stanja.

## Arhitektura sistema
Sistem je podeljen u četiri glavne celine:
1. **SCADA nivo (PC):** Nadzorno-upravljačka aplikacija (`SCADA.vi`) preko koje operater zadaje komande i prati proces putem HMI interfejsa (LED indikatori, softverski tasteri, alarmi). Komunikacija se vrši preko mrežnih promenljivih (Network Shared Variables).
2. **Nivo realnog vremena (cRIO):** Izvršna logika sistema (`cRIO_main.vi`) koja sprovodi akviziciju digitalnih ulaza, kontrolu digitalnih izlaza i upravlja stanjima automata.
3. **Hardware-in-the-Loop (HIL):** Uređaj koji generiše slučajne vrednosti dotoka tečnosti, predstavljajući spoljni poremećaj za upravljački sistem.
4. **Simulacioni proces:** Računar zadužen za grafičku vizuelizaciju rezervoara, pumpi, dotočnih ventila i senzora nivoa (B1-B5).

## Automat stanja (State Machine)
Srž upravljačkog algoritma realizovana je kroz 7 specifičnih stanja definisanih u `System_States.ctl`:
* `INIT`: Početna inicijalizacija svih promenljivih i izlaza na nulu.
* `IDLE`: Mirno stanje sistema koje čeka odabir režima rada (Hand/Auto) od strane operatera.
* `AUTO_RUN`: Sekvencijalno upravljanje dotočnim ventilom (Y1) i pumpama (P1, P2) u zavisnosti od očitavanja senzora nivoa.
* `AUTO_SHUTDOWN`: Kontrolisano pražnjenje rezervoara, prekid dotoka i rad pumpi do pada ispod minimuma.
* `MANUAL_MODE`: Ručno upravljanje aktuatorima (isključivo preko SCADA interfejsa), uz zadržavanje osnovnih hardverskih zaštita.
* `FAULT_EMERGENCY`: Havarijsko stanje inicirano pritiskom na E-STOP.
* `FAULT`: Stanje greške koje zahteva potvrdu operatera (Acknowledge) pre povratka u normalan rad.

## Pokretanje projekta
1. Klonirajte repozitorijum na lokalnu mašinu.
2. Otvorite `projekat3pumpa.lvproj` u LabVIEW okruženju.
3. Pokrenite `cRIO_main.vi` (ukoliko imate povezan hardver ili simulirano cRIO okruženje).
4. Pokrenite `SCADA.vi` za pristup korisničkom interfejsu.
