---
title: Úvod do Azure Cosmos DB
description: Přečtěte si něco o službě Azure Cosmos DB. Tato globálně distribuovaná databáze s více modely je navržená s ohledem na nízkou latenci, elastickou škálovatelnost a vysokou dostupnost a nabízí nativní podporu pro data NoSQL.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: overview
ms.date: 10/23/2019
ms.openlocfilehash: 6292c3c2d928581d0564fe457342ea7b1e17111c
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983020"
---
# <a name="welcome-to-azure-cosmos-db"></a>Vítá vás Azure Cosmos DB

Dnešní aplikace musí být vysoce citlivé a vždy online. Chcete-li dosáhnout nízké latence a vysoké dostupnosti, instance těchto aplikací je třeba nasadit v datových centrech, které jsou blízko k jejich uživatelům. Aplikace musí reagovat v reálném čase na velké změny v používání ve špičce, ukládat stále rostoucí objemy dat a zpřístupnit tato data uživatelům v milisekundách.

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu s více modely. Pomocí kliknutí na tlačítko cosmos DB umožňuje elasticky a nezávisle škálovat propustnost a úložiště v libovolném počtu oblastí Azure po celém světě. Můžete elasticky škálovat propustnost a úložiště a využít rychlý, jednociferný milisekundový přístup k datům pomocí vašeho oblíbeného rozhraní API, včetně: SQL, MongoDB, Cassandra, Tables nebo Gremlin. Cosmos DB poskytuje komplexní [smlouvy o úrovni služeb](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) (SLA) pro propustnost, latenci, dostupnost a konzistenci záruky, něco, co žádná jiná databázová služba nabízí.

