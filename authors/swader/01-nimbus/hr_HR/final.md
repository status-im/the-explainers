# Nimbus za neznalice

[Nimbus](https://nimbus.status.im) je novi klijent za Ethereumov sljedeći stadij: Serenity. Nimbus je zapravo novi [čvor](https://bitfalls.com/hr/2017/11/26/whats-bitcoin-node-mining-vs-validation/), u neku ruku. Ako ste Ethereum laik i pokušavate doznati što Nimbus jest *upravo sada*, došli ste na pravo mjesto.

O shardingu, skaliranju i svemu drugome što [članak najave](https://blog.status.im/introducing-nimbus-3360367bb311) spominje pričati ćemo u budućim člancima. Ovaj članak napisan je kako bi vas upoznao s onime što Nimbus jest _danas_.

Želimo vam pomoći da razumijete što ovaj alat predstavlja, zašto postoji, i kako pomoći pri njegovom razvoju kako biste zajedno s Nimbus timom pomogli razviti Ethereum 2.0.

## Što je Nimbus sada?

!["Koja je moja svrha?" - Robot koji dodaje maslac](/wp-content/uploads/2018/11/01.png)

Za pregled onoga što Nimbus _želi_ biti, pogledajte [originalnu objavu](https://blog.status.im/introducing-nimbus-3360367bb311).

Trenutno, u studenom 2018, Nimbus je set alata koji uspješno prolazi neke softverske testove. Softverski testovi su setovi računalnog koda koji se moraju izvršiti bez da jave grešku. Kada se izvrše pomoću Nimbusa, rezultat tih testova ne bi smio biti negativan. Ako svi [ovi testovi](https://ethereum-tests.readthedocs.io/en/latest/) _prođu_ (pass), Nimbus se može smatrati Ethereum klijentom.

Ovako izgleda prekrasni ekran uspješnih testova.

![](/wp-content/uploads/2018/11/02.png)

Od ožujka 2019. Nimbus će sinkronizirati Ethereum [proof of work](https://bitfalls.com/hr/2018/04/24/whats-the-difference-between-proof-of-work-pow-proof-of-stake-pos-and-delegated-pos/) lanac do bloka 1.1 milijun, no ne može biti korišten za spajanje na neki [novčanik](https://bitfalls.com/hr/2017/08/31/what-cryptocurrency-wallet/) ili za slanje transakcija. Neće potpuno sinkronizirati blockchain ni u _light_ (laganom) ni u _full_ (punom) načinu rada, niti će pokrenuti neko korisničko sučelje.

Što se tiče Ethereum 2.0 strane Nimbusa koja se razvija u [nim-beacon-chain repozitoriju](https://github.com/status-im/nim-beacon-chain), trenutno će simulirati nekoliko validatora i beacon čvorova, spojiti ih, te graditi beacon lanac. To se može pokrenuti i lokalno pa slobodno isprobajte prateći upute iz [službenih objava](https://our.status.im/tag/nimbus).

## Dakle, koji mu je cilj?

![Nimbusov cilj](/wp-content/uploads/2018/11/03.png)

Tim trenutno razvija set alata i modula koji će zajedno zadovoljiti uvjete da podrže Ethereum 2.0, poput podrške za sharding ili validaciju putem [Proof of Stake](https://bitfalls.com/hr/2018/04/24/whats-the-difference-between-proof-of-work-pow-proof-of-stake-pos-and-delegated-pos/) sustava. Istovremeno ali zasebno se razvija i Ethereum 1.0 strana Nimbusa, što znači da će Nimbus biti alternativa za [Geth](https://bitfalls.com/hr/2018/02/12/explaining-ethereum-tools-geth-mist/) i Parity kao _full_ čvorove, ali će imati i mogućnost pokretanja u _light_ načinu rada - bez da treba preuzeti cijeli blockchain - i za [_stateless_](https://ethresear.ch/t/the-stateless-client-concept/172) način rada.

Prioritet je razviti Nimbus kao Ethereum 2.0 čvor, a tek nakon toga usavršiti 1.0 stranu. To je zbog toga jer Nimbus tim financije dobiva od Ethereum Fundacije koja prioritizira razvoj novog Ethereuma.

## Modularnost

Da bi ga se moglo pokrenuti na slabim i _embedded_ uređajima, Nimbus će morati biti iznimno podesiv. Krajnji je cilj podržati sve module na plug-and-play način jednostavnom aktivacijom i deaktivacijom po želji, dok će ti moduli funkcionirati kao pod-programi koji API-jem komuniciraju s Nimbus jezgrom.

Specifično, Nimbus će imati razne verzije. Moći ćete ga kompajlirati (izgraditi) i pokrenuti s posebnim opcijama. Ako ga stavljate na Nokia 3310 telefon, nećete graditi istu verziju kao kada ga stavljate na snažno stolno računalo, niti će to biti ista verzija koja će se koristiti u micro-USB stickovima koje ćete potajno priključiti u "pametne" gradske klupe da biste potajno dodatno decentralizirali mrežu 🕵️‍♂️

I, da, sve ovo će biti moguće s Nimbusom.

![Nimbus, šampion](/wp-content/uploads/2018/11/04.png)

Sučelja (poput JSON-RPC ili neko za _embedded Javu_) biti će razvijena za razne operativne sustave na kojima ćemo koristiti Nimbus. Tim se usredotočuje na otvoreni hardver, no ni jedna platforma neće biti zapostavljena. Nimbus će, primjerice, pružati i [EVM-C sučelje](https://github.com/ethereum/evmc) pa će svi klijenti koji koriste isti moći odabrati žele li koristiti Aleth, Geth, ili Nimbus.

Drugi modularni dijelovi biti će i primjerice back-end baza podataka koja se koristi (SQLite vs RocksDB), sustav za pisanje logova (konzola, JSON, txt datoteke, baza podataka), mrežni protokol itd.

## Natječe li se Nimbus s Gethom?

Da i ne. Ethereumova misija je od prvog dana da ima više klijenata radi zdravlja mreže ako se pronađe greška u jednom od njih. Na primjer, greška u Bitcoin Core klijentu oštetiti će cijelu Bitcoin mrežu jer je Bitcoin Core program koji se koristi u [95% slučajeva](https://coin.dance/nodes).

![](../images/05.png)

To je neprihvatljivo u Ethereumu, pa se trudimo imati diversificirani skup klijenata. Vodeći su trenutno Geth i Parity [u relativno sličnim postocima](https://www.ethernodes.org/network/1) s ponešto ostalih između njih. Cilj je izgraditi Nimbus do te razine da preuzme barem četvrtinu tog kolača, a kasnije uz druge klijente i manje.

Bitno je napomenuti da s Nimbusom ne pokušavamo smanjiti broj Gethova u mreži ili Paritijevu popularnost. Nimbus je tu da prošiti ekosustav novim uređajima koji sada mogu pokrenuti svoj čvor, a ne da zamijeni trenutne klijente. Kao primjer - Status aplikacija će koristiti Nimbus kada Nimbus bude spreman.

## Izgradnja

Dosta priče, ajmo graditi.

Prije nego počnemo, pretpostavlja se da

- ste [pročitali uvod u Nim](https://bitfalls.com/2018/10/09/introduction-into-the-nim-language/) i imate Nim na sustavu
- koristite sustav poput OS X ili Linux koji je u prijateljskim odnosima s komandnom linijom (Terminal). Ako niste, predlažemo da instalirate neku virtualnu mašinu koja ima sav potrebni softver već instaliran. Primjerice, [naša Nim Vagrant mašina](https://our.status.im/setting-up-a-local-vagrant-environment-for-nim-development/) je vrlo dobro rješenje.

_Napomena: Nimbus koristi Makefile sustav za igradnju komponenti kako bi proces pokretanja softvera bio identičan na svim platformama. Ne koristimo Nimov upravitelj paketima Nimble jer je fundamentalno nefunkcionalan i ne izgleda da će problemi uskoro biti riješeni._

### Instalacija

Klonirajte Nimbus:

```bash
git clone https://github.com/status-im/nimbus
cd nimbus
```

Da pokrenemo Nimbus prvo moramo instalirati RocksDB bazu podataka i noviju verziju Nima. Na OS X izvršite:

```bash
brew install rocksdb
```

Na Linux sustavima, sljedeće bi trebalo biti dovoljno:

```bash
sudo apt-get install librocksdb-dev rocksdb # or your own Linux distribution's equivalent
```

Na Windows sustavu, prvo se pobrinite da imate `make` instaliran - to možete učiniti pomoću popularnih upravitelja paketima kao što je [Chocolatey](https://chocolatey.org):

```bash
choco install make
```

Alternativno, instalirajte `MinGW32make.exe` s [MinGW web stranice](http://www.mingw.org/).

_Napomena: Windows zahtijeva da dodate programe koje želite moći pokrenuti iz bilo kojeg dijela sustava u vašu PATH varijablu radnog okruženja. To se radi tako da otvorite Start izbornik, tražite "env", kliknete na Edit System Environment Variables i izmijenite PATH unos na popisu tako da dodate novu vrijednost na dno koja odgovara lokaciji gdje ste instalirali `make` ili `mingw make` (ako ste koristili Chocolatey, ovaj korak nije potreban). Moj PATH izgleda [ovako](https://imgur.com/a/yQIi6Qa)._

Samo na Windowsima - pokrenite:

```bash
make fetch-dlls
```

ili ako ste instalirali `mingw32make.exe`:

```bash
mingw32make.exe fetch-dlls
```

To će preuzeti RocksDB i SQLite baze podataka i staviti ih u mapu `build` odakle će im Nimbus moći pristupiti.

## Pokretanje

Da izgradite Nimbus iz izvornog koda, na Linux / OS X:

```bash
make
```

na Windows:

```bash
make
```

ili 

```bash
mingw32make.exe
```

Nimbus će biti izgrađen i smješten u `build` mapu odakle ga možete pokrenuti naredbom `nimbus` ili `nimbus.exe` u Windows okruženju.

Da pokrenete testove:

```bash
make test # (ili mingw32make.exe test na Windowsima s MinGW)
```

Ako sve prođe kako treba, ekran bi trebao vrištati zelene `[OK]`-ove.

![](../images/06.png)

Da ažuirate datoteke za ponovnu izgradnju ako je došlo do nekih promjena:

```bash
make update
```

Da počistite izgrađene datoteke i počnete ispočetka:

```bash
make clean
```

## Ethereum 2.0

Da pokrenete i testirate Ethereum 2.0 verziju Nimbusa (simulaciju mreže):

```bash
make eth2_network_simulation
```

Pokrenuti će se nekoliko Beacon čvorova i validatori će probati izgraditi beacon lanac. Više o samoj simulaciji pogledajte u [ovom članku](https://our.status.im/nimbus-testnet-simulation).

Da počnete simulaciju ispočetka i generirate sve datoteke iz nule:

```bash
make clean_eth2_network_simulation_files
```

---

Čestitamo! Uspješno ste izgradili i pokrenuli Nimbus, donekle. U budućim člancima raščlaniti ćemo neke testove, pogledati kako najlakše pridonijeti Nimbusu, uroniti u Nim jezik i još mnogo toga.
