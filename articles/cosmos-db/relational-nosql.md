---
title: Princip rozdílů mezi Azure Cosmos DB NoSQL a relačními databázemi
description: Tento článek popisuje rozdíly mezi NoSQL a relačními databázemi.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/16/2019
ms.reviewer: sngun
ms.openlocfilehash: 63663d228052934616a59f5d84ff16a6510e46c9
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92282076"
---
# <a name="understanding-the-differences-between-nosql-and-relational-databases"></a>Princip rozdílů mezi NoSQL a relačními databázemi

V tomto článku se dozvíte některé z klíčových výhod databází NoSQL prostřednictvím relačních databází. Probereme také některé problémy při práci s NoSQL. Podrobný pohled na různá úložiště dat, která existují, najdete v našem článku o [výběru správného úložiště dat](https://docs.microsoft.com/azure/architecture/guide/technology-choices/data-store-overview).

## <a name="high-throughput"></a>Vysoká propustnost

Jedním z nejoblíbenějších výzev při údržbě relačního databázového systému je to, že většina relačních modulů používá zámky a zámky k prosazování striktní [sémantiky kyselosti](https://en.wikipedia.org/wiki/ACID). Tento přístup má výhody v souvislosti s zajištěním konzistentního stavu dat v rámci databáze. Existují však těžké kompromisy s ohledem na souběžnost, latenci a dostupnost. Z důvodu těchto základních omezení architektury můžou vysoké transakční svazky způsobit ruční horizontálních oddílůí dat. Implementace ručních horizontálního dělení může být časově náročné a bolestivý cvičení.

V těchto scénářích můžou [distribuované databáze](https://en.wikipedia.org/wiki/Distributed_database) nabízet lépe škálovatelné řešení. Údržba však může být stále náročná a časově náročná cvičení. Správci mohou muset udělat dodatečnou práci, aby bylo zajištěno, že distribuovaná povaha systému bude transparentní. Mohou být také nutné přihlédnout k "odpojenému" povaze databáze.

[Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction) tyto výzvy zjednodušuje tím, že se nasadí po celém světě napříč všemi oblastmi Azure. Rozsahy oddílu je možné dynamicky rozdělit, aby bylo možné bezproblémově rozšiřovat databázi v rámci aplikace a současně zachovat vysokou dostupnost. Jemně odstupňované a vysoce kontrolované – nativní řízení prostředků v cloudu usnadňuje [záruky ohromující latence](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs#consistency-levels-and-latency) a předvídatelný výkon. Vytváření oddílů je plně spravované, takže správci nemusí psát kód ani spravovat oddíly.

Pokud vaše transakční svazky dosáhnou extrémních úrovní, například mnoho tisíc transakcí za sekundu, měli byste zvážit distribuovanou databázi NoSQL. Zvažte Azure Cosmos DB maximální efektivity, snadné údržby a snížení celkových nákladů na vlastnictví.

:::image type="content" source="./media/relational-or-nosql/backend-scaled.png" alt-text="Back-end" border="false":::

## <a name="hierarchical-data"></a>Hierarchická data

Existuje značný počet případů použití, kdy transakce v databázi mohou obsahovat mnoho vztahů mezi nadřazenými a podřízenými objekty. Tyto vztahy se můžou v průběhu času výrazně zvýšit a jejich správa se obtížně prokáže. Formuláře [hierarchických databází](https://en.wikipedia.org/wiki/Hierarchical_database_model) se objevily během 1980s, ale nenašly se z důvodu neefektivity úložiště. Také ztratily vzdálení, protože [relační model vybrané Codd](https://en.wikipedia.org/wiki/Relational_model) se stal de facto standard používaný prakticky všemi běžnými systémy správy databází.

V současné době ale ještě výrazně vzrostla oblíbenosti databází ve stylu dokumentu. Tyto databáze mohou být považovány za rezásoby hierarchického paradigmata databáze a jsou nyní nepotlačeny v důsledku nákladů na ukládání dat na disk. V důsledku toho by bylo udržování velkého počtu složitých vztahů mezi nadřazenými a podřízenými objekty v relační databázi považováno za antipattern v porovnání s moderními přístupy orientovanými na dokument.

V případě potřeby vývoje [objektově orientovaného návrhu](https://en.wikipedia.org/wiki/Object-oriented_design)a [neshody dopadů](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch) , které vznikají při kombinaci s relačními modely, se také zvýrazní antipattern relačních databází pro určité případy použití. V důsledku toho mohou nastat skryté, ale často významné náklady na údržbu. I když se přístupy ke službě [ORM](https://en.wikipedia.org/wiki/Object-relational_mapping) vyvinuly tak, aby částečně zmírnily tyto databáze zaměřené na dokumenty, ale s využitím objektově orientovaných přístupů mnohem lépe. S tímto přístupem nebudou vývojáři nucení docházet k ovladačům ORM ani [databázovým strojům](https://en.wikipedia.org/wiki/Object_database)Bespoke specifickým pro jazyk. Pokud vaše data obsahují mnoho vztahů mezi nadřazenými a podřízenými tabulkami a hlubokou úrovní hierarchie, můžete zvážit použití databáze dokumentů NoSQL, jako je [Azure Cosmos DB rozhraní SQL API](https://docs.microsoft.com/azure/cosmos-db/introduction).

:::image type="content" source="./media/relational-or-nosql/order-orderdetails.jpg" alt-text="Back-end":::

## <a name="complex-networks-and-relationships"></a>Komplexní sítě a vztahy

Ironically, na základě jejich názvu, obsahují relační databáze méně než optimální řešení pro modelování hloubkových a složitých vztahů. Důvodem je, že relace mezi entitami ve skutečnosti v relační databázi neexistují. Je nutné je vypočítat za běhu, přičemž komplexní vztahy vyžadují spojení kartézském, aby bylo možné mapování pomocí dotazů. V důsledku toho se operace budou exponenciálně dražší v souvislosti s výpočtem, když se relace zvyšují. V některých případech se relační databáze, která se pokouší o správu takových entit, stane nepoužitelnou.

V době, kdy se v relačních databázích objevily různé formy "síťových" databází, ale stejně jako u hierarchických databází se tyto systémy struggled k získání oblíbenosti. Pomalé přijetí bylo způsobeno nedostatečnými případy použití v čase a neefektivitou úložiště. V dnešní době se databázové stroje grafů mohly považovat za opakovaný výskyt paradigma síťové databáze. Klíčovou výhodou těchto systémů je, že relace jsou v rámci databáze uložené jako občané "první třídy". Proto je možné vztahy procházení provádět v konstantním čase, ale nemusíte zvyšovat složitost času u každého nového spojení nebo vzájemného produktu.

Pokud udržujete složitou síť relací ve vaší databázi, možná budete chtít zvážit databázi grafu, jako je [Azure Cosmos DB rozhraní API Gremlin](https://docs.microsoft.com/azure/cosmos-db/graph-introduction) pro správu těchto dat.

:::image type="content" source="./media/relational-or-nosql/graph.png" alt-text="Back-end":::

Azure Cosmos DB je databázová služba pro více modelů, která nabízí projekci rozhraní API pro všechny hlavní typy modelů NoSQL; Sloupce – rodina, dokument, graf a klíč-hodnota. Vrstvy rozhraní API pro [Gremlin (Graph)](https://docs.microsoft.com/azure/cosmos-db/gremlin-support) a SQL (Core) jsou plně interoperabilní. Přináší to výhody pro přepínání mezi různými modely na úrovni programovatelnosti. Obchody s grafy je možné dotazovat v podobě složitých síťových procházení a transakcí modelování jako záznamů dokumentů ve stejném úložišti.

## <a name="fluid-schema"></a>Schéma kapalin

Další konkrétní charakteristikou relačních databází je, že schémata je nutné definovat v době návrhu. To přináší výhody z pohledu referenční integrity a shody dat. Může ale také být omezující, když aplikace roste. Reakce na změny ve schématu napříč logicky oddělenými modely, které sdílejí stejnou definici tabulky nebo databáze, se můžou v průběhu času časem složitě. Takové případy použití často využívají ze schématu, které se přenese do aplikace pro správu na základě jednotlivých záznamů. To vyžaduje, aby byla databáze "Schema nezávislá" a aby záznamy v nich byly "samy popisující", a to s ohledem na data obsažená v nich.

Pokud spravujete data, jejichž struktury se neustále mění, zejména pokud transakce pocházejí z externích zdrojů, kde je obtížné vyhodnotit shodu v rámci databáze, možná budete chtít zvážit další přístup k nezávislá schématu pomocí spravované databázové služby NoSQL, jako je Azure Cosmos DB.

## <a name="microservices"></a>Mikroslužby

Model [mikroslužeb](https://en.wikipedia.org/wiki/Microservices) se v posledních letech významně zvětšil. Tento model má své kořeny v [architektuře orientované na služby](https://en.wikipedia.org/wiki/Service-oriented_architecture). Standardní pro přenos dat v těchto moderních architekturách mikroslužeb je [JSON](https://en.wikipedia.org/wiki/JSON), což znamená, že se jedná o paměťové médium pro převážnou většinu NoSQL databází orientovaných na dokumenty. Díky tomu je NoSQL dokumentů mnohem pohodlnější, a to jak pro trvalost, tak pro synchronizaci (pomocí [vzorů zdrojů událostí](https://en.wikipedia.org/wiki/Event-driven_architecture)) napříč komplexními implementacemi mikroslužeb. Další tradiční relační databáze mohou být mnohem složitější, aby byly v těchto architekturách uchovávány. Důvodem je větší množství transformace vyžadované pro stav i synchronizaci napříč rozhraními API. Azure Cosmos DB zejména nabízí celou řadu funkcí, díky kterým je ještě více bezproblémové pro architektury mikroslužeb založené na formátu JSON, než kolik databází NoSQL:

* Volba čistě datových typů JSON
* modul JavaScript a [rozhraní API dotazů](https://docs.microsoft.com/azure/cosmos-db/javascript-query-api) integrované do databáze.
* Stavový [kanál změny](https://docs.microsoft.com/azure/cosmos-db/change-feed) , ke kterému se klienti můžou přihlásit, aby mohli dostávat oznámení o změnách kontejneru.

## <a name="some-challenges-with-nosql-databases"></a>Některé problémy s databázemi NoSQL

I když při implementaci databází NoSQL existují nějaké jasné výhody, existují i některé problémy, které byste mohli chtít vzít v úvahu. Pokud pracujete s relačním modelem, nemusíte být přítomné ve stejném stupni:

* transakce s mnoha relacemi ukazující na stejnou entitu.
* transakce vyžadující silnou konzistenci napříč celou datovou sadou.

Při první výzvě je pravidlo pro povýšení v databázích NoSQL obecně denormalizace, která se jako kloubová operace vyprodukuje v distribuovaném systému efektivněji. Existují však problémy s návrhem, které přicházejí do hry s tímto přístupem. Podíváme se na příklad produktu, který se vztahuje k jedné kategorii a několika značkám:

:::image type="content" source="./media/relational-or-nosql/many-joins.png" alt-text="Back-end" a spojení k načtení dat. 

Kompromis je v tom, že čtení jsou efektivnější v denormalizovaném záznamu a stále efektivnější, protože se zvyšuje počet propojených entit. Stejně jako efektivita čtení se však zvyšuje s rostoucím počtem spojených entit v nenormalizované podobě, proto je příliš náročná údržba zachování entit v synchronizaci. Jedním ze způsobů, jak tento obchod snížit, je vytvoření [hybridního datového modelu](https://docs.microsoft.com/azure/cosmos-db/modeling-data#hybrid-data-models).

I když je k dispozici větší flexibilita v databázích NoSQL, abyste mohli řešit tyto kompromisy, větší flexibilita může také vytvořit další rozhodnutí o návrhu. Projděte si náš článek [jak modelovat a rozdělit data na Azure Cosmos DB s využitím reálného světa](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example), který zahrnuje přístup k zachování [denormalizovaných uživatelských dat v synchronizaci](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example#denormalizing-usernames) , kde uživatelé nepracují jenom v různých oddílech, ale v různých kontejnerech.

S ohledem na silnou konzistenci se to bude vyžadovat v celé datové sadě. V případech, kdy je to nezbytné, se však může jednat o výzvu v distribuovaných databázích. Aby se zajistila silná konzistence, je potřeba synchronizovat data napříč všemi replikami a oblastmi předtím, než je povolíte klientům, aby si ji mohli přečíst. To může zvýšit latenci čtení.

Znovu Azure Cosmos DB nabízí větší flexibilitu než relační databáze pro různé kompromisy, které jsou zde relevantní, ale u implementací malého rozsahu může tento přístup přidat další důležité informace o návrhu. Další podrobnosti k tomuto tématu najdete v našem článku o [kompromisech v konzistenci, dostupnosti a výkonu](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs) .

## <a name="next-steps"></a>Další kroky

Naučte se spravovat účet Azure Cosmos a další koncepty:

* [Jak spravovat účet Azure Cosmos](how-to-manage-database-account.md)
* [Globální distribuce](distribute-data-globally.md)
* [Úrovně konzistence](consistency-levels.md)
* [Práce s kontejnery a položkami Azure Cosmos](account-databases-containers-items.md)
* [Koncový bod služby virtuální sítě pro účet Azure Cosmos](how-to-configure-vnet-service-endpoint.md)
* [IP-brána firewall pro účet Azure Cosmos](how-to-configure-firewall.md)
* [Postup přidání a odebrání oblastí Azure do účtu Azure Cosmos](how-to-manage-database-account.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
