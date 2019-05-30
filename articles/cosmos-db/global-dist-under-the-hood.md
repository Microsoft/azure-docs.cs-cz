---
title: Globální distribuce pomocí rozhraní Azure Cosmos DB - pod pokličkou
description: Tento článek obsahuje podrobné technické informace o globální distribuci služby Azure Cosmos DB
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: c490657eb67a34e79c8dbaea31cb59b49cc6448e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241096"
---
# <a name="global-data-distribution-with-azure-cosmos-db---under-the-hood"></a>Distribuce globálních dat pomocí služby Azure Cosmos DB - pod pokličkou

Azure Cosmos DB je podkladovou službu v Azure, takže je nasazená ve všech oblastech Azure po celém světě, včetně veřejného, suverénních, ministerstva obrany (DoD) a státních cloudů. V rámci datového centra můžeme nasadit a spravovat služby Azure Cosmos DB na velkém razítka počítačů, každý s vyhrazenou místního úložiště. V rámci datového centra Azure Cosmos DB je nasadit napříč mnoha clusterech je každý potenciálně spuštění více generacemi hardwaru. Počítače v rámci clusteru jsou typicky rozděleny mezi 10-20 domén selhání pro zajištění vysoké dostupnosti v rámci oblasti. Následující obrázek ukazuje topologie systému globální distribuce služby Cosmos DB:

![Topologie systému](./media/global-dist-under-the-hood/distributed-system-topology.png)

**Globální distribuce ve službě Azure Cosmos DB je na klíč:** V každém okamžiku jen několika kliknutími nebo programově pomocí jediného volání rozhraní API můžete přidat nebo odebrat zeměpisné oblasti přidružené k databázi Cosmos. Databáze Cosmos, pak sestává ze sady Cosmos kontejnerů. Ve službě Cosmos DB kontejnery slouží jako logické jednotky distribuci a škálovatelnost. Kolekce, tabulky a grafy, které vytvoříte jsou (interní) právě Cosmos kontejnery. Kontejnery jsou zcela nezávislý na schématu a zadejte rozsah dotazu. Data v kontejneru Cosmos automaticky indexuje po ingestování. Automatické indexování umožňuje uživatelům zadávat dotazy na data bez starostí o schéma nebo správu indexů, zejména v globálně distribuované instalaci.  

- V dané oblasti, distribuci dat v rámci kontejneru pomocí oddílu – klíče, které poskytuje a transparentně spravuje základní fyzické oddíly (*místní distribuční*).  

- Jednotlivé fyzické oddíly se také replikuje napříč zeměpisnými oblastmi (*globální distribuce*). 

Když aplikace pomocí služby Cosmos DB elastické škálování propustnosti kontejneru Cosmos nebo využívá další úložiště, Cosmos DB transparentně zpracovává operace správy oddílů (rozdělení, klonování, odstranění) ve všech oblastech. Bez ohledu na škálování, distribuci nebo selhání, Cosmos DB nadále bude poskytovat jeden systémový obraz data v kontejnerech, které jsou globálně distribuované napříč libovolným počtem oblastí.  

Jak je znázorněno na následujícím obrázku, data v rámci kontejneru se distribuují podél dvou dimenzí – v rámci oblasti a oblastmi po celém světě:  

![Fyzické oddíly](./media/global-dist-under-the-hood/distribution-of-resource-partitions.png)

Fyzický oddíl je implementováno skupina repliky, volá se *sady replik*. Každý počítač hostuje stovky repliky, které odpovídají různé fyzické oddíly v rámci dlouhodobého procesy, jak je znázorněno na obrázku výše. Repliky odpovídající na fyzické oddíly jsou umístěné dynamicky a vyrovnáváno zatížení napříč počítači v rámci clusteru a datových center v rámci oblasti.  

Replika jednoznačně patří do tenanta služby Azure Cosmos DB. Každá replika je hostitelem instance služby Cosmos DB [databázový stroj](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), která spravuje prostředky, jakož i přidružené indexy. Databázový stroj Cosmos DB funguje v systému typu atom sekvence záznamů (ARS). Modul je závislá na konceptu schématu, rozostření hranici mezi strukturu a instance hodnoty záznamů. Cosmos DB dosahuje úplného schématu agnosticism automaticky automatického indexování veškerých při ingestování efektivním způsobem, který umožňuje dotazování jejich globálně distribuovaných dat bez nutnosti schéma nebo správu indexů.

Databázový stroj Cosmos se skládá z komponent včetně provádění několika koordinaci primitiv, jazykové moduly runtime, procesor dotazů a úložiště a indexování subsystémy za transakcí úložiště a indexování dat v uvedeném pořadí. Zajištění vysoké dostupnosti a odolnosti databázový stroj udržuje jeho dat a indexu na jednotkách SSD a replikuje mezi různými instancemi databáze modul v rámci repliku-deklaračních v uvedeném pořadí. Větší tenantů odpovídají škálování propustnosti a úložiště ve větším měřítku a mít větší nebo více replikami, nebo obojí. Všechny komponenty systému je plně asynchronní – někdy blokuje žádné vlákno a každé vlákno funguje krátkodobou bez dalších nákladů na všechny nepotřebné vlákno přepínače. Omezení četnosti a protitlak jsou zapojena napříč celým spektrem, od řízení přístupu na všech vstupně-výstupní cesty. Databázový stroj cosmos je určen využívat jemně odstupňovaná souběžnosti a k zajištění vysoké propustnosti při práci v rámci frugal objemy systémových prostředků.

