---
title: Pochopení rozdílů mezi Azure Cosmos DB NoSQL a relačními databázemi
description: Tento článek vyjmenovává rozdíly mezi NoSQL a relačnídatabáze
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/16/2019
ms.reviewer: sngun
ms.openlocfilehash: 1cd80fee51565f2a2c1afa38ed883c10f51a5ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896620"
---
# <a name="understanding-the-differences-between-nosql-and-relational-databases"></a>Pochopení rozdílů mezi NoSQL a relačními databázemi

Tento článek bude výčet některé klíčové výhody nosql databází přes relační databáze. Budeme také diskutovat o některých výzvách při práci s NoSQL. Pro podrobný pohled na různá úložiště dat, které existují, podívejte se na náš článek o [výběru správného úložiště dat](https://docs.microsoft.com/azure/architecture/guide/technology-choices/data-store-overview).

## <a name="high-throughput"></a>Vysoká propustnost

Jedním z nejzřetelnějších problémů při udržování relační databázový systém je, že většina relační motory použít zámky a zámky k vynucení přísné [acid sémantiku](https://en.wikipedia.org/wiki/ACID). Tento přístup má výhody z hlediska zajištění konzistentní stav dat v rámci databáze. Existují však těžké kompromisy s ohledem na souběžnost, latence a dostupnost. Vzhledem k těmto základním architektonickým omezením mohou vysoké transakční objemy vést k nutnosti ručně střepovat data. Implementace ručního shardingu může být časově náročné a bolestivé cvičení.

V těchto scénářích [distribuované databáze](https://en.wikipedia.org/wiki/Distributed_database) může nabídnout škálovatelnější řešení. Údržba však může být stále nákladným a časově náročným cvičením. Správci mohou muset udělat další práci, aby zajistili, že distribuovaná povaha systému je transparentní. Mohou také muset zohlednit "odpojenou" povahu databáze.

[Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction) tyto výzvy zjednodušuje tím, že se nasazuje po celém světě ve všech oblastech Azure. Rozsahy oddílů lze dynamicky rozdělit, aby se databáze bezproblémově rozšiřovala v souladu s aplikací a současně udržovala vysokou dostupnost. Jemně odstupňovaná multinájemní nancy a přísně řízené zásady správného řízení prostředků nativní pro cloud usnadňuje [úžasné záruky latence](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs#consistency-levels-and-latency) a předvídatelný výkon. Dělení je plně spravováno, takže správci nemusí psát kód nebo spravovat oddíly.

Pokud vaše transakční objemy dosahují extrémníúrovně, například mnoho tisíc transakcí za sekundu, měli byste zvážit distribuovanou databázi NoSQL. Zvažte Azure Cosmos DB pro maximální efektivitu, snadnou údržbu a snížení celkových nákladů na vlastnictví.

![Back-end](./media/relational-or-nosql/backend-scaled.png)

## <a name="hierarchical-data"></a>Hierarchická data

Existuje významný počet případů použití, kde transakce v databázi může obsahovat mnoho vztahů nadřazený podřízený. Tyto vztahy mohou v průběhu času výrazně růst a obtížně se zvládat. Formy [hierarchických databází](https://en.wikipedia.org/wiki/Hierarchical_database_model) se objevily v průběhu osmdesátých let, ale nebyly populární kvůli neefektivnosti skladování. Oni také ztratili trakci jako [Ted Codd relační model](https://en.wikipedia.org/wiki/Relational_model) se stal de facto standard používaný prakticky všechny běžné systémy pro správu databází.

Dnes však popularita databází ve stylu dokumentů výrazně vzrostla. Tyto databáze mohou být považovány za znovuobjevení paradigmatu hierarchické databáze, nyní bez zábran obavy týkající se nákladů na ukládání dat na disk. V důsledku toho může být zachování mnoha složitých vztahů mezi entitami nadřazený podřízený chod v relační databázi nyní považováno za anti-pattern ve srovnání s moderními přístupy orientovanými na dokumenty.

Vznik [objektově orientovaného designu](https://en.wikipedia.org/wiki/Object-oriented_design)a [nesoulad impedance,](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch) který vzniká při jeho kombinaci s relačními modely, také zvýrazní anti-vzor v relačních databázích pro určité případy použití. V důsledku toho mohou vzniknout skryté, ale často významné náklady na údržbu. Přestože [se přístupy ORM vyvinuly,](https://en.wikipedia.org/wiki/Object-relational_mapping) aby to částečně zmírnily, databáze orientované na dokumenty se nicméně mnohem lépe sjednocují s objektově orientovanými přístupy. S tímto přístupem vývojáři nejsou nuceni být potvrzena orm ovladače nebo na míru jazykově specifické [OO databázové stroje](https://en.wikipedia.org/wiki/Object_database). Pokud vaše data obsahují mnoho nadřazených a podřízených relací a hluboké úrovně hierarchie, můžete zvážit použití databáze dokumentů NoSQL, jako je například [rozhraní SQL API Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction).

![Podrobnosti objednávky](./media/relational-or-nosql/order-orderdetails.jpg)

## <a name="complex-networks-and-relationships"></a>Komplexní sítě a vztahy

Je ironií, že vzhledem k jejich názvu představují relační databáze méně než optimální řešení pro modelování hlubokých a složitých vztahů. Důvodem je, že vztahy mezi entitami ve skutečnosti neexistují v relační databázi. Je třeba je vypočítat za běhu, se složitými vztahy, které vyžadují kartézská spojení, aby bylo možné mapování pomocí dotazů. V důsledku toho operace exponenciálně dražší z hlediska výpočtu jako zvýšení vztahů. V některých případech relační databáze, která se pokouší spravovat tyto entity, se stane nepoužitelnou.

Různé formy "sítě" databází se objevily v době, kdy se objevily relační databáze, ale stejně jako u hierarchických databází se tyto systémy snažily získat popularitu. Pomalé přijetí bylo způsobeno nedostatkem případů použití v té době, a skladování neefektivnosti. Dnes by bylo možné považovat databázové moduly grafů za opětovné objevení paradigmatu síťové databáze. Hlavní výhodou těchto systémů je, že vztahy jsou uloženy jako "prvotřídní občané" v databázi. Proto lze procházet vztahy v konstantním čase, spíše než zvýšení časové složitosti s každým novým spojením nebo křížovým produktem.

Pokud udržujete komplexní síť vztahů v databázi, můžete zvážit databázi grafů, jako je například [rozhraní API Azure Cosmos DB Gremlin](https://docs.microsoft.com/azure/cosmos-db/graph-introduction) pro správu těchto dat.

![Graph](./media/relational-or-nosql/graph.png)

Azure Cosmos DB je vícemodelová databázová služba, která nabízí projekci rozhraní API pro všechny hlavní typy modelů NoSQL; Rodina sloupců, Dokument, Graf a Hodnota klíče. [Gremlin (graf)](https://docs.microsoft.com/azure/cosmos-db/gremlin-support) a SQL (Core) Document API vrstvy jsou plně interoperabilní. To má výhody pro přepínání mezi různými modely na úrovni programovatelnosti. Úložiště grafů lze dotazovat z hlediska složitých průchodů sítě i transakcí modelovaných jako záznamy dokumentů ve stejném úložišti.

## <a name="fluid-schema"></a>Schéma tekutin

Další konkrétní charakteristikou relačních databází je, že schémata musí být definována v době návrhu. To má přínos z hlediska referenční integrity a shody údajů. Však může být také omezující jako aplikace roste. Reakce na změny ve schématu napříč logicky samostatnými modely, které sdílejí stejnou tabulku nebo definici databáze, se může časem zkomplikovat. Tyto případy použití často těžit ze schématu převedena na aplikaci spravovat na základě záznamu. To vyžaduje, aby databáze byla "agnostik schématu" a umožnila, aby záznamy byly "samopopisovací" z hlediska dat v nich obsažených.

Pokud spravujete data, jejichž struktury se neustále mění vysokou rychlostí, zejména pokud transakce mohou pocházet z externích zdrojů, kde je obtížné vynutit shodu v databázi, můžete zvážit přístup více šepomu a gnostik pomocí spravované databázové služby NoSQL, jako je Azure Cosmos DB.

## <a name="microservices"></a>Mikroslužby

Struktura [mikroslužeb](https://en.wikipedia.org/wiki/Microservices) v posledních letech výrazně vzrostla. Tento vzor má své kořeny v [architektuře orientované na služby](https://en.wikipedia.org/wiki/Service-oriented_architecture). De facto standardem pro přenos dat v těchto moderních architekturách mikroslužeb je [JSON](https://en.wikipedia.org/wiki/JSON), což je také paměťové médium pro drtivou většinu dokumentově orientovaných NoSQL databází. Díky tomu je NoSQL dokument ukládá mnohem bezproblémovější vhodné pro perzistenci a synchronizaci (pomocí [vzorců získávání událostí)](https://en.wikipedia.org/wiki/Event-driven_architecture)napříč složitými implementacemi mikroslužeb. Tradiční relační databáze může být mnohem složitější udržovat v těchto architekturách. To je způsobeno větší množství transformace potřebné pro stav a synchronizace mezi api. Azure Cosmos DB zejména má řadu funkcí, které z něj činí ještě bezproblémovější pro architektury Mikroslužeb založené na JSON než mnoho databází NoSQL:

* výběr čistých datových typů JSON
* JavaScriptový engine a [api dotazu](https://docs.microsoft.com/azure/cosmos-db/javascript-query-api) integrované do databáze.
* nejmodernější [kanál o změně,](https://docs.microsoft.com/azure/cosmos-db/change-feed) ke kterému se klienti mohou přihlásit, aby získali upozornění na změny kontejneru.

## <a name="some-challenges-with-nosql-databases"></a>Některé výzvy s nosql databázemi

Ačkoli existují některé jasné výhody při implementaci nosql databází, existují také některé problémy, které budete chtít vzít v úvahu. Ty nemusí být přítomny ve stejné míře při práci s relačním modelem:

* transakce s mnoha vztahy směřujícími ke stejné entitě.
* transakce, které vyžadují silnou konzistentnost v celé datové sadě.

Při pohledu na první výzvu, pravidlo-of-palec v databázích NoSQL je obecně denormalizace, která, jak kloubové dříve, produkuje efektivnější čtení v distribuovaném systému. Nicméně, tam jsou některé konstrukční výzvy, které přicházejí do hry s tímto přístupem. Vezměme si příklad produktu, který souvisí s jednou kategorií a více značkami:

![Spojení](./media/relational-or-nosql/many-joins.png)

Osvědčeným postupem v databázi dokumentů NoSQL by bylo denormalizovat název kategorie a názvy značek přímo v "dokumentu produktu". Aby však byly kategorie, značky a produkty synchronizovány, možnosti návrhu, které to usnadňují, přidaly složitost údržby, protože data jsou duplikována ve více záznamech v produktu, spíše než jednoduchá aktualizace v "one-to-many" a spojení pro načtení dat. 

Kompromis je, že čtení jsou efektivnější v nenormalizované záznamu a stále efektivnější, jak se zvyšuje počet koncepčně spojených entit. Však stejně jako efektivita čtení zvyšuje s rostoucím počtem spojených entit v denormalize záznamu, tak také složitost údržby udržování entit v synchronizaci. Jedním ze způsobů zmírnění tohoto kompromisu je vytvoření [hybridního datového modelu](https://docs.microsoft.com/azure/cosmos-db/modeling-data#hybrid-data-models).

Zatímco v databázích NoSQL je k dispozici větší flexibilita pro řešení těchto kompromisů, zvýšená flexibilita může také přinést více rozhodnutí o návrhu. Podívejte se do našeho [článku, jak modelovat a rozdělovat data v Azure Cosmos DB pomocí příkladu reálného světa](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example), který zahrnuje přístup k [synchronizaci nenormalizovaných uživatelských dat,](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example#denormalizing-usernames) kde uživatelé nejen sedí v různých oddílech, ale v různých kontejnerech.

S ohledem na silnou konzistenci je vzácné, že to bude vyžadováno v celé sadě dat. V případech, kdy je to však nezbytné, může to být problém v distribuovaných databázích. Aby byla zajištěna silná konzistence, je třeba synchronizovat data ve všech replikách a oblastech, než klienti mohou číst. To může zvýšit latenci čtení.

Azure Cosmos DB opět nabízí větší flexibilitu než relační databáze pro různé kompromisy, které jsou relevantní zde, ale pro malé škálování implementace, tento přístup může přidat další aspekty návrhu. Další podrobnosti k tomuto tématu narvou na náš článek o [konzistenci, dostupnosti a kompromisech](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs) výkonu.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak spravovat svůj účet Azure Cosmos a další koncepty:

* [Jak spravovat svůj účet Azure Cosmos](how-to-manage-database-account.md)
* [Globální distribuce](distribute-data-globally.md)
* [Úrovně konzistence](consistency-levels.md)
* [Práce s kontejnery a položkami Azure Cosmos](databases-containers-items.md)
* [Koncový bod služby Virtuální sítě pro váš účet Azure Cosmos](vnet-service-endpoint.md)
* [IP brána firewall pro váš účet Azure Cosmos](firewall-support.md)
* [Jak přidat a odebrat oblasti Azure do svého účtu Azure Cosmos](how-to-manage-database-account.md)
* [SLA DB Služby Azure Cosmos](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
