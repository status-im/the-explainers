Ovaj je članak dio [Dva Točka Nula](https://bitfalls.com/hr/tag/two-point-oh/) serijala u kojem objašnjavamo koncepte iz Ethereuma 2.0, sada poznatog pod imenom Serenity. U ovom ćemo dijelu objasniti koncept Beacon lanca.

Znanje koje vam je potrebno za razumijevanje sadržaja koji slijedi je samo ovaj [kratki članak o validatorima](https://bitfalls.com/hr/2018/11/29/dva-tocka-nula-sto-su-to-ethereum-validatori/). Do sada vjerojatno znate razliku proof of stake i proof of work algoritma za osiguranje mreže, ali ako vam je i to nepoznato, svakako preporučamo pročitati [ovaj uvod](https://bitfalls.com/hr/2018/04/24/whats-the-difference-between-proof-of-work-pow-proof-of-stake-pos-and-delegated-pos/).

Zavalite se u fotelju: noge u zrak i kava u ruke - naučimo nešto novo!

![Kava spremna?](/wp-content/uploads/2018/12/01.png)

### Beacon Lanac

**Beacon lanac je novi blockchain** u središtu sljedeće verzije Ethereuma.

Jedna od odgovornosti ovog lanca je bilježenje aktivnih [Validatora](https://bitfalls.com/hr/2018/11/29/dva-tocka-nula-sto-su-to-ethereum-validatori/) koji grade blockchain umjesto trenutnih rudara. Druga je spremanje referenci na stanje _shardova_.

#### Odgovornost 1: Portal za Staking

Ulazak u Proof of Stake raditi će se preko _registracijskog ugovora_ na trenutnom Proof of Work Ethereum lancu jer je u planu da ta dva lanca žive jedan uz drugog neko dogledno vrijeme (cca 2 godine).

Taj će pametni ugovor prihvaćati točno 32 ethera kroz jednu registracijsku funkciju koja će emitirati "event" (događaj) koji će biti ekvivalentan članskoj iskaznici tog validatora. Bitno je napomenuti da će ovo biti jednosmjerna razmjena, dakle, možete samo poslati 32 ethera na beacon chain registracijski ugovor, ne i nazad. I ako vam to zvuči čudno i kao da će istovremeno postojati dva različita ethera, u pravu ste, no više o tome u [priči o dva ethera](https://our.status.im/two-point-oh-the-tale-of-two-ethers/).

Napomenimo i da će se nakon pune tranzicije u PoS sustav tj. jednom kad se PoW lanac prebaci u _legacy shard_ ili arhivni ugovor (više o tome u _Dva Točka Nula: Legacy Shard_) sve ovo odvijati isključivo na beacon chainu i generalno biti puno uređenije i manje konfuzno.

Jednom kad je registriran na beacon lancu, validator može biti nasumično odabran da validira jedan do dva _sharda_ (za detalje o tom aspektu, vidi članak o validatorima). Validator će spajati podatke iz shardova kojima je dodijeljen s podacima s Beacon lanca i predlagati novi block ili potvrditi / odbiti blokove nekih drugih validatora, tako dugo dok sam taj validator ne dođe na red da predlaže (jedan ciklus).

![Produljivanje lanca](/wp-content/uploads/2018/12/02.png)

Da ponovimo: beacon chain nema _podatke_ - neće spremati stanja računa, lokacije tokena, stanje dappova niti bilo kakve druge informacije koje Ethereum lanac trenutno sprema. Prvo, sprema popis validatora u sustavu. Drugo, sprema _atestacije_.

#### Odgovornost 2: Spremanje atestacija

Čega...?

_Atestacije_ su potvrđeni i od strane validatora potpisani hashevi koji predstavljaju trenutno stanje nekog _sharda_. Pojednostavimo tu kompleksnu tvrdnju.

Kada nešto _hashirate_, pretvarate to u niz slova i brojki koji se u potpunosti mijenja kad se u originalu desi i najmanja promjena. Npr. ako otvorite ovaj [hash generator](https://passwordsgenerator.net/sha256-hash-generator/) i napišete "Hell", hash ispod polja za upis biti će potpuno drugačiji ako dodate "o" da biste dobili "Hello".

![](/wp-content/uploads/2018/12/hash.gif)

Ako promijenite i samo jedan jedini piksel u Full HD slici i hashirate je, hash će biti potpuno drugačiji od onoga koji dobivate kada hashirate originalnu sliku. To znači da i najmanja promjena uzrokuje vrlo očite efekte.

Da ne ulazimo previše u koncept shardova, smatrajte ih podacima na kojima izvršavamo određene operacije (poput promjena stanja računa, popisa tko ima koliko tokena, promjena vlasništva [NFTova](https://bitfalls.com/hr/2018/10/15/the-last-nft-non-fungible-token-explanation-post-youll-ever-need/) itd.). Shardovi su blockchainov _tvrdi disk_. Svaki put kada se stanje nekog sharda promijeni - čak i ako se radi o samo jednom računu - njegov se hash mijenja u potpunosti. Taj se hash sprema na beacon lancu kao kontrolna točka (atestacija) za trenutno stanje tog sharda. Drugim riječima, kad god se dogodi neka promjena u podacima nekog sharda, ta se promjena sprema na beacon lanac u obliku novog hasha. Time shardovi mogu pratiti međusobne promjene preko beacon lanca što omogućuje asinkronu komunikaciju između shardova. Zašto je to bitno i kako funkcionira biti će objašnjeno u _Dva Točka Nula: Sharding_ članku.

Odavde dolazi _beacon_ (svjetiljka) u _beacon lancu_. Kao svjetionik u maglovitoj luci, beacon u beacon chainu pomaže shardovima da se međusobno nađu i validatorima da ih obrađuju.

### Beacon čvorovi i Validatori

Sada se vjerojatno pitate kako će sve to utjecati na [čvorove za rudarenje i validaciju](https://bitfalls.com/hr/2017/11/26/whats-bitcoin-node-mining-vs-validation/), i mijenja li se i njihova terminologija. Odgovor je: da 😱

Postoje dva tipa "čvorova" kojih sada treba biti svjestan, svaki s podtipovima koje nećemo pokriti u ovom članku.

**Beacon čvorovi** _validiraju validatore_, spajaju njihove potpise (tj. kad se radi o mnogo validatora, oni šalju svoje javne ključeve koje je potrebno spojiti i obraditi odjednom), preuzimaju potrebne podatke s kojima gradimo nove blokove i šalju te podatke validatorima. Beacon čvorovi su ekvivalentni rudarima u trenutnom sustavu, ali za to ne bivaju nagrađeni. Isplativost pokretanja beacon čvora je još uvijek [kontroverzna i često raspravljana tema](https://github.com/ethereum/eth2.0-specs/issues/157) u Ethereum 2.0 krugovima.

**Validator klijenti** mogu, s druge strane, u teoriji biti jako maleni programi koji se vrte čak i na mobilnim uređajima - poput onoga kakav se gradi u [Nimbus](https://bitfalls.com/hr/2018/11/10/nimbus-for-newbies/)-u. To su programi čija je jedina funkcija da ostanu spojeni na internet i drže Ether _stakeanim_ (uloženim) - stabilna internet veza je potrebna jer validator treba redovito potpisivati blokove i atestacije i slati ih u mrežu za provjeru drugima. Validatori grade blokove od podataka koje im prosljeđuje beacon čvor na koji su povezani. Validatori također trpe posljedice izgradnje krivih blokova, ili bivanja offline: u tom se slučaju dešava _slashing_ - rezanje uloga, tj. gubljenje dijela uloga od 32 ethera. Validatori u početku neće raditi na nečemu poput mobitela (često pisanje na i čitanje s diska ubilo bi i disk i bateriju). Na optimizacijama će se raditi kasnije.

Postoje i drugi tipovi čvorova - "laki" klijenti, "ultralaki" klijenti, stateless čvorovi, čvorovi punih arhiva koji služe za vraćanje podataka koji su odstranjeni iz blockchaina zbog neplaćanja najma prostora (više o tome u _Dva Točka Nula: Najam_), itd. Kroz sve njih proći ćemo detaljno.

## Nimbusova implementacija

Nimbus tim trenutno radi na više komponenti Beacon Chain [specifikacije](https://github.com/ethereum/eth2.0-specs/blob/master/specs/beacon-chain.md).

Glavnina odrađenog posla je u [nim-beacon-chain](https://github.com/status-im/nim-beacon-chain) repozitoriju koji je odvojen od glavnog Nimbus repozitorija jer je potonji zasebna implementacija za testiranje. Dok spajanje s klijentima drugih timova kreće u proljeće 2019., Ethereum 2.0 strana Nimbusa može se pokrenuti već sada - više o tome saznajte u našim [člancima o napredku razvoja](https://our.status.im/nimbus-development-update-feb-2019/).

Trenutno najveće blokade kod finalizacije Nimbusa su nedefinirani dijelovi specifikacije. Svi timovi koji trenutno rade na klijentima za Ethereum 2.0 protokol žele kvalitetnu komunikaciju kako između članova tako i između klijenata i bez detaljnih specifikacija to nije moguće. Šema postizanja nasumičnosti (vidi _Dva Točka Nula: nasumičnost_) također nije potpuno razjašnjena. Nadalje, ni način na koji se bira koji lanac odabrati u slučaju konfliktnih blokova nije u potpunosti definiran.

Trenutno svi timovi rade ovo:

<iframe src="https://giphy.com/embed/WkXTjAYa7b34A" width="403" height="480" frameBorder="0" class="giphy-embed" allowFullScreen></iframe>

## Zaključak

U prva dva dijela Dva Točka Nula serijala bilo je mnogo sadržaja za absorbirati - Validatori i Beacon lanac su u srži novog Ethereuma i bitno je da ih shvatite u potpunosti.

Ubuduće, članci će biti daleko manji i specifičniji ali generalni aspekti novog sustava predstavljenog do sada vjerojatno neće prolaziti kroz veće promjene i čine dobru podlogu za objašnjavanje tih koncepata drugima - probajte! Ukoliko još uvijek nešto ne shvaćate, slobodno me kontaktirajte [na Twitteru](https://twitter.com/bitfalls).

Ukratko: Beacon chain je glavni lanac Ethereuma 2.0 na koji se svi _shardovi_ spajaju i preko kojeg komuniciraju. Kroz Beacon lanac se ulazi u _staking_ sustav, sustav ulaganja (vidi Dva Točka Nula: Ekonomija), ali taj je lanac isto tako način na koji se postiže komunikacija među shardovima koji služe kao baza podataka blockchaina i spremaju svoje hasheve u beacon lanac. Beacon čvorovi su graditelji novih blokova preko validatora koji ih predlažu ili potvrđuju.

U sljedećem dijelu objasniti ćemo [priču o dva ethera](https://our.status.im/two-point-oh-the-tale-of-two-ethers/) (i ne, ne mislim pritom na Ethereum Classic!)
