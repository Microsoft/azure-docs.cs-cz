---
title: Globální distribuce s Azure Cosmos DB – pod kapotou
description: Tento článek poskytuje technické informace týkající se globální distribuce Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 1b47ad27abbe59eceabd15d091f88f4659d8dad6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "102486382"
---
# <a name="global-data-distribution-with-azure-cosmos-db---under-the-hood"></a>Globální distribuce dat pomocí Azure Cosmos DB – pod kapotou
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB je základní služba v Azure, která se nasazuje napříč všemi oblastmi Azure po celém světě, včetně veřejných, svrchovaného, ministerstva obrany a státních cloudů. V rámci datového centra nasadíme a spravujeme Azure Cosmos DB na obrovských razítek počítačů, z nichž každá má vyhrazené místní úložiště. V rámci datového centra je Azure Cosmos DB nasazený v mnoha clusterech, z nichž každá potenciálně spouští více generací hardwaru. Počítače v clusteru jsou obvykle rozloženy mezi 10-20 domén selhání pro zajištění vysoké dostupnosti v rámci oblasti. Následující obrázek ukazuje topologii globálního distribučního systému Cosmos DB:

:::image type="content" source="./media/global-dist-under-the-hood/distributed-system-topology.png" alt-text="Systémová topologie" border="false":::

**Globální distribuce v Azure Cosmos DB je klíč:** Po několika kliknutích nebo programově s jedním voláním rozhraní API můžete přidat nebo odebrat geografické oblasti přidružené k databázi Cosmos. Databáze Cosmos se zase skládá ze sady Cosmos kontejnerů. V Cosmos DB kontejnery slouží jako logické jednotky distribuce a škálovatelnosti. Kolekce, tabulky a grafy, které vytvoříte, jsou (interně) pouze Cosmos kontejnery. Kontejnery jsou zcela nezávislá schématu a poskytují obor dotazu. Data v kontejneru Cosmos jsou automaticky indexována při příjmu. Automatické indexování umožňuje uživatelům dotazování na data bez starostí se správou schématu nebo indexu, zejména při globálně distribuované instalaci.  

- V dané oblasti jsou data v kontejneru distribuována pomocí klíče oddílu, který zadáte a je transparentně spravován základními fyzickými oddíly (*místní distribuce*).  

- Každý fyzický oddíl je také replikován v rámci geografických oblastí (*globální distribuce*). 

Když aplikace používající Cosmos DB elasticky škáluje propustnost Cosmos kontejneru nebo spotřebovává více úložiště, Cosmos DB transparentně zpracovává operace správy oddílů (rozdělené, klonování, odstraňování) napříč všemi oblastmi. Nezávisle na škálování, distribuci nebo selháních aplikace Cosmos DB nadále poskytovat jedinou systémovou bitovou kopii dat v kontejnerech, které jsou globálně distribuované napříč libovolným počtem oblastí.  

Jak je znázorněno na následujícím obrázku, data v kontejneru jsou distribuována do dvou dimenzí – v oblasti a oblastech, po celém světě:  

:::image type="content" source="./media/global-dist-under-the-hood/distribution-of-resource-partitions.png" alt-text="fyzické oddíly" border="false":::

Fyzický oddíl je implementován skupinou replik, která se nazývá *sada replik*. Každý počítač hostuje stovky replik, které odpovídají různým fyzickým oddílům v rámci pevně stanovené sady procesů, jak je znázorněno na obrázku výše. Repliky odpovídající fyzickým oddílům se dynamicky umísťují a vyrovnávají zatížení napříč počítači v rámci clusteru a datových center v rámci jedné oblasti.  

Replika jedinečně patří do tenanta Azure Cosmos DB. Každá replika je hostitelem instance [databázového stroje](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)Cosmos DB, který spravuje prostředky i přidružené indexy. Databázový stroj Cosmos funguje na systém typů založených na Atom-Record-Sequence (ARS). Modul je nezávislá k konceptu schématu a rozostří hranice mezi strukturou a hodnotami instancí záznamů. Cosmos DB dosahuje úplného agnosticism schématu automatickým indexováním všeho po ingestování, což umožňuje uživatelům dotazování na globálně distribuovaná data, aniž by museli zabývat se správou schématu nebo indexu.

Databázový stroj Cosmos se skládá z komponent, včetně implementace několika koordinačních primitivních prvků, jazykových modulů runtime, procesoru dotazů a subsystémů úložiště a indexování zodpovědných za transakční úložiště a indexování dat (v uvedeném pořadí). Aby zajistila odolnost a vysokou dostupnost, databázový stroj uchovává data a index v SSD a replikuje je mezi instancemi databázového stroje v uvedeném pořadí replik. Větší klienti odpovídají vyšší škále propustnosti a úložiště a mají buď větší nebo více replik, nebo obojí. Každá součást systému je plně asynchronní – žádné vlákno nikdy neblokuje a každé vlákno provádí krátkodobou práci bez nutnosti přepínačů zbytečných vláken. Omezení rychlosti a zatížení se nachází v celém zásobníku z řízení přístupu ke všem cestám vstupu a výstupu. Databázový stroj Cosmos je navržený tak, aby využil jemně odstupňované souběžnosti a poskytoval vysokou propustnost při provozu v Frugal množství systémových prostředků.

