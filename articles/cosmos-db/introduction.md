---
title: Úvod do služby Azure Cosmos DB
description: Přečtěte si něco o službě Azure Cosmos DB. Tato globálně distribuovaná databáze s více modely je navržená s ohledem na nízkou latenci, elastickou škálovatelnost a vysokou dostupnost a nabízí nativní podporu pro data NoSQL.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: overview
ms.date: 10/23/2019
ms.openlocfilehash: 4e9d632880b792d16ffe752a55396c649c6688e5
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91398814"
---
# <a name="welcome-to-azure-cosmos-db"></a>Vítá vás Azure Cosmos DB

V dnešních aplikacích je nutné, aby byly vysoce reagovaly a vždy online. Aby bylo možné dosáhnout nízké latence a vysoké dostupnosti, je třeba instance těchto aplikací nasadit v datových centrech, které jsou blízko jejich uživatelů. Aplikace musí v reálném čase reagovat na velké změny využití v době špičky, ukládat stále větší objemy dat a zpřístupnit tato data uživatelům v milisekundách.

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Když kliknete na tlačítko, Cosmos DB vám umožní elasticky a nezávisle škálovat propustnost a úložiště napříč libovolným počtem oblastí Azure po celém světě. Můžete elasticky škálovat propustnost a úložiště a využít rychlý přístup k datům v řádu milisekund pomocí vašich oblíbených rozhraní API, včetně: SQL, MongoDB, Cassandra, Tables nebo Gremlin. Cosmos DB poskytuje komplexní [smlouvy o úrovni služeb](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) (SLA) kvůli propustnosti, latenci, dostupnosti a zárukám konzistence bez dalších nabídek databázových služeb.