Globální distribuce cosmos DB spoléhá na dva klíče abstrakce – *sady replik* a *sad oddílů*. Sady replik je modulární Lego blok pro koordinaci a sadě oddílů je dynamické překrytí jeden nebo více geograficky distribuované fyzické oddíly. Chcete-li pochopit, jak globální distribuce díla, Nejdřív musíte seznámit tyto dva klíče abstrakce. 

## <a name="replica-sets"></a>Sady replik

Fyzický oddíl je vyhodnocena jako skupinu samoobslužně spravovaným a dynamicky s vyrovnáváním zatížení rozdělené mezi více domén selhání, názvem sady replik replik. Tato sada souhrnně implementuje protokol počítače replikované stavu tak, aby data v rámci oddílu fyzického vysoce dostupné, odolné a konzistentní vzhledem k aplikacím. Členství sady replik *N* je dynamický – udržuje kolísá mezi *Nminimum* a *Nmaximum* založené na chyby, úkoly správy a čas se nezdařilo repliky pro obnovení/obnovení. Replikace podle změn členství, protokolu také změní konfiguraci velikost čtení a zápis kvor. Propustnost, které je přiřazen k dané fyzickému oddílu rovnoměrně distribuovat, použijeme dvě myšlenky: 

- Nejprve je vyšší než náklady na použití aktualizací na následovník náklady na zpracování požadavky na zápis na vedoucí instancí. Odpovídajícím způsobem, je vedoucí instancí rozpočtu více systémových prostředků, než sledujícími. 

- Za druhé Pokud je to možné, je čtení kvora pro úrovně konzistence dané složen výhradně z následný repliky. Můžeme vyhnout kontaktování vedoucí pro poskytování čtení, pokud to není vyžadováno. Využíváme celou řadou nápady z research na vztah [zatížení a kapacitu](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) v systémech kvora pro [pět modelů konzistence](consistency-levels.md) podporující Cosmos DB.  

## <a name="partition-sets"></a>Sad oddílů

Skupina fyzických oddílů, jeden ze všech nakonfigurovaných s oblastí databáze Cosmos, tvoří ke správě stejnou sadu klíčů replikovat napříč všemi oblastmi nakonfigurované. Tato vyšší primitiva koordinaci se volá *sady oddílů* -geograficky distribuované dynamické překrytí z fyzických oddílů, Správa danou sadu klíčů. Když daný fyzický oddíl (repliky sadu) působí v rámci clusteru, sadě oddílů může zahrnovat clustery, datovými centry a geografických oblastí, jak je znázorněno na následujícím obrázku:  

![Sad oddílů](./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png)

Sadu oddílu si lze představit jako rozptýlené "super repliky sadu", který se skládá z několika sady replik, vlastnící stejnou sadu klíčů. Podobně jako sady replik oddílu sadu pro členství je dynamický – kolísá podle operace správy implicitní fyzický oddíl přidání nebo odebrání nových oddílů do a z daného oddílu sady (například při horizontálním navýšením kapacity propustnosti na kontejner, přidat nebo odebrat a oblasti do databáze Cosmos, nebo když dojde k selhání). Tím, že s všech oddílů (z oddílu sadu), správa sady oddílů členství v rámci své vlastní sady replik, členství je plně decentralizované a s vysokou dostupností. Během změny konfigurace sady oddílů je zároveň je stanovené topologie překrytí mezi fyzickými oddíly. Topologie se dynamicky určí úrovně konzistence, geografické vzdálenosti a dostupnou šířku pásma sítě mezi zdrojovým a cílové fyzické oddíly.  

Služba umožňuje nakonfigurovat databází Cosmos oblasti zápisu na jeden nebo více oblastí zápisu, a v závislosti na výběru, sad oddílů jsou nakonfigurované tak, aby přijímal zápisy v přesně jednoho nebo všech oblastech. Systém využívá protokol dvouúrovňová, vnořené caiq – jednu úroveň pracuje v rámci repliky sady replik oddílu fyzického přijímá zápisy a druhý funguje na úrovni sadě oddílů poskytnout úplné záruky pořadí pro všechny Potvrdit zápisy v rámci sady oddílů. Tato shoda vícevrstvého, vnořeného je velmi důležité pro provádění naše přísné smlouvy SLA pro vysokou dostupnost, stejně jako implementace modelů konzistence, které Cosmos DB nabízí svým zákazníkům.  

## <a name="conflict-resolution"></a>Řešení konfliktů

