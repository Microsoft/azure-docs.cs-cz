---
title: Globální distribuce s Azure Cosmos DB - pod kapotou
description: Tento článek obsahuje technické podrobnosti týkající se globální distribuce Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: a46a69476a2ad6550bc7b3a533fd09565d461db3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74872124"
---
# <a name="global-data-distribution-with-azure-cosmos-db---under-the-hood"></a>Globální distribuce dat s Azure Cosmos DB – pod kapotou

Azure Cosmos DB je základní služba v Azure, takže se nasazuje ve všech oblastech Azure po celém světě, včetně veřejných, suverénních, ministerstva obrany (DoD) a vládních cloudů. V rámci datového centra nasazujeme a spravujeme Azure Cosmos DB na masivních známkách počítačů, z nichž každý má vyhrazené místní úložiště. V rámci datového centra se Azure Cosmos DB nasazuje v mnoha clusterech, z nichž každá může spouštět více generací hardwaru. Počítače v rámci clusteru jsou obvykle rozloženy do 10 až 20 domén selhání pro vysokou dostupnost v rámci oblasti. Následující obrázek znázorňuje topologii globálního distribučního systému Cosmos DB:

![Topologie systému](./media/global-dist-under-the-hood/distributed-system-topology.png)

**Globální distribuce v Azure Cosmos DB je na klíč:** Kdykoli, s několika kliknutími nebo programově s jedním voláním rozhraní API, můžete přidat nebo odebrat geografické oblasti přidružené k databázi Cosmos. Databáze Cosmos, zase se skládá ze sady kontejnerů Cosmos. V Cosmos DB kontejnery slouží jako logické jednotky distribuce a škálovatelnost. Kolekce, tabulky a grafy, které vytvoříte, jsou (interně) pouze kontejnery Cosmos. Kontejnery jsou zcela šetrné a gnostik a poskytují obor pro dotaz. Data v kontejneru Cosmos se automaticky indexují při požití. Automatické indexování umožňuje uživatelům dotazovat data bez potíží správy schématu nebo indexu, zejména v globálně distribuovaném nastavení.  

- V dané oblasti jsou data v rámci kontejneru distribuována pomocí klíče oddílu, který zadáte a je transparentně spravován základními fyzickými oddíly (*místní distribuce*).  

- Každý fyzický oddíl je také replikován napříč geografickými oblastmi *(globální distribuce).* 

Když aplikace pomocí Cosmos DB elasticky škáluje propustnost na kontejneru Cosmos nebo spotřebovává více úložiště, Cosmos DB transparentně zpracovává operace správy oddílů (rozdělení, klonování, odstranění) ve všech oblastech. Nezávisle na škálování, distribuci nebo selhání, Cosmos DB nadále poskytovat jednu bitovou kopii systému dat v rámci kontejnerů, které jsou globálně distribuovány v libovolném počtu oblastí.  

Jak je znázorněno na následujícím obrázku, data v rámci kontejneru jsou distribuována ve dvou dimenzích – v rámci oblasti a napříč oblastmi po celém světě:  

![fyzické oddíly](./media/global-dist-under-the-hood/distribution-of-resource-partitions.png)

Fyzický oddíl je implementován skupinou replik, která se nazývá *sada replik*. Každý počítač hostuje stovky replik, které odpovídají různým fyzickým oddílům v rámci pevné sady procesů, jak je znázorněno na obrázku výše. Repliky odpovídající fyzické oddíly jsou dynamicky umístěny a vyrovnávání zatížení mezi počítači v rámci clusteru a datových center v rámci oblasti.  

Replika jednoznačně patří do tenanta Azure Cosmos DB. Každá replika hostuje instanci [databázového stroje](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)Cosmos DB , který spravuje prostředky i přidružené indexy. Databázový stroj Cosmos pracuje na systému ars (atom-record-sequence). Modul je agnostik konceptu schématu, rozostření hranice mezi strukturou a instance hodnoty záznamů. Cosmos DB dosahuje úplné hořkosti schématu tím, že automaticky indexuje vše při ingestování efektivním způsobem, což umožňuje uživatelům dotazovat jejich globálně distribuovaná data bez nutnosti zabývat se správou schématu nebo indexu.

Databázový stroj Cosmos se skládá z komponent, včetně implementace několika koordinačních primitiv, modulů za běhu jazyků, zpracovatele dotazů a subsystémů pro ukládání a indexování odpovědných za transakční ukládání a indexování dat, v uvedeném pořadí. Chcete-li poskytnout odolnost a vysokou dostupnost, databázový stroj zachová svá data a index na ssd a replikuje je mezi instancemi databázového stroje v rámci sady replik v uvedeném pořadí. Větší klienti odpovídají vyšší škálování propustnost a úložiště a mají větší nebo více replik nebo obojí. Každá součást systému je plně asynchronní – žádné vlákno nikdy blokuje a každé vlákno provádí krátkodobou práci bez zbytečných přepínačů vláken. Omezení rychlosti a zpětný tlak jsou propukány v celém zásobníku od kontroly vstupu až po všechny cesty vstupně-o. Databázový stroj Cosmos je navržen tak, aby využíval jemně odstupňovanou souběžnost a poskytoval vysokou propustnost při provozu v rámci šikmá množství systémových prostředků.

