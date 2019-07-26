---
title: Globální distribuce s Azure Cosmos DB – pod kapotou
description: Tento článek obsahuje podrobné technické informace o globální distribuci služby Azure Cosmos DB
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: 849c3a745de08e7cf8ff7f1b8bb237a6d0f54395
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384157"
---
# <a name="global-data-distribution-with-azure-cosmos-db---under-the-hood"></a>Globální distribuce dat pomocí Azure Cosmos DB – pod kapotou

Azure Cosmos DB je základní služba v Azure, která se nasazuje napříč všemi oblastmi Azure po celém světě, včetně veřejných, svrchovaného, ministerstva obrany a státních cloudů. V rámci datového centra můžeme nasadit a spravovat služby Azure Cosmos DB na velkém razítka počítačů, každý s vyhrazenou místního úložiště. V rámci datového centra Azure Cosmos DB je nasadit napříč mnoha clusterech je každý potenciálně spuštění více generacemi hardwaru. Počítače v clusteru jsou obvykle rozloženy mezi 10-20 domén selhání pro zajištění vysoké dostupnosti v rámci oblasti. Následující obrázek ukazuje topologie systému globální distribuce služby Cosmos DB:

![Topologie systému](./media/global-dist-under-the-hood/distributed-system-topology.png)

**Globální distribuce v Azure Cosmos DB je klíč:** Po několika kliknutích nebo programově s jedním voláním rozhraní API můžete přidat nebo odebrat geografické oblasti přidružené k databázi Cosmos. Databáze Cosmos se zase skládá ze sady Cosmos kontejnerů. Ve službě Cosmos DB kontejnery slouží jako logické jednotky distribuci a škálovatelnost. Kolekce, tabulky a grafy, které vytvoříte jsou (interní) právě Cosmos kontejnery. Kontejnery jsou zcela nezávislá schématu a poskytují obor dotazu. Data v kontejneru Cosmos automaticky indexuje po ingestování. Automatické indexování umožňuje uživatelům dotazování na data bez starostí se správou schématu nebo indexu, zejména při globálně distribuované instalaci.  

- V dané oblasti jsou data v kontejneru distribuována pomocí klíče oddílu, který zadáte a je transparentně spravován základními fyzickými oddíly (*místní distribuce*).  

- Každý fyzický oddíl je také replikován v rámci geografických oblastí (*globální distribuce*). 

Když aplikace používající Cosmos DB elasticky škáluje propustnost Cosmos kontejneru nebo spotřebovává více úložiště, Cosmos DB transparentně zpracovává operace správy oddílů (rozdělené, klonování, odstraňování) napříč všemi oblastmi. Bez ohledu na škálování, distribuci nebo selhání, Cosmos DB nadále bude poskytovat jeden systémový obraz data v kontejnerech, které jsou globálně distribuované napříč libovolným počtem oblastí.  

Jak je znázorněno na následujícím obrázku, data v kontejneru jsou distribuována do dvou dimenzí – v oblasti a oblastech, po celém světě:  

![Fyzické oddíly](./media/global-dist-under-the-hood/distribution-of-resource-partitions.png)

Fyzický oddíl je implementován skupinou replik, která se nazývá *sada replik*. Každý počítač hostuje stovky replik, které odpovídají různým fyzickým oddílům v rámci pevně stanovené sady procesů, jak je znázorněno na obrázku výše. Repliky odpovídající na fyzické oddíly jsou umístěné dynamicky a vyrovnáváno zatížení napříč počítači v rámci clusteru a datových center v rámci oblasti.  

Replika jednoznačně patří do tenanta služby Azure Cosmos DB. Každá replika je hostitelem instance služby Cosmos DB [databázový stroj](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), která spravuje prostředky, jakož i přidružené indexy. Databázový stroj Cosmos DB funguje v systému typu atom sekvence záznamů (ARS). Modul je nezávislá k konceptu schématu a rozostří hranice mezi strukturou a hodnotami instancí záznamů. Cosmos DB dosahuje úplného schématu agnosticism automaticky automatického indexování veškerých při ingestování efektivním způsobem, který umožňuje dotazování jejich globálně distribuovaných dat bez nutnosti schéma nebo správu indexů.

Databázový stroj Cosmos se skládá z komponent, včetně implementace několika koordinačních primitivních prvků, jazykových modulů runtime, procesoru dotazů a subsystémů úložiště a indexování zodpovědných za transakční úložiště a indexování dat. přestup. Zajištění vysoké dostupnosti a odolnosti databázový stroj udržuje jeho dat a indexu na jednotkách SSD a replikuje mezi různými instancemi databáze modul v rámci repliku-deklaračních v uvedeném pořadí. Větší klienti odpovídají vyšší škále propustnosti a úložiště a mají buď větší nebo více replik, nebo obojí. Všechny komponenty systému je plně asynchronní – někdy blokuje žádné vlákno a každé vlákno funguje krátkodobou bez dalších nákladů na všechny nepotřebné vlákno přepínače. Omezení četnosti a protitlak jsou zapojena napříč celým spektrem, od řízení přístupu na všech vstupně-výstupní cesty. Databázový stroj Cosmos je navržený tak, aby využil jemně odstupňované souběžnosti a poskytoval vysokou propustnost při provozu v Frugal množství systémových prostředků.