Globální distribuce Cosmos DB spoléhá na dvě abstrakce klíčů – *sady replik* a *sady oddílů*. Sada replik je modulární Galerie příček pro koordinaci a sada oddílů je dynamická překryva jednoho nebo více geograficky distribuovaných fyzických oddílů. Abychom porozuměli tomu, jak globální distribuce funguje, musíme pochopit tyto dvě klíčové abstrakce. 

## <a name="replica-sets"></a>Sady replik

Fyzický oddíl je vyhodnocen jako samoobslužná skupina replik s vyrovnáváním zatížení v různých doménách selhání, která se nazývá sada replik. Tato sada souhrnně implementuje protokol replikovaného stavového počítače, aby byla data v rámci fyzického oddílu vysoce dostupná, trvalá a konzistentní. Členství v sadě replik *N* je dynamické – udržuje výkyv mezi *nminimum* a *nmaximumy* na základě selhání, operací správy a času, kdy se neúspěšné repliky mají znovu vygenerovat/obnovit. Na základě změn členství překonfiguruje protokol replikace také velikost kvora pro čtení a zápis. Pro jednotnou distribuci propustnosti, která je přiřazená danému fyzickému oddílu, používáme dvě nápady: 

- Za prvé, náklady na zpracování požadavků na zápis na vedoucím jsou vyšší než náklady na použití aktualizací v následném programu. V souladu s tím je vedoucí z rozpočtu mnohem více systémových prostředků než sledující. 

- V druhé míře je kvorum čtení pro danou úroveň konzistence tvořeno výhradně v replikách subsystému. Nemusíme kontaktovat vedoucího, aby zavedla čtení, pokud to není nutné. Pro [pět modelů konzistence](consistency-levels.md) , které Cosmos DB podporuje, využíváme řadu nápadů od výzkumu na základě vztahu [zatížení a kapacity](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) v systémech založených na kvoru.  

## <a name="partition-sets"></a>Sady oddílů

Skupina fyzických oddílů, jedna z každé konfigurace s oblastmi databáze Cosmos, se skládá pro správu stejné sady klíčů replikovaných ve všech nakonfigurovaných oblastech. Tato vyšší koordinační primitivum se nazývá *oddíl-set* – geograficky distribuované dynamické překrytí fyzických oddílů, které spravují danou sadu klíčů. I když je daný fyzický oddíl (sada replik) vymezen v rámci clusteru, sada oddílů může zahrnovat clustery, datová centra a geografické oblasti, jak je znázorněno na následujícím obrázku:  

:::image type="content" source="./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png" alt-text="Sady oddílů" border="false":::

Můžete si představit sadu oddílů jako geograficky rozptýlenou "Super sadu replik", která se skládá z několika sad replik, které mají stejnou sadu klíčů. Podobně jako u sady replik je členství v sadě oddílů také dynamické – mění se na základě implicitních operací správy fyzického oddílu, které přidávají nebo odebírají nové oddíly do nebo z dané sady oddílů (například při horizontálním navýšení kapacity propustnosti v kontejneru, přidání nebo odebrání oblasti do databáze Cosmos nebo při selhání). Vzhledem k tomu, že každý z oddílů (sada oddílů) spravuje členství oddílu v rámci vlastní sady replik, je členství plně decentralizované a vysoce dostupné. Během opětovné konfigurace sady oddílů je také navázána topologie překrytí mezi fyzickými oddíly. Topologie se dynamicky vybere na základě úrovně konzistence, zeměpisné vzdálenosti a dostupné šířky pásma sítě mezi zdrojovým a cílovým fyzickým oddílem.  

Služba umožňuje konfigurovat databáze Cosmos buď s jednou oblastí pro zápis, nebo s několika oblastmi zápisu, a v závislosti na možnosti volby, sady oddílů jsou nakonfigurovány tak, aby přijímaly zápisy pouze v jedné nebo všech oblastech. Systém využívá protokol s vnořenou dohodou na dvou úrovních – jedna úroveň funguje v rámci replik sady repliky fyzického oddílu, který přijímá zápisy, a druhá pracuje na úrovni oddílu, aby poskytovala úplné řazení pro všechny potvrzené zápisy v rámci sady oddílů. Tato víceúrovňová a vnořená shoda je zásadní pro implementaci našeho přísného SLA pro vysokou dostupnost a také pro implementaci modelů konzistence, které Cosmos DB nabídky svým zákazníkům.  

## <a name="conflict-resolution"></a>Řešení konfliktů