Náš návrh šíření aktualizace, odstraňování konfliktů a sledování příčinnou souvislost inspirován z předchozí pracovní [epidemické algoritmy](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) a [Bayou](https://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf) systému. I když jádrech nápady mají zůstat naživu a poskytují praktický rámec pro komunikaci služby Cosmos DB návrhu systému, také prošly významné transformace jako jsme použili na systém služby Cosmos DB. To bylo nutné, protože předchozí systémy byly navrženy s zásady správného řízení prostředků ani v měřítku, jakou potřebuje Cosmos DB fungovat ani k poskytování možností (například konzistenci omezená neaktuálnost) a přísné a komplexní smlouvy SLA, které poskytuje služby Cosmos DB svým zákazníkům.  

Připomínáme, že sadě oddílů je distribuovaná napříč několika oblastmi a následuje protokol replikace databází Cosmos (více hlavních databází) k replikaci dat mezi fyzickými oddíly obsahující dané sady oddílů. Jednotlivé fyzické oddíly (z oddílu sadu) přijímá zápisu a obvykle slouží čtení na klienty, kteří jsou místní pro tuto oblast. Zápisy přijal fyzický oddíl v rámci oblasti jsou trvale potvrzena a nastavit na vysokou dostupnost v rámci oddílu fyzického předtím, než budou potvrzeny do klienta. Tyto jsou nezávazně zápisy a se rozšíří na jiných fyzických oddílů v rámci sady oddílu pomocí kanál proti entropie. Klienti mohou požadovat nezávazně nebo potvrzené zápisy předáním hlavičku požadavku. Šíření proti entropie (včetně frekvence šíření) je dynamická, založeny na topologii blízkosti sady oddílů, místní fyzické oddíly a konzistence, nakonfigurovaná úroveň. Cosmos DB v rámci oddílu sady, následuje schéma primární potvrzení se dynamicky vybrané arbiter oddílu. Výběr arbiter je dynamický a je nedílnou součástí Rekonfigurace sady oddílů na základě topologie překrytí. Povolujeme zaručeno potvrzené zápisy (včetně více-row/dávce aktualizací). 

Zavádíme kódovaného vektoru hodiny (obsahující ID oblasti a logické hodiny odpovídající úroveň shody na sady replik a sadě oddílů) pro sledování příčiny a verze aktualizace vektory a vyřešte konflikty. Topologie a algoritmus výběru peer jsou navržený tak, aby pevné a minimální úložiště a sítě minimální režie vektorů verze. Algoritmus zaručuje vlastnost striktní konvergence.  

Pro databáze Cosmos nakonfigurované s využitím více oblastí zápisu systém nabízí celou řadu flexibilní automatické konflikt zásad řešení pro vývojáře lze vybírat, včetně: 

- **Služby Wins posledního zápisu (LWW)** , který, ve výchozím nastavení, používá vlastnost definovaná systémem časové razítko (která je založená na protokolu synchronizaci času hodin). Cosmos DB můžete také zadat jiné vlastní číselné vlastnosti pro řešení konfliktů.  
- **Definované aplikací (vlastní) konflikt zásada řešení** (vyjádřeno prostřednictvím procedury sloučení), která je navržená pro odsouhlasení definovaného aplikací sémantiku a je v konfliktu. Tyto postupy získat vyvolána při zjištění konfliktu zápis – zápis pod záštitou databázové transakce na straně serveru. Poskytuje tento systém přesně jednou zaručit k provádění procedury sloučení jako součást závazku protokolu. Existují [několik ukázek řešení konfliktu](how-to-manage-conflicts.md) si můžete pohrát s.  

## <a name="consistency-models"></a>Modely konzistence

Zda databáze Cosmos nakonfigurovat jeden nebo více oblastí zápisu, můžete vybrat z pět jasně definované modely konzistence. S využitím více oblastí zápisu tady jsou několik důležitých aspektů úrovně konzistence:  

Konzistenci omezená neaktuálnost zaručuje, že všechny operace čtení se během *K* předpony nebo *T* sekund od poslední zápis v libovolné oblasti. Kromě toho čtení pomocí konzistenci omezená neaktuálnost je zaručena monotónní a konzistentní předpona záruky. Protokol ochrany proti entropie funguje způsobem míra časově omezený a zajistí, že předpony není accumulate a nebude muset použít protitlak na zápisy. Záruky konzistence relace monotónní čtení, monotónní zápis čtení vlastních zápisů, zápis následuje čtení a konzistentní předpona zaručuje po celém světě. Pro databáze nakonfigurované s silnou konzistenci výhody více oblastí zápisu (zápisu s nízkou latencí, zápis vysoké dostupnosti) se nevztahuje, z důvodu synchronní replikace mezi oblastmi.

Sémantika pět modelů konzistence ve službě Cosmos DB jsou popsány [tady](consistency-levels.md)a matematicky popisují pomocí základní specifikace TLA + [tady](https://github.com/Azure/azure-cosmos-tla).

## <a name="next-steps"></a>Další postup

Další informace o konfigurace globální distribuce pomocí následujících článcích:

* [Přidat/odebrat oblasti ze svého účtu databáze](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Konfigurování klientů pro vícenásobné navádění](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Jak vytvořit zásady překladu IP adres vlastní konflikt](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
