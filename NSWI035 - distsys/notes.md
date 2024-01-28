## Zdroje
- Tanenbaum, van Steen: Distributed systems
  - [free copy](https://www.distributed-systems.net/index.php/books/ds4/)
- Chow, Johnson: Distributed Operating Systems & Algorithms
  - [archived](https://archive.org/details/distributedopera0000chow)
- Antonopoulos: Mastering Bitcoin
  - [free on repo](https://github.com/bitcoinbook/bitcoinbook?tab=readme-ov-file)
- Novák: Lightning Network
  - found no free version
- Paxos, Raft: ~ wiki, posts, lecture recordings

# Introduction 
definice a funkce, hw architektury

### Cluster Computing​
- nody plnia tú istú úlohu
- nody majú homogénne hw/OS, 
- často blízko pri sebe, prepojené cez LAN​
- centralizované (manager master node)
- shared memory, RDMA, MQ​
- (?) batch ⇝ single system image​
- (?) middleware​

### Grid Computing​

- nody môžu mať vlastné programy, algoritmy
- nody môžu mať heterogénne HW/OS
- časo ďaleko od seba prepojené cez WAN
- nody dokopy tvoria tzv. Virtual Organisation
- OGSA - Open Grid Services Architecture​
  - Fabric layer
    - consists of the physical and logical resources that are available for grid computing, such as computers, networks, storage devices, sensors, etc. The fabric layer provides basic services for accessing and managing these resources.
  - Connectivity layer
    - defines the communication protocols and mechanisms that allow the exchange of data and messages among grid components. The connectivity layer supports security, encryption, authentication, and authorization features.
  - Resource layer
    - provides a uniform interface for using and controlling the resources in the fabric layer. The resource layer defines the properties, states, and behaviors of each resource, and exposes them as grid services that can be invoked by other components.
  - Collective layer
    - implements the coordination and collaboration logic among multiple grid services. The collective layer provides services for discovery, monitoring, scheduling, brokering, data management, and fault tolerance.
  - Application layer
    - consists of the end-user applications that utilize the grid services and resources. The application layer can also provide tools and frameworks for developing and deploying grid applications.

### Cloud Computing​

- resource providers, utility computing​
  - IaaS, PaaS, SaaS, ... *aaS​
- high availability - distributed protocols​

### Distributed Information Systems
- Distributed Data Processing​
  - množstvo nodeov spracúva rozdelené dáta paralelne
  - data-intensive computing, Big Data, NoSQL databáze, Hadoop, data mining​
- Distributed Transaction Processing​
  - distribuované transakce, vnořené transakce, 
    - DB systémy, enterprise aplikace​
      - transactional RPC, transakční monitor​
  - ACID​
    - Atomicity - each transaction either fails or succeeds as a whole
    - Consistency - database is in a valid state before and after transaction
    - Isolation - parallel transactions leave database in state as if they were executed sequentially
    - Durability - once committed, transactions effects will remain in place
- Enterprise Application Integration ​
  - RPC, RMI / message-oriented middleware, ESB, publish-subscribe​
    - tightly / loosely coupled communication​

### Pervasive Systems
- dynamická organizace​
- typicky velký a měnící se počet 'malých' heterogenních uzlů​
  - mikrouzly, senzory, sci-fi house, samoorganizace, kolektivní znalost a rozhodování, distribuovaní agenti, inteligentní brouci, inteligentní plíseň, ...

### Peer-to-peer Systems
- DHT, file sharing, P2P computing​
  - cryptocurrencies, smart contracts, censorship-resistant ...

## Motivácia a ciele distribuovaného systému

### Motivácia
- Ekonomika
  - síť běžných PC vs. supercomputer ​
- Rozšiřitelnost​
  - přidání uzlu vs. upgrade centrálního serveru​
- Distribuovanost​
  - 'inherentní distribuovanost' problému
- Prispôsobivosť
  - Autonómia
    - každý node je schopný samostatnej funkčnosti
  - Decentralizované rozhodovanie
    - každý node vykonáva rozhodnutia nezávisle na ostatných
  - Otvorenosť
    - prepojenie rôznych komponentov s otvoreným rozhraním
  - Migrácia procesov a prostriedkov
    - entity možno prenášať podľa potreby

### Ciele

- **Transparentnosť**
  - skrytie komplexnosti DS pred užívateľom
  - **Access / prístupová** 
    - nie je rozdiel medzi reprezentáciou a prístupom medzi lokálnymi a vzdialenými prostriedkami
  - **Location / lokačná**
    - nie je rozoznateľné, ktoré prostriedky sú lokálne a ktoré nie
    - tomuto pomáhajú uniformné ID prostriedkov, ako napr. URL
  - **Relocation / relokačná**
    - prostriedky môžu meniť úložisko bez vplyvu na usera
  - **Migration / migračná**
    - rovnako sa pristupuje bez ohľadu kde je užívateľ
  - **Replication / replikačná**
    - nie je vidno, že existujú replikované kópie prostriedkov, resp. ku ktorej kópii má user prístup
  - **Concurrency / konkurenčná**
    - nie je vidno, ak ten istý prostriedok použiva niekto iný
  - **Failure / chybová**
    - nie je vidno, že nejaká časť systému je nedostupná
- **Spolehlivost​**
  - výpadek jednoho uzlu vs. výpadek CPU ​
- **Škálovateľnosť**
  - vyhnúť sa známkam centralizovanosti (pri dosť veľkom systéme potential bottleneck)
  - tým pádom
    - uzly nemajú úplne informácie o stave systému
      - oneskorenie komunikácie, nutnosť synchronizácie
    - uzly sa rozhodujú na základe lokálne dostupných informácii
      - caching, partitioning, 
      - hrozí oneskorenie, nepresnosti
    - výpadok uzlu nesmie spôsobiť nefunkčnosť systému (single point of failue)
    - nedá sa spoliehať na presné globálne hodiny
- **Výkonnosť​**
  - výkon nerastie lineárne s počtom uzlov
    - komplexnejšia architektúra, náklady na réžiu

## 8 falllacies of distributed computing
- The network is reliable.
- Latency is zero.
- Bandwidth is infinite.
- The network is secure.
- Topology doesn't change.
- There is one administrator.
- Transport cost is zero.
- The network is homogeneous.

## Multiprocesory vs. multicomputery

- **Tightly-coupled (Multiprocesor)**
  - spoločná pamäť pre všetky CPU
  - **Symmetric multiprocessor system (zbernicová archiektúra)**
    - jedna zbernica spája procesory
    - lacná, dostupná, nízko škálovateľná (max. desiatky uzlov)
    - nutno synchronizovať cache
  - **prepínačová architektúra**
    - možný väčší počet uzlov
    - kvadratický počet switchov - nákladné
    - alternatíva Omega network
      - obojstranne binárne stromové prepojenie memory a cpu
      - n * log n switchov
- **Loosely-coupled (Multicomputer)**
  - samostatná pamäť pre každé CPU
  - **zbernicová architektúra**
    - menšia vzájomná komunikácia vďaka vlastnej pamäti
    - škálovateľné
    - lacné, dostupné
    - datacentrá
  - **prepínačová architektúra**
    - v tvare mriežky
      - jednoduchá implementácia
      - riešenie 2D problémov - grafy, analýza obrazu
    - alebo hyperkocky
      - node spojený so susedmi na každú stranu
      - supercomputery - 10000-ce uzlov


## Multiprocesory

### Uniform Memory Access

### (Cache-coherent) Non-Uniform Memory Access
- distribuovaná pamäť
- directory-based cache coherence
- 

### Cache-only Memory Access
- distribuovaná pamäť
- directory-based cache coherence
- distribuovaná pamäť je použitá ako veľká sekundárna cache, tzv. Attraction memory

----------------------------------------------------------------

# Interprocess communication

- **Exactly once / at least once / at most once**
  - [goodread](https://bravenewgeek.com/you-cannot-have-exactly-once-delivery/)

## klient/server model

## Správy

## Spoľahlivosť

## RPC

- prepojenie klienta a servera na aplikačnej úrovni
  - klient používa metódy z interface
  - interface implementuje stub u klienta
  - stub posiela správu serveru
  - na serveri správu preberie skeleton
  - vykoná metódu
  - odošle naspäť výsledok
  - u klienta stub rozbalí správu, predá vyýsledok klientovi
- proces
  - **Kompilácia**
    - klient stub a server skeleton sú programované spoločne
  - **Spojenie**
    - server zviditeľnený (binder, trader, broker)
    - klient sa napojí
  - **Vyvolanie**
    - (marshalling) správa sa prenesie
    - (?) potvrdzovanie, exception handling
- problémy
  - **Transparentnosť**
    - prístup ku globálnym premenným
      - neexistuje zdielaná pamäť
      - DSM - ťažkopádne
    - pointery, dynamické štruktúry
    - polia
    - reprezentácia dát
      - endianita, floaty, encoding
    - rozdiely v sémantike
    - skupinová komunikácia
    - security

## Skupinová komunikácia

----------------------------------------------------------------

# Synchronization algorithms

## Fyzické hodiny

- **Cristianov algoritmus**
  - proces
    - sender pošle request o čas v čase T0
    - receiver pripojí k odpovedi svoj čas T1, odošle
    - sender dostane odpoveď v čase T2
    - sender nastaví svoj čas na T1 + (T2 - T0) / 2
  - sender posiela requesty periodicky
  - čas sa upraví postupne, úpravou rýchlosti vlastných hodín, až kým sa čas nevyrovná

- **Berkeley algoritmus**
  - nesnaží sa o správny čas, iba synchronizáciu
  - používa sa najviac vo wireless prostredí
  - predpokladá žiadne skoky, ergo viac-menej konštantný čas prenosu správy ku každému v sieti
  - proces
    - všetkým pošle svoj čas
    - od všetkých dostane ich offset od svojho poslaného času
    - vyhodí extrémy, spriemeruje odpovede, a rozošle nový čas

- **Intersection algoritmus**

- **Intervalový čas**
  - Google TrueTime

## Logické hodiny

- neoznačujú čas, iba poradie udalostí
- nezaručujú kauzálnu závislosť

- **Synchronizácia**
  - sender pošle správu so svojim priloženým logickým časom
  - ak receiver dostane v správe logický čas väčší ako ten svoj, upraví svoj logický čas na o jeden vyšší ako ten prijatý

## Vylúčenie procesov

- **Centralizovaný algoritmus**
  - centrálny koordinátor
  - proces
    - node chce prístup k shared resource
    - pošle žiadosť koordinátorovi
    - koordinátor sa pozrie či resource niekto používa
      - ak nie, potvrdí prístup
      - inak počká, kým sa resource uvoľní, odpovie potom
  - problémy
    - single point of failure
  - komunikačná zložitosť
    - 2 ~ 3
- **Lamportov algoritmus**
  - distribuovaný
  - proces
    - node chce prístup k shared resource
    - vypýta si od každého iného nodeu prístup, priloží timestamp
    - každý node kt. ho nepoužíva, potvrdí prístup
    - ak ho nejaký používa, odpovie, až keď skončí
    - ak node dostane odpovede od všetkých a nedostal žiadosť s menším timestampom ako svoju, berie resource
    - nakoniec posiela release všetkým
    - všetci mažú jeho staré requesty
  - komunikačná zložitosť
    - 3(n-1)
    - REQ to all but self
    - RES from all but self
    - REL to all but self
- **Ricart & Agrawala algoritmus**
  - proces
    - podobne ako Lamport, bez release
  - problémy
    - výpadok toho, čo resource používa
  - komunikačná zložitosť
    - 2(n-1)
    - REQ to all but self
    - RES from all but self
- **Volby**
  - každý node má 1 hlas
  - proces
    - node chce prístup k shared resource
    - pošle každému žiadosť
    - ak dostane aspoň N/2 hlasov, berie resource
    - inak čaká
  - komunikačná zložitosť
    - O(n)
  - výhody
    - vydrží výpadok až floor(N/2) nodeov
  - nevýhody
    - deadlock (i.e. 2 procesy dostanú narovnako hlasov)
- **Volebné okrsky**
  - nody patria do okrskov
  - podmienky na okrsky
    - každé 2 nody v aspoň 1 spoločnom okrsku
    - každý okrsok rovnako veľký
    - každý node v rovnakom počte okrskov
  - komunikačná zložitosť
    - O(|Sp|)
- **Strom (Agrawal & El Abbadi)**
- **Skeleton (Raymond)**
- **Suzuki & Kasami**
- **Token ring**

## Voľba koordinátora

- **Bully**
  - všetky nodey s ID: 1..N
  - proces
    - niekto si všimne, že koordinátor sa neozýva
    - pošle COORD všetkým s vyšším ID
    - ak dostane odpoveď, končí
    - ak nie, stáva sa koordinátorom, posiela broadcast o zmene
    - každý, kto dostane COORD, odošle OK a posiela COORD vyššie 
- **Invitation algoritmus**
  - nodey s ID
  - koordinátor posiela heartbeat
  - node odpovedá na heartbeat, či je sám koordinátor
  - ak koordinátor dostane odpoveď, že je niekto koordinátor, zbaví ho funkcie a preberie jeho group
  - ak node nedostane heartbeat v timeoute, stáva sa koordinátorom
- **Kruhový algoritmus**
  - nodey s ID
  - proces
    - niekto sa rozhodne voliť, pošle následníkový (sender_id, highest_id)
    - každý prepošle správu, prípadne nastaví highest_id
    - keď sa správa vráti senderovi, highest_id je nový koordinátor
    - posiela novú správu s oznamon koordinátora
  - zložitosť
    - O(n^2)
- **Randomizovaný protokol**
  - najlepšie na spoľahlivej rýchlej lokálnej sieti
  - leader posiela heartbeat
  - ak nie je heartbeat v timeoute, node púšťa voľby, dáva si hlas, broadcastuje žiadosť o hlas
  - ak dostane nadpolovičné hlasy, stáva sa leader
  - inak timeout 150-300ms
- **Lepší kruhový (Hirschback & Sinclair)**
  - zlepšenie komunikácie
  - koordinácia okolia 2^k
  - proces
    - node pošle žiadosť o voľby pre seba naľavo aj napravo
    - 

## Kauzálna závislosť

- udalosti vrámci procesu sú kauzálne závislé
  - p1 -> p2
- odoslanie a prijatie jednej správy naprieč procesmi je kauzálne závislé

## Doručovacie protokoly

### Kauzálne doručovanie
- **Vektorové hodiny**
  - použitie vektorovej značky rovnakej dĺžky ako je počet procesov
  - základné pravidlá
    - každý proces začína s nulovou značkou
    - send pi(m): VT(m) = ++VT(pi)[i]
      - e.g. p2 s time (2, 4, 1) pošle správu p3 s časom (2, 5, 1)
    - recv pj(m): VT(pj) = max(VT(pj), VT(m)) [foreach k]
      - e.g. p2 s time (2, 4, 1) prijme správu od p 1 s časom (4, 1, 3)
                a upraví svoj time na (4, 4, 3)
- **Maticové hodiny**
  - časová značka je vektor vektorov - vektor pre každú skupinu
  - pri osodielaní, node inkrementuje svoj counter v každom vektore, v ktorej skupine je
- **Distribuovaný total-order protokol**
  - zabezpečiť, že každý receiver dostane tú istú sadu správ v tom istom poradí
  - proces
    - receiver po prijatí správy pošle späť čas doručenia
    - sender zvolí maximálny čas doručenia a oznámi ho všetkým
    - receiveri na základe toho usporiadajú správy
  - example
    - nech sú senderi S1, S2
    - každý pošle broadcast pre R1, R2
    - R1 dostane S1 v T5, S2 v T8
    - R2 dostane S1 v T6, S2 v T7
    - S1 dostane časy doručenia T5, T6, rozošle T6
    - S2 dostane časy doručenia T8, T7, rozošle T8
    - receiveri dostanú časy, vedia, že S1 (T6) predchádza S2 (T8)

### Spoľahlivé doručovanie
- doručovanie nie je spoľahlivé
  - výpadky senderov, receiverov
- **Transakcie**
  - doručenie až po commite
  - funguje, ale zbytočne
- **Flooding**
  - ak node dostane správu, ktorú ešte nedostal, rozošle všetkým ostatným
  - spoľahlivé, neefektívne - O(n^2)
- **Flooding s potvrdzovaním**
  - p_i odošle správu všetkým ostatným
  - p_j dostane správu, pošle p_i ACK a rozošle správu všetkým ostatným
  - p_j si udrží správu, až kým nevie že ju prijali všetci
- **Trans algoritmus**
  - [source](https://ieeexplore.ieee.org/document/80121)
  - spoľahlivé kauzálne doručovanie
  - využitie kauzality a piggybackingu potvrdení
  - správy obsahujú informácie o predchádzajúcich správach
  - napr.
    - A pošle správu a
    - B dostane správu a
    - B neskôr pošle vlastnú správu b, do nej cez piggyback pridá Ack(a)
    - C dostane správu b+Ack(a)
      - C v nej vidí, že existuje správa a, kt. ale nedostal. Keď neskôr broadcastuje vlastnú správu c, pridá k nej Nak(a)+Ack(b)
    - A dostane správu c+Nak(a)+Ack(b)
      - vidí, že niekto nedostal a, opakuje broadcast
  - data structure
    - každá **správa** obsahuje v headeri
      - sender processor ID
      - message ID (seq. number)
      - ACK list (of message ID)
      - NAK list (of message ID)
    - každý **procesor** si drží
      - ACK list (of message ID)
        - zoznam ID správ, ktoré tento procesor dostal
        - tento zoznam si plní sám, keď prijme novú správu
      - NAK list (of message ID)
        - zoznam ID správ, o ktorých vie, že existujú, no nedostal ich
        - tento zoznam plní z ACK listov prijatých správ
          - tz. v správe nájde info o tom, že nejaký iný procesor tvrdí, že dostal nejakú správu, kt. ale tento procesor nemá vo vlastných ACK
      - RECEIVED list (of message)
        - správy, kt. dostal alebo nedávno broadcastoval
        - slúžia na opätpvný broadcast / retransmission
        - mažú sa až keď je isté, že ich nie je treba viac odosielať
      - PENDING RETRANSMISSION list (of message ID)
        - tu sa držia správy, na ktoré iný procesor rozoslal NAK, teda mu ich treba doručiť
        - tieto procesor broadcastuje znova alebo zmaže, ak zbadá, že ich broadcastuje iný procesor
  - proces
    - **odoslanie správy**
      - keď procesor odosiela broadcast, pripojí k nej svoje ACK a NAK
      - svoje ACK, ktoré takto broadcastoval, si už nemusí pamätať
    - **prijatie správy**
      - ("+" = append, "-" = delete, "\" = difference, "&" = intersection, "|" = union)
      - self.ACK += message.ID
      - self.RECEIVED += message
        - pridá správu do svojej pamäte
        - keď si ju bude niekto pýtať, vie ju broadcastnuť
      - self.NAK -= message.ID
        - zmaže správu zo svojich NAK
        - dostal správu, ktorú evidoval ako chýbajúcu v minulosti
      - self.PENDING -= message.ID
        - ak má správu vo svojich PENDING, zmaže ju
        - správa, ktorú mal (re-)broadcastovať, už stihol rozoslať niekto iný
      - self.ACK \= message.ACK
        - všetky ACK v message zmaže zo svojich ACK
        - ACK v message sa plní iba keď si nejaký procesor ukladá message do RECEIVED, teda je isté, že niekto si správy z message.ACK pamätá, ergo ich nemusí držať tento procesor
      - self.NAK |= message.ACK \ self.ACK
      - self.NAK |= message.NAK \ self.ACK
        - všetky ACK a NAK v message, kt. nemá vo svojich ACK, zapíše do svojich NAK
        - niekto iný dostal (alebo si pýta) správu, kt. tento ešte nevidel, teda ich chce tieť
      - self.PENDING |= self.RECEIVED & message.NAK
        - všetky NAK v message, kt. má vo svojom RECEIVED si pridá do svojich PENDING
        - message, kt. niekto iný chce a tento procesor ich má, môže neskôr broadcastovať
      - self.NAK |= self.MAX_SEEN..(message.ID - 1)
        - všetky message od poslednej známej až po message.ID pridať do self.NAK
        - keďže správy sú kauzálne zoradené, možno z ich seq.num. odvodiť, aké správy ešte procesor nevidel

## Virtuálna synchronia
- [source](https://medium.com/@macworks/virtual-synchrony-575219a01252)
- mechanizmus zaručujúci **atomický multicast**
  - teda źe správu dostanú všetci členovia skupiny alebo nikto
- "virtuálna" preto, lebo v praxi funguje asynchrónne, no teoreticky funguje synchrónne
- 

----------------------------------------------------------------

# Distributed consensus

## Detekcia globálneho stavu
- prečo?
  - analýza stabilných vlastností (i.e., ukončenie systému, uviaznutie výpočtu)
  - 
- **Dijkstra-Sholten algoritmus**
  - pre **strom**
    - každý list pri prechodu do **pasívneho** stavu pošle signál rodičovi
    - každý proces, kt. dostane signál od všetkých detí, pošle signál rodičovi
    - ak iniciátor dostane signál od všetkých detí, systém končí
  - pre **DAG**
    - každá hrana má **deficit**
      - = # of messages (down) - # of signals (up)
    - listy ako pre strom - skončia a pošlú signál odkiaľ prišla správa
    - každý proces, kt. má **nulový dátový deficit** (sum of deficit of outgoing edges), posiela signály hore, aby aj **signálový deficit** (sum of deficit of incoming edges) bol nulový
  - pre **obecný graf**
    - problém: nemáme listy, ktoré začnú ukončenie
    - riešenie: dynamicky generujeme spanning tree
    - pri rozosielaní computation message si každý node zapamätá edge, po ktorom prišla prvá správa
    - proces
      - initiator rozošle computational message
      - ak node dostane message a je to prvýkrát, označí daný incoming edge ako prvý a rozošle message ďalej
      - ak node dostane message a nie je to prvýkrát, hneď odošle signál späť
      - ak node dostane signál od každého outgoing edge, správa sa ako list
- **Huangov algoritmus**
  - iniciátor má váhu 1
  - keď proces pošle správu, 1/2 svojej váhy predá správe
  - keď proces prijme správu, preberie jej váhu
  - keď proces skončí, pošle signál, kt. dá celú svoju váhu
  - systém končí, keď iniciátor má znova váhu 1
- **Značkový (TM) algoritmus**
- 

## Konzistentný stav
- **Rez** je rozdelenie udalostí na disjunktné past a future
- **Konzistentný rez** je rez, kde sedí kauzalita (ak a predchádza b a b je v past, aj a je v past)
- **Stav** distribuovaného výpočtu je množina udalostí počas výpočtu
- **Konzistentný stav** je past konzistentného  rezu 
- KS S' je **dosiahnuteľný** z S, ak existuje udalosť e, že:
  - $S \Rightarrow_e S'$ iff $\exists e: S' = S \cup \{e\}$
- **Rozvrh** $s = (e_1, \dots, e_n)$ je taký, že platí:
  - $S \Rightarrow_{e_1} S_1 \Rightarrow_{e_2} \dots S_{n-1} \Rightarrow_{e_n} S_n$
  - platí $S \subseteq S_n$
- 

## Dosiahnutie distribuovanej zhody

## Replikovaný stavový automat

## Paxos

## RAFT

----------------------------------------------------------------

# Distributed shared memory 

## Konzistenčné modely

## Distribuované stránkovanie

# Asset and process management

## Zablokovanie a distribuované algoritmy detekce

## Vzdialené spustenie procesov

## Migrácia

## Vyvažovanie záťaže

----------------------------------------------------------------

# Replication 

## Mechanizmy replikácie

## Klientocentrické konzistenčné modely

## Epidemické protokoly

----------------------------------------------------------------

# Technical principles of cryptocurrency

## Blockchain

## Konsenzus

## UTXO

## Multisig

## Segwit

## Merkle tree

## Bloom filters

## Proof-of-work vs. Proof-of-stake

## Lightning network

( ..., taproot, coinjoin, smart contracts, DeFi, stablecoin, CBDC, zero-knowledge proofs, ... )