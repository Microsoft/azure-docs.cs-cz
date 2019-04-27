---
title: Úvod do služby Azure Cosmos DB
description: Přečtěte si něco o službě Azure Cosmos DB. Tato globálně distribuovaná databáze s více modely je navržená s ohledem na nízkou latenci, elastickou škálovatelnost a vysokou dostupnost a nabízí nativní podporu pro data NoSQL.
author: rimman
ms.service: cosmos-db
ms.topic: overview
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: a965db4334e88d032f767bd7b855dea6f3ff174f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61042957"
---
# <a name="welcome-to-azure-cosmos-db"></a>Vítá vás Azure Cosmos DB

Dnešní aplikace musí být vždy online a s velmi rychlou odezvou. Pokud chcete dosáhnout nízké latence a vysoká dostupnost, instancí těchto aplikací je nutné nasadit v datových centrech, která jsou blízko uživatelům. Aplikace potřebovat reagovat v reálném čase na velké změny využití v obdobích špičky, ukládat rostoucímu množství dat a tato data zpřístupníte uživatelům v milisekundách.

Azure Cosmos DB je globálně distribuovaná a vícemodelová databázová služba od Microsoftu. Kliknutím na tlačítko Cosmos DB umožňuje Elasticky a nezávisle škálovat propustnost a úložiště napříč libovolným počtem oblastí Azure po celém světě. Můžete Elasticky škálovat propustnost a úložiště a využívat přístup k datům rychlé, jeden milisekund pomocí vašeho oblíbeného rozhraní API, včetně SQL, MongoDB, Cassandra, tabulek nebo Gremlin. Cosmos DB poskytuje komplexní [smlouvy o úrovni služeb](https://aka.ms/acdbsla) (SLA) pro propustnosti, latence, dostupnosti a záruky konzistence, něco žádné jiné databázové služby nabízí.

[Službu Azure Cosmos DB můžete vyzkoušet zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure, poplatků a závazků.

> [!div class="nextstepaction"]
> [Vyzkoušejte si Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/)

![Azure Cosmos DB je globálně distribuovaná databázová služba od Microsoftu, která nabízí elastické škálování na více instancí, zaručenou nízkou latenci, pět modelů konzistence a komplexní zaručené smlouvy SLA.](./media/introduction/azure-cosmos-db.png)

## <a name="key-benefits"></a>Klíčové výhody

### <a name="turnkey-global-distribution"></a>Globální distribuce na klíč

Cosmos DB umožňuje sestavovat s velmi rychlou odezvou a vysoce dostupné aplikace po celém světě. Cosmos DB transparentně replikuje vaše data co nejblíž uživatelům, aby vaši uživatelé mohli komunikovat s repliky dat, která je nejblíž k nim.

Cosmos DB umožňuje přidání nebo odebrání všech oblastí Azure na účtu Cosmos kdykoli kliknutím na tlačítko. Cosmos DB bezproblémově replikovat data do všech oblastí, které jsou spojené s vaším účtem Cosmos, i když vaše aplikace zůstává s vysokou dostupností, Děkujeme k *vícenásobné navádění* funkce této služby. Další informace najdete v tématu [globální distribuce](distribute-data-globally.md) článku.

### <a name="always-on"></a>Stálé připojení

Tím, že těsně integrovaná se sadou infrastrukturu Azure a [transparentní multimasterovou replikací](global-dist-under-the-hood.md), Cosmos DB poskytuje [99,999 % vysokou dostupnost](high-availability.md) pro obě operace čtení a zápisu. Cosmos DB také poskytuje schopnost regionální převzetí služeb při selhání účtu Cosmos vyvolat prostřednictvím kódu programu (nebo se přes portál). Tato funkce pomáhá zajistit, že vaše aplikace slouží k převzetí služeb při selhání v případě regionálního.

### <a name="elastic-scalability-of-throughput-and-storage-worldwide"></a>Elastické škálovatelnosti propustnosti a úložiště po celém světě

Navrhované daným transparentní horizontální dělení a multimasterovou replikací, Cosmos DB nabízí bezprecedentní elastickou škálovatelnost pro zápisy a čtení, všechny na celém světě. Můžete Elasticky vertikálně navýšit kapacitu z tisíců na stovky milionů požadavků za sekundu na světě pomocí jediného volání rozhraní API a platíte jenom za propustnost (a úložiště), které potřebujete. Tato funkce umožňuje zacházet s neočekávaným nárůstem vaše úlohy bez nutnosti zřizovat pro ve špičce. Další informace najdete v tématu [dělení ve službě Cosmos DB](partitioning-overview.md), [zřízenou propustnost na kontejnerech a databází](set-throughput.md), a [škálování zřízená propustnost globálně](scaling-throughput.md).

### <a name="guaranteed-low-latency-at-99th-percentile-worldwide"></a>Garance nízké latence na 99. percentilu po celém světě

Pomocí služby Cosmos DB, můžete vytvořit s velmi rychlou odezvou, aplikace v globálním měřítku. S jeho protokol nové multimasterovou replikací a blokátory a [databázový stroj optimalizovaný pro zápis](index-policy.md), Cosmos DB garantuje latenci kratší než 10 ms pro obě, čtení a zápisy na 99. percentilu po celém světě (indexované) . Tato možnost umožňuje trvalý příjem dat a mimořádně rychlé dotazy pro aplikace s velmi rychlou odezvou.

### <a name="precisely-defined-multiple-consistency-choices"></a>Přesně vymezit, více možností konzistence

Při vytváření globálně distribuovaných aplikací ve službě Cosmos DB, nepotřebujete provádět extreme [kompromisy mezi konzistencí, dostupností, latence a propustnosti](consistency-levels-tradeoffs.md). Protokol multimasterovou replikací cosmos DB nabízí pečlivě navržený [pět jasně definovaných voleb konzistence](consistency-levels.md) - *silné*, *omezená neaktuálnost*, *relace*, *konzistentní předpona*, a *konečné* – výsledkem je intuitivní programovací model s nízkou latencí a vysokou dostupností pro vaše globálně distribuované aplikace.

### <a name="no-schema-or-index-management"></a>Žádné schéma nebo správu indexů

Schéma databáze a indexy aplikace schématu synchronizace je zvláště bolestivé pro globálně distribuované aplikace. Pomocí služby Cosmos DB není potřeba řešit schéma nebo správu indexů. Databázový stroj je zcela nezávislý na schématu.  Protože není potřeba Správa schématu a indexu je potřeba, také nemusíte starat o výpadky aplikací průběhu migrace schémat. Cosmos DB [automaticky indexuje všechna data](index-policy.md) a slouží rychlé dotazy.

### <a name="battle-tested-database-service"></a>Testovat Zocelenou databázová služba

Cosmos DB je podkladovou službu v Azure. Pro téměř deset let Cosmos DB se použil v řadě produktů společnosti Microsoft pro nepostradatelné aplikace v globálním měřítku, včetně Skype, Xbox, Office 365, Azure a mnoha dalších. V současné době Cosmos DB je jedním z nejrychleji se rozvíjejících služeb v Azure, používané mnoha externí zákazníky a důležité podnikové aplikace, které vyžadují pružné škálování, globální distribuce na klíč, multimasterovou replikací s nízkou latencí a vysokou dostupnost z obou čtení a zápisu.

### <a name="ubiquitous-regional-presence"></a>Všudypřítomná místní přítomnost

Cosmos DB je k dispozici ve všech oblastech Azure po celém světě, včetně 54 + oblasti ve veřejném cloudu [Azure China 21Vianet](https://www.azure.cn/en-us/), Azure Germany, Azure Government a Azure Government pro Ministerstvo obrany (DoD). Zobrazit [místní přítomnost služby Cosmos DB](regional-presence.md).

### <a name="secure-by-default-and-enterprise-ready"></a>Zabezpečení ve výchozím nastavení a připraveno pro podniky

Cosmos DB má certifikaci pro [širokou škálu standardy pro dodržování předpisů](compliance.md). Kromě toho je v klidovém stavu a pohybu šifrovaný všechna data ve službě Cosmos DB. Cosmos DB poskytuje úroveň autorizace řádek a splňuje striktní bezpečnostní standardy.

### <a name="significant-tco-savings"></a>Výrazné úspory celkových nákladů na vlastnictví

Cosmos DB je plně spravovaná služba, už nepotřebujete spravovat a provozovat nasazení složitých více datacenter a upgrady vašeho softwaru databáze, platit pro podporu, programů nebo operace nebo muset zřídit databáze ve špičce. Další informace najdete v tématu [optimalizovat náklady pomocí služby Cosmos DB](total-cost-ownership.md).

### <a name="industry-leading-comprehensive-slas"></a>Špičkové komplexní smlouvy SLA

Cosmos DB je první a jedinou službou, která nabízí [špičkové komplexní smlouvy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) včetně 99,999 % vysokou dostupnost, čtení a zápis latence na 99. percentilu, zaručenou propustnost a konzistenci.

### <a name="globally-distributed-operational-analytics-with-spark"></a>Globálně distribuované provozní analýzy se Sparkem

Můžete spustit [Spark](spark-connector.md) přímo s daty uloženými ve službě Cosmos DB. Díky této funkci můžete provádět s nízkou latencí, provozní analýzy v globálním měřítku bez dopadu na provoz přímo službou Cosmos DB transakčními úlohami. Další informace najdete v tématu [globálně Distribuovaný operační analýza](lambda-architecture.md).

### <a name="develop-applications-on-cosmos-db-using-popular-nosql-apis"></a>Vývoj aplikací na Cosmos DB s použitím oblíbených rozhraní API NoSQL

Cosmos DB nabízí široký výběr rozhraní API pro práci s daty ukládají do databáze Cosmos. Ve výchozím nastavení [můžete použít SQL](how-to-sql-query.md) (základní rozhraní API) pro dotazování na databázi Cosmos. Cosmos DB také implementuje rozhraní API pro [Cassandra](cassandra-introduction.md), [MongoDB](mongodb-introduction.md), [Gremlin](graph-introduction.md) a [Azure Table Storage](table-introduction.md). Pak můžete ovladače klienta (a nástroje) pro běžně používané NoSQL (třeba Cassandra, MongoDB Gremlin) přímo k databázi Cosmos. Díky podpoře protokolů přenosu běžně používaných NoSQL API, Cosmos DB umožňuje:

* Snadno migrujte své aplikace do služby Cosmos DB při zachování významné části vaší aplikace logiky.
* Zachovat aplikaci přenosné a nadále zůstávají nezávislá na cloud dodavatele.
* Získání plně spravovaná Cloudová služba se počáteční a finančně zajištěné smlouvy o úrovni služeb pro společné rozhraní API typu NoSQL. 
* Elasticky škálujte zřízenou propustnost a úložiště pro vaše databáze podle svých potřeb a platíte jenom za propustnost a úložiště, které potřebujete. To vede k výrazné úspory nákladů.

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Řešení, kterým služba Azure Cosmos DB přináší výhody

Žádné [webové, mobilní a herní aplikace a aplikace IoT](use-cases.md) , který je potřeba zpracovat obrovská množství dat, čtení a zápisů v [globální škálování](distribute-data-globally.md) s odpovědí téměř v reálném bude přínosem časy pro širokou škálu dat z Cosmos DB [záruka vysoké dostupnosti](https://azure.microsoft.com/support/legal/sla/cosmos-db/), vysoké propustnosti, nízké latence a přizpůsobitelná konzistence. Další informace o použití služby Azure Cosmos DB k vytváření [IoT a telematiku](use-cases.md#iot-and-telematics), [maloobchodní prodej a marketing](use-cases.md#retail-and-marketing), [herní](use-cases.md#gaming) a [webové a mobilní aplikace](use-cases.md#web-and-mobile-applications).

## <a name="next-steps"></a>Další postup

Další informace o Cosmos DB základní koncepty [globální distribuce na klíč](distribute-data-globally.md) a [dělení](partitioning-overview.md) a [zřízená propustnost](request-units.md).

Začínáme se službou Azure Cosmos DB s využitím jedné ze čtyř šablon Rychlý start:

* [Začínáme s rozhraním SQL API služby Azure Cosmos DB](create-sql-api-dotnet.md)
* [Začínáme s Azure Cosmos DB přes rozhraní API pro MongoDB](create-mongodb-nodejs.md)
* [Začínáme s rozhraním Cassandra API služby Azure Cosmos DB](create-cassandra-dotnet.md)
* [Začínáme s rozhraním Gremlin API služby Azure Cosmos DB](create-graph-dotnet.md)
* [Začínáme s rozhraním Table API služby Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Vyzkoušejte si Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/)