Globální distribuce Cosmos DB závisí na dvou klíčových abstrakcích – *sadách replik* a *sadách oddílů*. Replika-set je modulární Lego blok pro koordinaci a sada oddílů je dynamické překrytí jednoho nebo více geograficky distribuovaných fyzických oddílů. Abychom pochopili, jak globální distribuce funguje, musíme pochopit tyto dvě klíčové abstrakce. 

## <a name="replica-sets"></a>Sady replik

Fyzický oddíl se zhmotní jako samoobslužná a dynamicky vyrovnávácí skupina replik rozprostřených do více domén selhání, nazývaných sada replik. Tato sada společně implementuje protokol replikovaného stavu počítače, aby byla data v rámci fyzického oddílu vysoce dostupná, trvalá a konzistentní. Členství sady replik *N* je dynamické – stále kolísá mezi *NMin* a *NMax* na základě selhání, operací správy a času pro neúspěšné repliky k obnovení nebo obnovení. Na základě změn členství protokol replikace také překonfiguruje velikost kvor pro čtení a zápis. Chcete-li jednotně distribuovat propustnost, která je přiřazena k danému fyzickému oddílu, používáme dva nápady: 

- Za prvé, náklady na zpracování požadavků na zápis na vedoucíje vyšší než náklady na použití aktualizací na následovníka. Odpovídajícím způsobem, vůdce je rozpočet více systémových prostředků než následovníci. 

- Za druhé, pokud je to možné, čtecí kvorum pro danou úroveň konzistence se skládá výhradně z replik následovníka. Vyhýbáme se kontaktování vedoucího pro doručování čte, pokud to není nutné. Využíváme řadu nápadů z výzkumu provedeného na vztah [zatížení a kapacity](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) v systémech založených na kvoru pro [pět modelů konzistence,](consistency-levels.md) které Cosmos DB podporuje.  

## <a name="partition-sets"></a>Sady oddílů

Skupina fyzických oddílů, jeden z každé nakonfigurované s oblasti databáze Cosmos, se skládá pro správu stejné sady klíčů replikovaných ve všech nakonfigurovaných oblastech. Tento primitivní vyšší koordinace se nazývá *sada oddílů* - geograficky distribuované dynamické překrytí fyzických oddílů, které spravují danou sadu klíčů. Zatímco daný fyzický oddíl (sada replik) je vymezen v rámci clusteru, sada oddílů může span clustery, datová centra a geografické oblasti, jak je znázorněno na obrázku níže:  

![Sady oddílů](./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png)

Sadu oddílů si můžete usuzovat jako geograficky rozptýlenou "sadu super replik", která se skládá z více sad replik, které vlastní stejnou sadu klíčů. Podobně jako sada replik je členství v sadě oddílů také dynamické – kolísá na základě implicitních operací správy fyzických oddílů pro přidání nebo odebrání nových oddílů do/z dané sady oddílů (například při horizontálním navýšení kapacity propustnosti v kontejneru, přidání nebo odebrání oblasti do databáze Cosmos nebo při selhání). Na základě toho, že každý z oddílů (sady oddílů) spravuje členství sady oddílů v rámci vlastní sady replik, členství je plně decentralizované a vysoce dostupné. Během rekonfigurace sady oddílů je také vytvořena topologie překrytí mezi fyzickými oddíly. Topologie je dynamicky vybrána na základě úrovně konzistence, zeměpisné vzdálenosti a dostupné šířky pásma sítě mezi zdrojovým a cílovým fyzickým oddílem.  

Služba umožňuje konfigurovat databáze Cosmos s jednou oblastí zápisu nebo s více oblastmi zápisu a v závislosti na volbě jsou sady oddílů nakonfigurovány tak, aby přijímaly zápisy přesně v jedné nebo všech oblastech. Systém využívá dvouúrovňový, vnořený protokol konsensu – jedna úroveň pracuje v replikách repliky fyzického oddílu, který přijímá zápisy, a druhá pracuje na úrovni sady oddílů, aby poskytla úplné záruky řazení pro všechny potvrzené zápisy v rámci sady oddílů. Tento vícevrstvý, vnořený konsensus je rozhodující pro implementaci našich přísných sla pro vysokou dostupnost, stejně jako pro implementaci modelů konzistence, které Cosmos DB nabízí svým zákazníkům.  

## <a name="conflict-resolution"></a>Řešení konfliktů