Globální distribuce Cosmos DB spoléhá na dvě abstrakce klíčů – *sady replik* a *sady oddílů*. Sady replik je modulární Lego blok pro koordinaci a sadě oddílů je dynamické překrytí jeden nebo více geograficky distribuované fyzické oddíly. Chcete-li pochopit, jak globální distribuce díla, Nejdřív musíte seznámit tyto dva klíče abstrakce. 

## <a name="replica-sets"></a>Sady replik

Fyzický oddíl je vyhodnocen jako samoobslužná skupina replik s vyrovnáváním zatížení v různých doménách selhání, která se nazývá sada replik. Tato sada souhrnně implementuje protokol replikovaného stavového počítače, aby byla data v rámci fyzického oddílu vysoce dostupná, trvalá a konzistentní. Členství v sadě replik *N* je dynamické – udržuje výkyv mezi *nminimum* a nmaximumy na  základě selhání, operací správy a času, kdy se neúspěšné repliky mají znovu vygenerovat/obnovit. Replikace podle změn členství, protokolu také změní konfiguraci velikost čtení a zápis kvor. Pro jednotnou distribuci propustnosti, která je přiřazená danému fyzickému oddílu, používáme dvě nápady: 

- Za prvé, náklady na zpracování požadavků na zápis na vedoucím jsou vyšší než náklady na použití aktualizací v následném programu. Odpovídajícím způsobem, je vedoucí instancí rozpočtu více systémových prostředků, než sledujícími. 

- Za druhé Pokud je to možné, je čtení kvora pro úrovně konzistence dané složen výhradně z následný repliky. Můžeme vyhnout kontaktování vedoucí pro poskytování čtení, pokud to není vyžadováno. Pro [pět modelů konzistence](consistency-levels.md) , které Cosmos DB podporuje, využíváme řadu nápadů od výzkumu na základě vztahu [zatížení a kapacity](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) v systémech založených na kvoru.  

## <a name="partition-sets"></a>Sad oddílů

Skupina fyzických oddílů, jedna z každé konfigurace s oblastmi databáze Cosmos, se skládá pro správu stejné sady klíčů replikovaných ve všech nakonfigurovaných oblastech. Tato vyšší koordinační primitivum se nazývá *oddíl-set* – geograficky distribuované dynamické překrytí fyzických oddílů, které spravují danou sadu klíčů. I když je daný fyzický oddíl (sada replik) vymezen v rámci clusteru, sada oddílů může zahrnovat clustery, datová centra a geografické oblasti, jak je znázorněno na následujícím obrázku:  

![Sad oddílů](./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png)

Sadu oddílu si lze představit jako rozptýlené "super repliky sadu", který se skládá z několika sady replik, vlastnící stejnou sadu klíčů. Podobně jako u sady replik je členství v sadě oddílů také dynamické – mění se na základě implicitních operací správy fyzického oddílu, které přidávají nebo odebírají nové oddíly do nebo z dané sady oddílů (například při horizontálním navýšení kapacity propustnosti v případě kontejner, přidání nebo odebrání oblasti do databáze Cosmos nebo při výskytu chyby). Vzhledem k tomu, že každý z oddílů (sada oddílů) spravuje členství oddílu v rámci vlastní sady replik, je členství plně decentralizované a vysoce dostupné. Během změny konfigurace sady oddílů je zároveň je stanovené topologie překrytí mezi fyzickými oddíly. Topologie se dynamicky vybere na základě úrovně konzistence, zeměpisné vzdálenosti a dostupné šířky pásma sítě mezi zdrojovým a cílovým fyzickým oddílem.  

Služba umožňuje nakonfigurovat databází Cosmos oblasti zápisu na jeden nebo více oblastí zápisu, a v závislosti na výběru, sad oddílů jsou nakonfigurované tak, aby přijímal zápisy v přesně jednoho nebo všech oblastech. Systém využívá na dvou úrovních vnořený protokol konsensu – jedna úroveň funguje v rámci replik sady repliky fyzického oddílu, který přijímá zápisy, a druhá pracuje na úrovni oddílu, aby poskytovala úplné řazení pro všechny potvrzené zápisy v rámci sady oddílů. Tato shoda vícevrstvého, vnořeného je velmi důležité pro provádění naše přísné smlouvy SLA pro vysokou dostupnost, stejně jako implementace modelů konzistence, které Cosmos DB nabízí svým zákazníkům.  

## <a name="conflict-resolution"></a>Řešení konfliktů

