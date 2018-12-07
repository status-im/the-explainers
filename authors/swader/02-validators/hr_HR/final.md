# Dva Točka Nula: Što su Ethereum 2.0 Validatori?

Ethereum 2.0. 

Komplicirana i teško shvatljiva nadogradnja Ethereum [blockchaina](https://bitfalls.com/hr/2017/08/20/blockchain-explained-blockchain-works/) o kojoj mnogi pričaju a malo tko je razumije dublje od press releasova i površnih pregleda kakve objavljuju popularne publikacije.

Dva. Točka. Nula.

Duge riječi, mistične fraze, luda obećanja i imena kao iz Power Rangers serijala - sve to su česte pojave u Ethereum 2.0 nomenklaturi: Beacon Chain, Slasher, Validator, Randao, BLS (Boneh-Lynn-Shacham) potpisi, kristalizacija stanja... pogledajte i sami.

![Komplicirano](../images/01.png)

Kako da prosječni korisnik zaista shvati sve ovo? Posebice ako uzmemo u obzir česte izmjene u planovima razvoja i veliko spajanje Caspera i Shardinga u zajedničku fazu "Shasper"?

Ovo je prvi u serijalu članaka o Ethereumu 2.0 iz perspektive [Nimbus](https://bitfalls.com/nimbus) tima. Članci će podijeliti Ethereum 2.0 u manje, lakše razumljive cjeline i pojasniti svaku, pritom držeći sadržaj ažurnim kako se stvari mijenjaju. Pokriti ćemo i način na koji Nimbus implementira tematiku pojedinog članka.

Znanje koje vam je potrebno za razumijevanje ovog sadržaja je osnovno - možda nešto o [Ethereumu općenito](https://bitfalls.com/hr/2017/09/19/what-ethereum-compare-to-bitcoin/) i razlika [Proof of Stake i Proof of Work](https://bitfalls.com/hr/2018/04/24/whats-the-difference-between-proof-of-work-pow-proof-of-stake-pos-and-delegated-pos/).

## Dva Točka Nula općenito

Objasniti ćemo koncept Validatora u novom sustavu, no za početak pokrijmo neke osnove plana implementacije.

![Kada](../images/02.png)

## Lekcija 1: Serenity, ne Shasper

Na Devcon4 konferenciji ove godine, Vitalik je izrazio nezadovoljstvo terminom _Shasper_ (termin kojeg je zajednica izabrala za spajanje Caspera i Shardinga u jedan zajednički projekt), te je zamolio zajednicu da novi Ethereum nazivaju Serenity - pravim imenom nadogradnje koja nas čeka.

## Lekcija 2: Više faza

Serenity dolazi u više faza - točnije 4 općenite faze koje će zajedno trajati nekih 2 godine.

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">Great progress and roll out plan for <a href="https://twitter.com/hashtag/Ethereum?src=hash&amp;ref_src=twsrc%5Etfw">#Ethereum</a> 2.0 <a href="https://twitter.com/hashtag/devcon4?src=hash&amp;ref_src=twsrc%5Etfw">#devcon4</a> <a href="https://t.co/YwExJSu5AQ">pic.twitter.com/YwExJSu5AQ</a></p>&mdash; Pelle Brændgaard (@PelleB) <a href="https://twitter.com/PelleB/status/1057581668976885760?ref_src=twsrc%5Etfw">October 31, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

- Prva faza koju nazivamo Faza 0 (mi developeri volimo brojati od nule) ili _[beacon chain]((https://our.status.im/two-point-oh-the-beacon-chain/))_ faza fokusirati će se na Proof of Stake stranu novog blockchaina i Validatore
- Druga faza dodati će _shardove_ kao podatkovni sloj, nešto što ćemo objasniti u zasebnom članku
- Treća faza dopustiti će transfer podataka preko _shardova_, tj. pametne ugovore. Da, to znači da neko vrijeme pametni ugovori neće postojati u novom Ethereumu, pa ćemo i dalje ovisiti o trenutnom.
- Četvrta faza će, kako je Vitalik sam kazao, biti gotovo u cijelosti optimizacije i nadogradnje svih dosadašnjih faza - usavršavanje svjetskog računala koje smo izgradili


## Lekcija 3: Validatori

Validator je entitet koja može predlagati nove blokove za novi Ethereum blockchain, ili potvrditi tuđe predložene blokove kao validne. Da ne bi bilo zabune - radi se o automatiziranom softveru, ne manualnom predlaganju. Naravno, tehnički je moguće ručno predlagati blokove i potpisivati transakcije no u stvarnosti ne biste bili dovoljno brzi da konkurirate softveru. Za svrhu daljnjeg objašnjavanja, držimo se sljedeće definicije: validator je jedan "čvor", _jedno računalo_ (nazovimo ga 1C - 1 computer) koje radi to predlaganje ili potvrđivanje.

Validator koji predlaže zove sve _proposer_, dok validatora koji potvrđuje nazivamo _attester_.

Ovdje se termini još malo granaju.

Validatore izabiremo iz velikog popisa validatora registriranih na nečemu što zovemo [beacon chain](https://our.status.im/two-point-oh-the-beacon-chain/). Bira ih funkcija za nasumični odabir koju nazivamo RanDAO+VDF i podskup validatora koji postaju kandidati za sljedeće prijedloge blokova zajedno nazivamo _committee_.

Kad se neki validatori odaberu za članstvo u _committee_, odgovorni su za _atestiranje stanja_ tj. izgradnju novog bloka. To se dešava u vremenskom okviru kojeg nazivamo _slot_ (jedan slot znači jedan prijedlog za novi blok i potvrde tj. atestacije od drugih validatora za taj isti blok), dok skup slotova kroz koji svi validatori u _committee_-u imaju priliku predložiti novi blok nazivamo _cycle_ (ciklus).

Mnogo termina?

![Zbunjola](../images/03.png)

Ne brinite - to je to od definicija. Preostala je još samo jedna stvar koju trebate znati o validatorima - kako im se pridružiti.

### Kako postati validator

Da biste postali validator potrebno je napraviti depozit od točno 32 ethera u pametni ugovor na proof-of-work Ethereum blockchainu. Taj depozit tada generira svojevrsnu dozvolu da se adresa koja uplaćuje pridruži timu validatora. Validatori će biti odgovori za jedan do dva _sharda_ (koji dolaze u kasnijoj fazi Serenity nadogradnje) na kojima će predlagati i atestirati blokove. Drugim riječima, 1C resursa (jedno standardno računalo) moći će predlagati blokove odnosno atestirati na jednom do dva _sharda_. Ako imate više od 32 ethera za staking, tada vam je potrebno još 1C resursa. Staking time postaje nepraktičan za _poolove_ - skupove "rudara" koji zajednički stake-aju ether, pojačavajući tako decentralizaciju mreže.

![Decentralizacija](../images/04.png)

Cilj je da PoW i PoS sustav žive jedan uz drugog neko vrijeme jer, kao što smo objasnili u tekstu gore, prve dvije faze Serenitija neće uopće imati mogućnost prijenosa podataka pa ćemo i dalje ovisiti o PoW lancu da procesuira naše transakcije. Tranzicija će biti postepena što znači da rudari neće naglo ostati bez posla. Štoviše, situacija se za njih pozitivno razvija - to je nešto o čemu ćemo pričati u ProgPow članku nešto kasnije.

"Kako dugo mogu biti validator? I što je s onim slashingom i gubljenjem stake-anog ethera? I što to validatori validiraju ako nema prijenosa podataka među pametnim ugovorima ili računima u novom PoS lancu?"

Polako, znatiželjni čitatelju. Sve ćemo to objasniti.

1. Validator može ostati u sustavu u nedogled, tako dugo dok poštuje pravila sustava.
2. Kod gubljenja veze, validator će s vremenom gubiti dio svog uloga (32 eth). Taj će se gubitak eksponencijalno povećavati s duljinom gubitka veze, no kraći će periodi biti gotovo potpuno oprošteni (specifičnosti se još ne znaju). Validator neće izgubiti sav svoj ulog, već će mu se ulog smanjivati do neke granice i tada će biti izbačen iz skupa validatora, a ostatak ethera (npr. 16 eth) moći će podići tek nakon nekoliko tjedana ili mjeseci. Validator može izgubiti ulog i kod nepoštivanja pravila. Pritom je kazna puno ozbiljnija i gubici su puno brži. Gubitak stakea (uloga) zovemo _slashing_ (jer se _reže_), i algoritam koji to radi zove se Slasher.
3. Novi blockchain biti će praktički prazan u početku. Budući da neće postojati nikakvi podaci na blockchainu, neće se imati što obraditi pa će prve dvije faze omogućiti isključivo komunikaciju između čvorova bez nekih konkretnih podataka.

## Nimbusova implementacija

Validatori su velik dio [beacon chain](https://our.status.im/two-point-oh-the-beacon-chain/) implementacije i Nimbus tim radi na demonstraciji čvora koji uspješno održava vezu s drugim čvorovima (i čvorovima drugih timova) i vrti [beacon chain](https://our.status.im/two-point-oh-the-beacon-chain/).  Demonstracija bi dijelom trebala biti spremna u prosincu 2018, a u prvoj ozbiljnijoj inačici u ožujku 2019.

![Networking](../images/05.png)

## Zaključak

Validator je nešto poput modernog "rudara", ali rudara koji ne troši apsurdne količine električne energije da potvrdi transakcije - umjesto toga, validator gradi blokove iz transakcije ako ga sustav za to odabere i ako njegovu izgradnju bloka potvrde drugi validatori u grupi. Te grupe validatora zajedno kreiraju novi lanac i ni jedan validator nije moćniji od drugih. Da biste postali validator potrebno je poslati 32 eth u pametni ugovor na trenutnom Proof of Work lancu koji generira "račun" kojim se garantira ulaz u set validatora na [beacon lancu](https://our.status.im/two-point-oh-the-beacon-chain/).