Náš návrh šíření aktualizací, řešení konfliktů a sledování příčinných souvislostí je inspirován předchozí prací na [epidemických algoritmech](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) a systému [Bayou.](https://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf) Zatímco jádra myšlenek přežily a poskytují vhodný referenční rámec pro komunikaci návrhu systému Cosmos DB, prošly také významnou transformací, když jsme je aplikovali na systém Cosmos DB. To bylo zapotřebí, protože předchozí systémy nebyly navrženy ani se zásadou správného řízení zdrojů, ani s rozsahem, v jakém musí Cosmos DB fungovat, ani poskytovat schopnosti (například vázanou konzistenci neaktuálnosti) a přísné a komplexní smlouvy SLA, které Cosmos DB dodává svým zákazníkům.  

Připomeňme, že sada oddílů je distribuována mezi více oblastí a následuje protokol replikace Cosmos DBs (multi-master) k replikaci dat mezi fyzickými oddíly obsahujícími danou sadu oddílů. Každý fyzický oddíl (sady oddílů) přijímá zápisy a slouží čtení obvykle klientům, které jsou místní pro tuto oblast. Zápisy přijaté fyzický oddíl v rámci oblasti jsou trvale potvrzena a vysoce dostupné v rámci fyzického oddílu před jejich uznání klientovi. Jedná se o předběžné zápisy a jsou šířeny do jiných fyzických oddílů v rámci sady oddílů pomocí kanálu anti-entropie. Klienti mohou požadovat nezávazně nebo potvrzené zápisy předáním hlavičky požadavku. Šíření antientropie (včetně četnosti šíření) je dynamické, založené na topologii sady oddílů, regionální blízkosti fyzických oddílů a nakonfigurované úrovni konzistence. V rámci sady oddílů Cosmos DB následuje schéma primárního potvrzení s dynamicky vybraným oddílem arbiteru. Výběr arbitra je dynamický a je nedílnou součástí rekonfigurace sady oddílů na základě topologie překrytí. Potvrzené zápisy (včetně víceřádkových/dávkových aktualizací) mají být zaručeny objednány. 

Používáme kódované vektorové hodiny (obsahující ID oblasti a logické hodiny odpovídající každé úrovni konsensu na repliku-set a partition-set, respektive) pro sledování příčinné souvislosti a vektory verze pro detekci a řešení konfliktů aktualizací. Topologie a algoritmus vzájemného výběru jsou navrženy tak, aby zajistily pevné a minimální úložiště a minimální zatížení sítě vektorů verzí. Algoritmus zaručuje vlastnost striktní konvergence.  

Pro databáze Cosmos nakonfigurované s více oblastmi zápisu nabízí systém řadu flexibilních zásad automatického řešení konfliktů, ze kterých si mohou vývojáři vybrat, včetně: 

- **Last-Write-Wins (LWW)**, který ve výchozím nastavení používá systémem definovanou vlastnost časového razítka (která je založena na protokolu hodin časové synchronizace). Cosmos DB také umožňuje zadat všechny ostatní vlastní číselné vlastnosti, které mají být použity pro řešení konfliktů.  
- **Zásady řešení konfliktů definované aplikací (vlastní)** (vyjádřené pomocí slučovacích postupů), které jsou určeny pro sémantiku definovanou aplikací. Tyto postupy získat vyvolána při detekci konfliktů zápisu a zápisu pod záštitou databázové transakce na straně serveru. Systém poskytuje přesně jednou záruku pro provedení postupu sloučení jako součást protokolu závazku. K dispozici je [několik ukázek řešení konfliktů,](how-to-manage-conflicts.md) se kterými můžete hrát.  

## <a name="consistency-models"></a>Modely konzistence

Ať už nakonfigurujete databázi Cosmos s jednou nebo více oblastí zápisu, můžete si vybrat z pěti dobře definovaných modelů konzistence. S více oblastí zápisu jsou následující některé pozoruhodné aspekty úrovně konzistence:  

Ohraničená konzistence neaktuálnosti zaručuje, že všechna čtení budou v rámci předpony *K* nebo *T* sekund od nejnovějšího zápisu v některé z oblastí. Kromě toho čtení s ohraničenou konzistence neaktuálnost je zaručeno, že je monotónní a konzistentní záruky předpony. Protokol antientropie pracuje s omezenou rychlostí a zajišťuje, že se předpony nehromadí a není třeba použít protitlak na zápisy. Konzistence relace zaručuje monotónní čtení, monotónní zápis, čtení vlastních zápisů, zápis následuje čtení a konzistentní záruky předpony po celém světě. Pro databáze nakonfigurované se silnou konzistencí výhody (nízká latence zápisu, vysoká dostupnost zápisu) více oblastí zápisu neplatí z důvodu synchronní replikace napříč oblastmi.

Sémantiku pěti modelů konzistence v Cosmos DB jsou popsány [zde](consistency-levels.md)a matematicky popsány pomocí specifikace tla + vysoké úrovně [zde](https://github.com/Azure/azure-cosmos-tla).

## <a name="next-steps"></a>Další kroky

Další informace o konfiguraci globální distribuce pomocí následujících článků:

* [Přidání oblastí do účtu databáze nebo jejich odebrání](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Jak nakonfigurovat klienty pro multi-naváděcí](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Jak vytvořit vlastní zásady řešení konfliktů](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