Můžete [vyzkoušet Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure, zdarma a závazků nebo využít [Azure Cosmos dB úrovně Free](optimize-dev-test.md#azure-cosmos-db-free-tier) k získání účtu s prvním 400 ru/s a 5 GB volného místa.

> [!div class="nextstepaction"]
> [Vyzkoušet Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/)

:::image type="content" source="./media/introduction/azure-cosmos-db.png" alt-text="Azure Cosmos DB je globálně distribuovaná databázová služba od Microsoftu, která nabízí elastické škálování na více instancí, zaručenou nízkou latenci, pět modelů konzistence a komplexní zaručené smlouvy SLA." border="false":::

## <a name="key-benefits"></a>Klíčové výhody

### <a name="turnkey-global-distribution"></a>Globální distribuce klíč

Cosmos DB vám umožní vytvářet vysoce dostupné a vysoce dostupné aplikace po celém světě. Cosmos DB transparentně replikuje data všude, kde jsou vaši uživatelé, takže vaši uživatelé můžou pracovat s replikou dat, která jsou nejblíže.

Cosmos DB umožňuje kdykoli přidat nebo odebrat libovolné oblasti Azure do účtu Cosmos a kliknout na tlačítko. Cosmos DB bude bez problémů replikovat vaše data do všech oblastí přidružených k vašemu účtu Cosmos, zatímco vaše aplikace bude dál vysoce dostupná, díky funkcím pro *více koncových* částí služby. Další informace najdete v článku o [globální distribuci](distribute-data-globally.md) .

### <a name="always-on"></a>Vždy zapnuto

Díky důkladné integraci s infrastrukturou Azure a [transparentní replikací s více hlavními servery](global-dist-under-the-hood.md)poskytuje Cosmos DB [99,999% vysokou dostupnost](high-availability.md) pro čtení i zápis. Cosmos DB taky poskytuje možnost programově (nebo prostřednictvím portálu) vyvolat místní převzetí služeb při selhání vašeho účtu Cosmos. Tato možnost pomáhá zajistit, že vaše aplikace bude v případě regionálních havárií navržena pro převzetí služeb při selhání.

### <a name="elastic-scalability-of-throughput-and-storage-worldwide"></a>Elastická škálovatelnost propustnosti a úložiště po celém světě

Cosmos DB navržená pomocí transparentního horizontálního dělení a replikace s více hlavními servery, nabízí neotevřenou elastickou škálovatelnost pro vaše zápisy a čtení, a to vše po celém světě. Můžete elasticky škálovat až na stovky milionů požadavků za sekundu po celém světě a jediným voláním rozhraní API a platit jenom za propustnost (a úložiště), které potřebujete. Tato možnost vám pomůže se zabývat s neočekávanými špičkami ve vašich úlohách, aniž byste museli předem poskytovat špičku. Další informace najdete v tématech [dělení v Cosmos DB](partitioning-overview.md), [zřízená propustnost na kontejnerech a databázích](set-throughput.md)a [globálně se škálováním zajištěné propustnosti](scaling-throughput.md).

### <a name="guaranteed-low-latency-at-99th-percentile-worldwide"></a>Garantovaná nízká latence na 99. percentilu, po celém světě

Pomocí Cosmos DB můžete vytvářet vysoce reagující aplikace globálním škály. Díky novému serveru pro více hlavních serverů replikace a [databázovému stroji](index-policy.md), který je s podporou pro zápis a optimalizaci pro zápis, Cosmos DB zaručuje méně než 10 MS latence pro čtení (indexované) a zápisy na 99 percentilu, a to vše po celém světě. Tato schopnost umožňuje tolerovat přijímání dat a neuvěřitelně rychlé dotazy pro vysoce reagující aplikace.

### <a name="precisely-defined-multiple-consistency-choices"></a>Přesně definované, vícenásobné volby konzistence

Při sestavování globálně distribuovaných aplikací v Cosmos DB už nemusíte provádět extrémní [kompromisy mezi konzistencí, dostupností, latencí a propustností](consistency-levels-tradeoffs.md). Pro intuitivní programovací model s nízkou latencí a vysokou dostupností pro globálně distribuovanou aplikaci je možné pečlivě vymezit protokol replikace s více hlavními servery, který nabízí [pět jasně definovaných možností konzistence](consistency-levels.md)  -  *,* *ohraničená neaktuálnost*, *relace*, *konzistentní předpona*a konečný –. *eventual* Cosmos DB

### <a name="no-schema-or-index-management"></a>Žádná Správa schématu ani indexu

Udržování schématu databáze a indexů v synchronizaci se schématem aplikace je zvlášť bolestivý pro globálně distribuované aplikace. Pomocí Cosmos DB nemusíte zabývat správou schématu nebo indexu. Databázový stroj je plně schématu – nezávislá.  Vzhledem k tomu, že se nevyžaduje Správa schématu a indexů, nemusíte se také starat o výpadek aplikace při migraci schémat. Cosmos DB [automaticky indexuje všechna data](index-policy.md) a obsluhuje dotazy rychleji.

### <a name="battle-tested-database-service"></a>Testovaná databázová služba pro výročí

Cosmos DB je základní služba v Azure. Skoro pro desetiletí Cosmos DB používala řada produktů Microsoftu pro kritické aplikace v globálním měřítku, včetně Skype, Xbox, Microsoft 365, Azure a mnoha dalších. V současné době je Cosmos DB jednou z nejrychlejších rostoucích služeb v Azure, které používá mnoho externích zákazníků a důležitých aplikací, které vyžadují Elastické škálování, klíč globální distribuci, replikaci více hlavních serverů pro zajištění nízké latence a vysokou dostupnost obou operací čtení i zápisu.

### <a name="ubiquitous-regional-presence"></a>Všudypřítomný regionální přítomnost

Cosmos DB je k dispozici ve všech oblastech Azure po celém světě, včetně 54 + oblastí ve veřejném cloudu, [Azure Čína 21Vianet](https://www.azure.cn/en-us/), Azure německo, Azure Government a Azure Government pro departementy obrany (DOD). Podívejte se [na oblast Cosmos DB](regional-presence.md).

### <a name="secure-by-default-and-enterprise-ready"></a>Zabezpečení ve výchozím nastavení a připraveno pro podniky

Cosmos DB je certifikovaný pro [nejrůznější standardy dodržování předpisů](compliance.md). Veškerá data se navíc šifrují v klidovém stavu i v pohybu. Poskytuje autorizaci na úrovni řádků a dodržuje přísné standardy zabezpečení.

### <a name="significant-tco-savings"></a>Významné úspory celkových nákladů na vlastnictví

Vzhledem k tomu, že Cosmos DB je plně spravovaná služba, nebudete už muset spravovat a provozovat složitá nasazení s více datacentermi a upgrady vašeho databázového softwaru, platit za podporu, licencování a operace nebo musí zřídit databázi pro zatížení ve špičce. Další informace najdete v tématu [optimalizace nákladů pomocí Cosmos DB](total-cost-ownership.md).

### <a name="industry-leading-comprehensive-slas"></a>Špičkové komplexní SLA

Cosmos DB je první a jedinou službou, která nabízí [špičkové komplexní SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) , včetně 99,999% vysoké dostupnosti, latence čtení a zápisu na 99 percentilu, garantované propustnosti a konzistenci.

### <a name="globally-distributed-operational-analytics-and-ai-with-natively-built-in-apache-spark"></a>Globálně distribuované provozní analýzy a AI s nativně integrovanými Apache Spark

[Spark](spark-connector.md) můžete spustit přímo na datech uložených v Cosmos DB. Tato funkce vám umožní provádět nízkou latenci a provozní analýzu v globálním měřítku, aniž by to ovlivnilo transakční úlohy přímo proti Cosmos DB. Další informace najdete v tématu [globálně distribuovaná provozní analýza](lambda-architecture.md).

### <a name="develop-applications-on-cosmos-db-using-popular-open-source-software-oss-apis"></a>Vývoj aplikací v Cosmos DB pomocí oblíbených rozhraní API OSS (Open source software)

Cosmos DB nabízí možnost výběru rozhraní API pro práci s daty uloženými v databázi Cosmos. Ve výchozím nastavení [můžete](how-to-sql-query.md) pro dotazování databáze Cosmos použít rozhraní SQL (základní rozhraní API). Cosmos DB také implementuje rozhraní API pro [Cassandra](cassandra-introduction.md), [MongoDB](mongodb-introduction.md), [Gremlin](graph-introduction.md) a [Azure Table Storage](table-introduction.md). Klientské ovladače (a nástroje) pro běžně používané NoSQL (např. MongoDB, Cassandra, Gremlin) můžete nasměrovat přímo do vaší databáze Cosmos. Díky podpoře přenosových protokolů běžně používaných rozhraní NoSQL API vám Cosmos DB umožňuje:

* Snadná migrace aplikace do Cosmos DB a zachování významných částí logiky aplikace.
* Zajistěte, aby vaše aplikace byla přenosná a nadále zůstala dodavatel v cloudu – nezávislá.
* Získejte plně spravovanou cloudovou službu se špičkovými finančně zálohovanými Slami pro společná rozhraní NoSQL API. 
* Elastickě Škálujte zřízenou propustnost a úložiště pro vaše databáze podle svých potřeb a Plaťte jenom za propustnost a úložiště, které potřebujete. To vede ke značným úsporám nákladů.

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Řešení, kterým služba Azure Cosmos DB přináší výhody

Všechny [aplikace pro web, mobilní zařízení, hry a IoT](use-cases.md) , které potřebují zpracovávat obrovské objemy dat, čtení a zápisy v [globálním měřítku](distribute-data-globally.md) s téměř skutečnými dobami odezvy pro nejrůznější data, budou mít výhodu Cosmos DB [garantované vysoké dostupnosti](https://azure.microsoft.com/support/legal/sla/cosmos-db/), vysoké propustnosti, nízké latence a přizpůsobitelné konzistenci. Přečtěte si, jak můžete Azure Cosmos DB použít k sestavování [IoT a telematických](use-cases.md#iot-and-telematics), [maloobchodních a marketingových](use-cases.md#retail-and-marketing), [herních](use-cases.md#gaming) a [webových a mobilních aplikací](use-cases.md#web-and-mobile-applications).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o základních konceptech Cosmos DB [klíč globální distribuci](distribute-data-globally.md) a vytváření [oddílů](partitioning-overview.md) a [zřízené propustnost](request-units.md).

Začínáme se službou Azure Cosmos DB s využitím jedné ze čtyř šablon Rychlý start:

* [Začínáme s rozhraním SQL API služby Azure Cosmos DB](create-sql-api-dotnet.md)
* [Začínáme s rozhraním API Azure Cosmos DB pro MongoDB](create-mongodb-nodejs.md)
* [Začínáme s rozhraním Cassandra API služby Azure Cosmos DB](create-cassandra-dotnet.md)
* [Začínáme s rozhraním Gremlin API služby Azure Cosmos DB](create-graph-dotnet.md)
* [Začínáme s rozhraním Table API služby Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Vyzkoušejte si Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/)