Azure [Cosmos DB můžete vyzkoušet zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure, bezplatně a závazků, nebo použít [bezplatnou úroveň Azure Cosmos DB](optimize-dev-test.md#azure-cosmos-db-free-tier) k získání účtu s prvními 400 RU/s a 5 GB úložiště zdarma.

> [!div class="nextstepaction"]
> [Vyzkoušejte Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/)

![Azure Cosmos DB je globálně distribuovaná databázová služba od Microsoftu, která nabízí elastické škálování na více instancí, zaručenou nízkou latenci, pět modelů konzistence a komplexní zaručené smlouvy SLA.](./media/introduction/azure-cosmos-db.png)

## <a name="key-benefits"></a>Klíčové výhody

### <a name="turnkey-global-distribution"></a>Globální distribuce na klíč

Cosmos DB umožňuje vytvářet vysoce citlivé a vysoce dostupné aplikace po celém světě. Cosmos DB transparentně replikuje vaše data, ať jsou vaši uživatelé kdekoli, takže uživatelé mohou pracovat s replikou dat, která jsou jim nejblíže.

Cosmos DB umožňuje přidat nebo odebrat některou z oblastí Azure na váš účet Cosmos kdykoliv kliknutím na tlačítko. Cosmos DB bude bez problémů replikovat vaše data do všech oblastí spojených s vaším účtem Cosmos, zatímco vaše aplikace je i nadále vysoce dostupná díky *funkcím více naváděcích* služeb. Další informace naleznete v článku [globální distribuce.](distribute-data-globally.md)

### <a name="always-on"></a>Vždy zapnuto

Díky hluboké integraci s infrastrukturou Azure a [transparentní replikaci více hlavních serverů](global-dist-under-the-hood.md)poskytuje Cosmos DB [99,999 % vysokou dostupnost](high-availability.md) pro čtení i zápisy. Cosmos DB také poskytuje možnost programově (nebo prostřednictvím portálu) vyvolat místní převzetí služeb při selhání vašeho účtu Cosmos. Tato funkce pomáhá zajistit, že vaše aplikace je navržen a převzetí služeb při selhání v případě regionální havárie.

### <a name="elastic-scalability-of-throughput-and-storage-worldwide"></a>Elastická škálovatelnost propustnosti a úložiště po celém světě

Cosmos DB, navržený s transparentním horizontálním dělením a replikací s více servery, nabízí bezprecedentní elastickou škálovatelnost pro vaše zápisy a čtení po celém světě. Můžete elasticky vertikálně vertikálně škálovat z tisíců na stovky milionů požadavků za sekundu po celém světě, s jedním voláním rozhraní API a platit jenom za propustnost (a úložiště), které potřebujete. Tato funkce vám pomůže vypořádat se s neočekávanými špičkami v úlohách, aniž byste museli příliš zjišnit pro špičku. Další informace naleznete [v tématu dělení v Cosmos DB](partitioning-overview.md), [zřízená propustnost na kontejnery a databáze](set-throughput.md)a [škálování zřízená propustnost globálně](scaling-throughput.md).

### <a name="guaranteed-low-latency-at-99th-percentile-worldwide"></a>Zaručená nízká latence na 99 percentilu po celém světě

Pomocí Cosmos DB můžete vytvářet vysoce responzivní aplikace v měřítku planety. Díky novému protokolu replikace více hlavních serverů a [databázovému stroji optimalizovanému](index-policy.md)pro západku a zápisu cosmos DB zaručuje méně než 10 ms latence pro oba, čte (indexováno) a zapisuje na 99 percentilu po celém světě. Tato funkce umožňuje trvalé přijímdat data a bleskově rychlé dotazy pro vysoce responzivní aplikace.

### <a name="precisely-defined-multiple-consistency-choices"></a>Přesně definované, více možností konzistence

Při vytváření globálně distribuovaných aplikací v Cosmos DB už nemusíte provádět extrémní [kompromisy mezi konzistencí, dostupností, latencí a propustností](consistency-levels-tradeoffs.md). Multi-master replikační protokol Cosmos DB je pečlivě navržen tak, aby nabízel [pět dobře definovaných voleb](consistency-levels.md) - konzistence*silných*, *ohraničených neaktuálnost*, *relace*, *konzistentní předpona*a *eventuální* – pro intuitivní programovací model s nízkou latencí a vysokou dostupností pro globálně distribuovanou aplikaci.

### <a name="no-schema-or-index-management"></a>Žádná správa schématu nebo indexu

Udržování schématu databáze a indexů v synchronizaci se schématem aplikace je obzvláště bolestivé pro globálně distribuované aplikace. S Cosmos DB, není nutné řešit správu schématu nebo indexu. Databázový stroj je plně šepomně nezávislá.  Vzhledem k tomu, že není vyžadováno žádné správu schémat a indexu, nemusíte se při migraci schémat také starat o prostoje aplikací. Cosmos DB [automaticky indexuje všechna data](index-policy.md) a rychle obsluhuje dotazy.

### <a name="battle-tested-database-service"></a>Bitva testována databázová služba

Cosmos DB je základní služba v Azure. Cosmos DB používá téměř deset let mnoho produktů Microsoftu pro klíčové aplikace v globálním měřítku, včetně Skype, Xbox, Office 365, Azure a mnoha dalších. Dnes je Cosmos DB jednou z nejrychleji rostoucích služeb v Azure, kterou používá mnoho externích zákazníků a důležitých aplikací, které vyžadují elastické škálování, globální distribuci na klíč, replikaci více hlavních serverů pro nízkou latenci a vysokou dostupnost čtení i zápisů.

### <a name="ubiquitous-regional-presence"></a>Všudypřítomná regionální přítomnost

Cosmos DB je k dispozici ve všech oblastech Azure po celém světě, včetně více než 54 oblastí ve veřejném cloudu, [Azure China 21Vianet](https://www.azure.cn/en-us/), Azure Germany, Azure Government a Azure Government for Department of Defense (DoD). Viz [regionální přítomnost Cosmos DB](regional-presence.md).

### <a name="secure-by-default-and-enterprise-ready"></a>Zabezpečení ve výchozím nastavení a připraveno pro podnik

Cosmos DB je certifikován pro [širokou škálu standardů shody](compliance.md). Kromě toho jsou všechna data v Cosmos DB šifrována v klidovém stavu a v pohybu. Cosmos DB poskytuje autorizaci na úrovni řádků a dodržuje přísné bezpečnostní standardy.

### <a name="significant-tco-savings"></a>Významné úspory tco

Vzhledem k tomu, že Cosmos DB je plně spravovaná služba, nemusíte už spravovat a provozovat komplexní nasazení a upgrady databázového softwaru pro více datových center, platit za podporu, licencování nebo operace nebo muset zřídit databázi pro špičkové zatížení. Další informace naleznete v [tématu Optimalizace nákladů s Cosmos DB](total-cost-ownership.md).

### <a name="industry-leading-comprehensive-slas"></a>Špičková komplexní smlouvy SLA

Cosmos DB je první a jediná služba, která nabízí [špičkové komplexní smlouvy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) zahrnující 99,999% vysokou dostupnost, latenci čtení a zápisu na 99 percentilu, zaručenou propustnost a konzistenci.

### <a name="globally-distributed-operational-analytics-and-ai-with-natively-built-in-apache-spark"></a>Globálně distribuovaná provozní analytika a umělá ai s nativně vestavěnou apache spark

[Spark](spark-connector.md) můžete spustit přímo na datech uložených v Cosmos DB. Tato funkce umožňuje provést provozní analýzy s nízkou latencí v globálním měřítku, aniž by to mělo dopad na transakční úlohy operující přímo s Cosmos DB. Další informace naleznete [v tématu Globálně distribuovaná provozní analýza](lambda-architecture.md).

### <a name="develop-applications-on-cosmos-db-using-popular-open-source-software-oss-apis"></a>Vývoj aplikací na Cosmos DB pomocí populárních open source software (OSS) API

Cosmos DB nabízí výběr api pro práci s daty uloženými v databázi Cosmos. Ve výchozím nastavení [můžete použít SQL](how-to-sql-query.md) (základní rozhraní API) pro dotazování databáze Cosmos. Cosmos DB také implementuje rozhraní API pro [Cassandra](cassandra-introduction.md), [MongoDB](mongodb-introduction.md), [Gremlin](graph-introduction.md) a [Azure Table Storage](table-introduction.md). Ovladače (a nástroje) pro běžně používané NoSQL (např. MongoDB, Cassandra, Gremlin) můžete překážet přímo do databáze Cosmos. Podporou drátových protokolů běžně používaných nosql API cosmos DB umožňuje:

* Snadno migrujte aplikaci do Cosmos DB při zachování významné části aplikační logiky.
* Udržujte aplikaci přenosnou a nadále zůstat cloud dodavatele matnostik.
* Získejte plně spravovanou cloudovou službu s předními, finančně podporovanými službami SLA pro společná nosql API. 
* Elasticky škálujte zřízenou propustnost a úložiště pro vaše databáze na základě vašich potřeb a plaťte jenom za propustnost a úložiště, které potřebujete. To vede k významným úsporám nákladů.

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Řešení, kterým služba Azure Cosmos DB přináší výhody

Všechny [webové, mobilní, herní a ioT aplikace,](use-cases.md) které potřebuje zpracovat obrovské množství dat, čtení a zápisy v [globálním měřítku](distribute-data-globally.md) s téměř reálnými dobami odezvy pro různé údaje, budou těžit z [garantované vysoké dostupnosti](https://azure.microsoft.com/support/legal/sla/cosmos-db/)Cosmos DB , vysoké propustnosti, nízké latence a laditelné konzistence. Přečtěte si, jak lze Azure Cosmos DB použít k vytváření [IoT a telematiky](use-cases.md#iot-and-telematics), [maloobchodu a marketingu](use-cases.md#retail-and-marketing), [hraní her](use-cases.md#gaming) a [webových a mobilních aplikací](use-cases.md#web-and-mobile-applications).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o základní koncepty Cosmos DB [na klíč globální distribuce](distribute-data-globally.md) a [dělení](partitioning-overview.md) a [zřízené propustnost](request-units.md).

Začínáme se službou Azure Cosmos DB s využitím jedné ze čtyř šablon Rychlý start:

* [Začínáme s rozhraním SQL API služby Azure Cosmos DB](create-sql-api-dotnet.md)
* [Začínáme s rozhraním API Azure Cosmos DB pro MongoDB](create-mongodb-nodejs.md)
* [Začínáme s rozhraním Cassandra API služby Azure Cosmos DB](create-cassandra-dotnet.md)
* [Začínáme s rozhraním Gremlin API služby Azure Cosmos DB](create-graph-dotnet.md)
* [Začínáme s rozhraním Table API služby Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Vyzkoušejte si Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/)
