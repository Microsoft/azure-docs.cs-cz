---
title: Úvod do služby Azure Cosmos DB
description: Přečtěte si něco o službě Azure Cosmos DB. Tato globálně distribuovaná databáze s více modely je navržená s ohledem na nízkou latenci, elastickou škálovatelnost a vysokou dostupnost a nabízí nativní podporu pro data NoSQL.
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: overview
ms.date: 12/18/2018
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 0d4ae2ce567da0df9a15f229d3fc12e06c4993af
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53608371"
---
# <a name="welcome-to-azure-cosmos-db"></a>Vítá vás Azure Cosmos DB

Dnešní aplikace musí být vždy online a s velmi rychlou odezvou. Pokud chcete dosáhnout nízké latence a vysoká dostupnost, instancí těchto aplikací je nutné nasadit v datových centrech, která jsou blízko uživatelům. Aplikace potřebovat reagovat v reálném čase na velké změny využití v obdobích špičky, ukládat rostoucímu množství dat a tato data zpřístupníte uživatelům v milisekundách.

Azure Cosmos DB je globálně distribuovaná a vícemodelová databázová služba od Microsoftu. Stačí kliknout na tlačítko a Azure Cosmos DB vám umožní elasticky a nezávisle škálovat propustnost a úložiště v řadě geografických oblastí Azure. Můžete Elasticky škálovat propustnost a úložiště a využívat přístup k datům rychlé, jeden milisekund použití oblíbených rozhraní API mezi SQL, MongoDB, Apache Cassandra, tabulek nebo Gremlin. Cosmos DB poskytuje komplexní [smlouvy o úrovni služeb](https://aka.ms/acdbsla) (SLA) pro propustnosti, latence, dostupnosti a záruky konzistence, něco žádné jiné databázové služby můžou nabízet.

[Službu Azure Cosmos DB můžete vyzkoušet zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure, poplatků a závazků.

> [!div class="nextstepaction"]
> [Vyzkoušejte si Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/)

![Azure Cosmos DB je globálně distribuovaná databázová služba od Microsoftu, která nabízí elastické škálování na více instancí, zaručenou nízkou latenci, pět modelů konzistence a komplexní zaručené smlouvy SLA.](./media/introduction/azure-cosmos-db.png)

## <a name="key-benefits"></a>Klíčové výhody

### <a name="turnkey-global-distribution"></a>Globální distribuce na klíč

Cosmos DB umožňuje sestavovat s velmi rychlou odezvou a vysoce dostupné aplikace po celém světě. Cosmos DB transparentně replikuje vaše data co nejblíž uživatelům, aby vaši uživatelé mohli komunikovat s replika dat je nejblíže.

Cosmos DB umožňuje přidání nebo odebrání všech oblastí Azure na účtu Cosmos kdykoli kliknutím na tlačítko. Cosmos DB bezproblémově replikuje vaše data do všech oblastí, které jsou spojené s vaším účtem Cosmos, zatímco aplikace stále s vysokou dostupností do vícenásobného navádění služby.

Další informace najdete v tématu [globální distribuce](distribute-data-globally.md) článku.

### <a name="always-on"></a>Always "On"

Tím, že těsně integrovaná se sadou infrastrukturu Azure a [transparentní multimasterovou replikací](global-dist-under-the-hood.md), Cosmos DB poskytuje 99,999 % [vysoké dostupnosti](high-availability.md) pro obě operace čtení a zápisu. Cosmos DB také poskytuje schopnost regionální převzetí služeb při selhání účtu Cosmos vyvolat prostřednictvím kódu programu (nebo se přes portál). Tato funkce vám pomůže zajistit, že zatímco databáze Cosmos může automaticky převzetí služeb při selhání, zbytek aplikace je navržený tak, aby převzetí služeb při selhání při regionálního.

### <a name="elastic-scalability-of-throughput-and-storage-worldwide"></a>Elastické škálovatelnosti propustnosti a úložiště po celém světě

Navrhované daným transparentní horizontální dělení a multimasterovou replikací, Cosmos DB nabízí bezprecedentní elastickou škálovatelnost pro zápisy a čtení, všechny na celém světě. Můžete Elasticky vertikálně navýšit kapacitu z tisíců na stovky milionů požadavků za sekundu na světě pomocí jediného volání rozhraní API a platíte jenom za propustnost (a úložiště), které potřebujete. Tato funkce umožňuje zacházet s neočekávaným nárůstem vaše úlohy bez nutnosti zřizovat pro ve špičce. V tématu [dělení ve službě Cosmos DB](partitioning-overview.md), [zřízenou propustnost na kontejnerech a databází](set-throughput.md), a [škálování zřízená propustnost globálně](scaling-throughput.md).

### <a name="guaranteed-low-latency-at-99th-percentile-worldwide"></a>Garance nízké latence na 99. percentilu po celém světě

Pomocí služby Cosmos DB, můžete vytvořit s velmi rychlou odezvou, aplikace v globálním měřítku. S jeho protokol nové multimasterovou replikací a blokátory a [databázový stroj optimalizovaný pro zápis](index-policy.md), Cosmos DB garantuje latenci kratší než 10 ms pro obě, čtení a zápisy na 99. percentilu po celém světě (indexované) . Tato možnost umožňuje trvalý příjem dat a mimořádně rychlé dotazy pro aplikace s velmi rychlou odezvou.

### <a name="precisely-defined-multiple-consistency-choices"></a>Přesně vymezit, více možností konzistence

Už máte aby extrémně [kompromisy mezi konzistencí, dostupností, latencí a programovatelnosti](consistency-levels-tradeoffs.md). Protokol multimasterovou replikací cosmos DB nabízí pečlivě navržený [pět jasně definovaných voleb konzistence](consistency-levels.md) – silná, omezená neaktuálnost, konzistence předpon, relace a konečný výsledek – výsledkem je intuitivní programovací model s nízkou latencí a vysoká dostupnost pro globálně distribuované aplikace.

### <a name="no-schema-or-index-management"></a>Žádné schéma nebo správu indexů

Schéma databáze a indexy aplikace schématu synchronizace je zvláště bolestivé pro globálně distribuované aplikace. Nicméně pomocí služby Cosmos DB, nepotřebujete řešit schémata a indexy. Databázový stroj je zcela nezávislý na schématu.  Protože není potřeba Správa schématu a indexu je potřeba, také nemusíte starat o výpadky aplikací průběhu migrace schémat. Cosmos DB [automaticky indexuje všechna data](index-policy.md) – žádné schéma, žádné indexy vyžaduje – a zajišťuje rychlé dotazy.

### <a name="battle-tested-database-service"></a>Testovat Zocelenou databázová služba

Cosmos DB je podkladovou službu v Azure. Pro téměř deset let Cosmos DB se použil v řadě produktů společnosti Microsoft pro nepostradatelné aplikace v globálním měřítku, včetně Skype, Xbox, Office 365, Azure a mnoha dalších. V současné době Cosmos DB je jedním z nejrychleji se rozvíjejících služby v Azure používá mnoho externí zákazníky a aplikace, které vyžadují elastické škálování a/nebo na klíč s více – datacenter a více – oblast, multimasterovou replikace s nízkou latencí a vysokou dostupnost z obou čtení a zápisu.

### <a name="ubiquitous-regional-presence"></a>Všudypřítomná místní přítomnost

Cosmos DB je k dispozici ve všech oblastech Azure po celém světě, včetně 54 + oblasti ve veřejném cloudu, Azure China 21Vianet, Azure Germany, Azure Government a Azure Government pro oddělení nuclear Information. Zobrazit [místní přítomnost služby Cosmos DB](regional-presence.md).

### <a name="secure-by-default-and-enterprise-ready"></a>Zabezpečení ve výchozím nastavení a připraveno pro podniky

Cosmos DB má certifikaci pro [širokou škálu standardy pro dodržování předpisů](compliance.md). Kromě toho je v klidovém stavu a pohybu šifrovaný všechna data ve službě Cosmos DB. Cosmos DB poskytuje úroveň autorizace řádek a splňuje striktní bezpečnostní standardy.

### <a name="significant-tco-savings"></a>Výrazné úspory celkových nákladů na vlastnictví

Cosmos DB je plně spravovaná služba, budete muset již nebude spravovat a provozovat nasazení složitých více datacenter a upgrady vašeho softwaru databáze, platit pro podporu, programů nebo operace. Zobrazit [optimalizovat náklady pomocí služby Cosmos DB](total-cost-ownership.md).

### <a name="industry-leading-comprehensive-slas"></a>Špičkové komplexní smlouvy SLA

Cosmos DB je první a jedinou službou, která nabízí [špičkové komplexní smlouvy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) včetně 99,999 % vysokou dostupnost, čtení a zápis latence na 99. percentilu, zaručenou propustnost a konzistenci.

### <a name="apache-spark--cosmos-db--operational-analytics-at-global-scale"></a>Apache Sparku a Cosmos DB = provozní analýzy v globálním měřítku

Můžete spustit [Spark](spark-connector.md) s daty uloženými ve službě Cosmos DB. Díky této funkci můžete provádět s nízkou latencí, provozní analýzy v globálním měřítku bez dopadu na provoz přímo službou Cosmos DB transakčními úlohami.

### <a name="native-wire-compatible-mongodb-cassandra-gremlin-and-azure-table-storage-apis"></a>Nativní kompatibilního se při přenosu MongoDB, Cassandra, Gremlin a API pro Azure Table Storage

Cosmos DB nabízí široký výběr rozhraní API k aktualizaci a dotazování na data uložená v databázi Cosmos. Ve výchozím nastavení [můžete použít SQL](how-to-sql-query.md) aktualizaci a dotazování na data v databázi Cosmos.

Cosmos DB také implementuje [Cassandra](cassandra-introduction.md), [MongoDB](mongodb-introduction.md), a [Gremlin](graph-introduction.md) svážete protokoly přímo ve službě. To umožňuje bod Cassandra, MongoDB, nebo ovladače klienta Gremlin (Nástroje) přímo do vaší služby Cosmos databáze a Cosmos DB můžete využít jako plně spravovanou, globálně distribuované a Cassandra/MongoDB/Gremlin jako-service bez nutnosti platit za licence, podpora, nebo provozní náklady na dodavatele databáze. Nativní podpora pro Storage Cassandra, MongoDB a Gremlin/Table storage API poskytuje řadu výhod. Můžete:

* Snadno migrate databáze Cassandra, MongoDB, Gremlin a Table Storage do služby Cosmos DB. 
* Zachovat velkou část vaší aplikace logiky beze změny s rozhraním Cassandra, MongoDB, Gremlin a Table storage rozhraní služby Cosmos DB.
* Zachovat aplikaci přenosné a nadále zůstávají nezávislá na cloud dodavatele.
* Získání oboru přední a finančně zajištěné smlouvy SLA pro Cassandra, MongoDB a Gremlin databáze Cosmos DB používá technologii. 
* Elasticky škálujte propustnost a úložiště pro vaše Cassandra a databáze MongoDB podle svých potřeb a Plaťte jenom za propustnost a úložiště, které potřebujete. To vede k výrazné úspory nákladů.

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Řešení, kterým služba Azure Cosmos DB přináší výhody

[Garantovaná](https://azure.microsoft.com/support/legal/sla/cosmos-db/) vysoká dostupnost, vysoká propustnost, nízká latence a přizpůsobitelná konzistence ve službě Azure Cosmos DB bude výhodná pro všechny [webové, mobilní a herní aplikace a aplikace IoT](use-cases.md), které potřebují zpracovávat obrovské množství dat, čtení a zápisů v [globálním](distribute-data-globally.md) měřítku s dobou odezvy téměř v reálném čase. Další informace o možnostech použití služby Azure Cosmos DB pro [IoT a telematiku](use-cases.md#iot-and-telematics), [maloobchod a marketing](use-cases.md#retail-and-marketing), [hry](use-cases.md#gaming) a [webové a mobilní aplikace](use-cases.md#web-and-mobile-applications)

## <a name="next-steps"></a>Další postup

Další informace o Cosmos DB [globální distribuce](distribute-data-globally.md) a [dělení](partitioning-overview.md) možnosti.

Začínáme se službou Azure Cosmos DB s využitím jedné ze čtyř šablon Rychlý start:

* [Začínáme s rozhraním SQL API služby Azure Cosmos DB](create-sql-api-dotnet.md)
* [Začínáme s Azure Cosmos DB API pro MongoDB](create-mongodb-nodejs.md)
* [Začínáme s rozhraním Cassandra API služby Azure Cosmos DB](create-cassandra-dotnet.md)
* [Začínáme s rozhraním Gremlin API služby Azure Cosmos DB](create-graph-dotnet.md)
* [Začínáme s rozhraním Table API služby Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Vyzkoušejte si Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/)