Náš návrh šíření aktualizace, odstraňování konfliktů a sledování příčinnou souvislost inspirován z předchozí pracovní [epidemické algoritmy](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) a [Bayou](https://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf) systému. I když jádrech nápady mají zůstat naživu a poskytují praktický rámec pro komunikaci služby Cosmos DB návrhu systému, také prošly významné transformace jako jsme použili na systém služby Cosmos DB. To bylo potřeba, protože předchozí systémy byly navržené bez zásad správného řízení prostředků ani s škálováním, na kterém Cosmos DB potřebují pracovat, ani poskytovat funkce (například konzistence s ohraničenou neaktuálností) a přísným a komplexní SLA, které Cosmos DB poskytují svým zákazníkům.  

Připomínáme, že sadě oddílů je distribuovaná napříč několika oblastmi a následuje protokol replikace databází Cosmos (více hlavních databází) k replikaci dat mezi fyzickými oddíly obsahující dané sady oddílů. Každý fyzický oddíl (sada oddílů) přijímá zápisy a obsluhuje čtení obvykle pro klienty, kteří jsou místní k této oblasti. Zápisy přijaté fyzickým oddílem v rámci oblasti jsou trvale potvrzené a připravené v rámci fyzického oddílu před potvrzením klientovi. Tyto jsou nezávazně zápisy a se rozšíří na jiných fyzických oddílů v rámci sady oddílu pomocí kanál proti entropie. Klienti mohou požadovat nezávazně nebo potvrzené zápisy předáním hlavičku požadavku. Šíření proti entropie (včetně frekvence šíření) je dynamická, založeny na topologii blízkosti sady oddílů, místní fyzické oddíly a konzistence, nakonfigurovaná úroveň. Cosmos DB v rámci oddílu sady, následuje schéma primární potvrzení se dynamicky vybrané arbiter oddílu. Výběr arbiter je dynamický a je nedílnou součástí Rekonfigurace sady oddílů na základě topologie překrytí. Povolujeme zaručeno potvrzené zápisy (včetně více-row/dávce aktualizací). 

Zavádíme kódovaného vektoru hodiny (obsahující ID oblasti a logické hodiny odpovídající úroveň shody na sady replik a sadě oddílů) pro sledování příčiny a verze aktualizace vektory a vyřešte konflikty. Topologie a algoritmus výběru peer jsou navržený tak, aby pevné a minimální úložiště a sítě minimální režie vektorů verze. Algoritmus zaručuje vlastnost striktní konvergence.  

Pro databáze Cosmos nakonfigurované s využitím více oblastí zápisu systém nabízí celou řadu flexibilní automatické konflikt zásad řešení pro vývojáře lze vybírat, včetně: 

- **Last-Write-WINS (LWW)** , který ve výchozím nastavení používá systémově definovanou vlastnost časového razítka (která je založená na protokolu hodinové synchronizace času). Cosmos DB můžete také zadat jiné vlastní číselné vlastnosti pro řešení konfliktů.  
- **Zásady řešení konfliktů definované aplikací (vlastní)** (vyjádřeno prostřednictvím slučovacích procedur), který je navržen pro sladění sémantiky definované aplikací, které jsou v konfliktu. Tyto postupy získat vyvolána při zjištění konfliktu zápis – zápis pod záštitou databázové transakce na straně serveru. Systém poskytuje přesně jednu jistotu pro provedení slučovací procedury jako součást protokolu závazku. K dispozici je [několik ukázek řešení konfliktů](how-to-manage-conflicts.md) , se kterými můžete hrát.  

## <a name="consistency-models"></a>Modely konzistence

Bez ohledu na to, jestli nakonfigurujete databázi Cosmos s jednou nebo více oblastmi zápisu, můžete si vybrat z pěti jasně definovaných modelů konzistence. V případě více oblastí pro zápis jsou zde tyto významné aspekty úrovní konzistence:  

Hranice konzistence s ohraničenou kostarou se zaručuje, že všechny čtení budou v rámci předpony *KB* nebo *T* sekund z posledního zápisu v jakékoli oblasti. Kromě toho je zaručeno, že čtení s omezenou konzistencí neaktuálnosti budou monotónní a s konzistentními zárukami předpony. Protokol ochrany proti entropie funguje způsobem míra časově omezený a zajistí, že předpony není accumulate a nebude muset použít protitlak na zápisy. Konzistence relace zaručuje monotónní čtení, monotónní zápisu, čtení vlastních zápisů, zápis následuje po čtení a konzistentní záruky předpony, po celém světě. Pro databáze nakonfigurované se silnou konzistencí se nevztahují výhody (nízká latence zápisu, dostupnost vysokého zápisu) více oblastí pro zápis, protože se jedná o synchronní replikaci napříč oblastmi.

Sémantika pěti modelů konzistence v Cosmos DB je popsána [zde](consistency-levels.md)a matematicky popsány pomocí víceúrovňového modelu tla + Specification [.](https://github.com/Azure/azure-cosmos-tla)

## <a name="next-steps"></a>Další postup

Další informace o konfigurace globální distribuce pomocí následujících článcích:

* [Přidat/odebrat oblasti ze svého účtu databáze](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Konfigurování klientů pro vícenásobné navádění](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Jak vytvořit vlastní zásady řešení konfliktů](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