Náš návrh pro šíření aktualizací, řešení konfliktů a sledování příčin se nechte inspirovat z předchozích prací na [algoritmech epidemie](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) a [Bayou](https://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf) systému. I když se zadržely jádra nápadů a poskytovaly pohodlný rámec referenčních informací pro komunikaci s návrhem systému Cosmos DB, mají také významnou transformaci, jak jsme je použili pro Cosmos DB systém. To bylo potřeba, protože předchozí systémy byly navržené bez zásad správného řízení prostředků ani s škálováním, na kterém Cosmos DB potřebuje pracovat, ani poskytovat funkce (například konzistence s ohraničenou neaktuálností) a přísné a komplexní SLA, které Cosmos DB doručí svým zákazníkům.  

Odvolá, že sada oddílů je distribuovaná napříč několika oblastmi a následuje Cosmos databáze (zápisy ve více oblastech) pro replikaci dat mezi fyzickými oddíly, které tvoří danou sadu oddílů. Každý fyzický oddíl (sada oddílů) přijímá zápisy a obsluhuje čtení obvykle pro klienty, kteří jsou místní k této oblasti. Zápisy přijaté fyzickým oddílem v rámci oblasti jsou trvale potvrzené a připravené v rámci fyzického oddílu před potvrzením klientovi. Jedná se o nezávazně-zápisy a šíří se do jiných fyzických oddílů v rámci sady oddílů pomocí kanálu anti-entropie. Klienti si můžou vyžádat buď nezávazně nebo potvrzené zápisy, předáním hlavičky žádosti. Šíření proti entropii (včetně četnosti šíření) je dynamické na základě topologie oddílu, oblasti a oblasti fyzických oddílů a úrovně konzistence nakonfigurované. V rámci sady oddílů Cosmos DB postupovat podle primárního schématu potvrzení s dynamicky vybraným oddílem arbiter. Výběr arbiter je dynamický a je nedílnou součástí opětovné konfigurace sady oddílů založené na topologii překrytí. Potvrzené zápisy (včetně víceřádkových nebo dávkových aktualizací) jsou zaručené pro objednání. 

Používáme kódované vektorové hodiny (s ID oblasti a logickými hodinami odpovídajícími jednotlivým úrovním shody v sadě replik a sadě oddílů) pro účely sledování příčin a jejich řešení ke zjištění a vyřešení konfliktů aktualizací. Topologie a algoritmus výběru druhé strany jsou navržené tak, aby se zajistila pevná a minimální velikost úložiště a minimální nároky na síť pro vektory verzí. Algoritmus garantuje striktní vlastnost konvergence.  

Pro databáze Cosmos nakonfigurované s více oblastmi zápisu nabízí systém řadu flexibilních zásad řešení konfliktů, které vývojáři můžou vybírat, včetně těchto: 

- **Last-Write-WINS (LWW)**, který ve výchozím nastavení používá systémově definovanou vlastnost časového razítka (která je založená na protokolu hodinové synchronizace času). Cosmos DB také umožňuje zadat jakoukoli jinou vlastní číselnou vlastnost, která se má použít pro řešení konfliktů.  
- **Zásada pro řešení konfliktů definovaná aplikací (vlastní)** (vyjádřená prostřednictvím procedur sloučení), která je určená pro sémantiku pro sémantiku definovaná aplikacemi pro účely sladění konfliktů. Tyto postupy se vyvolají při detekci konfliktů zápisu a zápisu pod záštitou databázové transakce na straně serveru. Systém poskytuje přesně jednu jistotu pro provedení slučovací procedury jako součást protokolu závazku. K dispozici je [několik ukázek řešení konfliktů](how-to-manage-conflicts.md) , se kterými můžete hrát.  

## <a name="consistency-models"></a>Modely konzistence

Bez ohledu na to, jestli nakonfigurujete databázi Cosmos s jednou nebo více oblastmi zápisu, můžete si vybrat z pěti jasně definovaných modelů konzistence. V případě více oblastí pro zápis jsou zde tyto významné aspekty úrovní konzistence:  

Hranice konzistence s ohraničenou kostarou se zaručuje, že všechny čtení budou v rámci předpony *KB* nebo *T* sekund z posledního zápisu v jakékoli oblasti. Kromě toho je zaručeno, že čtení s omezenou konzistencí neaktuálnosti budou monotónní a s konzistentními zárukami předpony. Protokol anti-entropie pracuje v omezeném poměru a zajišťuje, aby se předpony neshromáždily a aby se nemusely použít zatížení pro zápis. Konzistence relace zaručuje monotónní čtení, monotónní zápisu, čtení vlastních zápisů, zápis následuje po čtení a konzistentní záruky předpony, po celém světě. Pro databáze nakonfigurované se silnou konzistencí se nevztahují výhody (nízká latence zápisu, dostupnost vysokého zápisu) více oblastí pro zápis, protože se jedná o synchronní replikaci napříč oblastmi.

Sémantika pěti modelů konzistence v Cosmos DB je popsána [zde](consistency-levels.md)a matematicky popsány pomocí víceúrovňového modelu tla + Specification [.](https://github.com/Azure/azure-cosmos-tla)

## <a name="next-steps"></a>Další kroky

Další informace o konfiguraci globální distribuce pomocí následujících článků:

* [Přidání oblastí do účtu databáze nebo jejich odebrání](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Jak vytvořit vlastní zásady řešení konfliktů](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
