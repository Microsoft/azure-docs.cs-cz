---
title: Nejčastější dotazy k různým rozhraním API v Azure Cosmos DB
description: Získejte odpovědi na nejčastější dotazy ohledně služby Azure Cosmos DB, globálně distribuovaná a vícemodelová databázová služba. Další informace o kapacitě a úrovně výkonu a škálování.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 322d1c9ead1e7591c359c35c445fa32529db22ef
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78387141"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Nejčastější dotazy k různým rozhraním API ve službě Azure Cosmos DB

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Co jsou typické případy použití pro službu Azure Cosmos DB?

Azure Cosmos DB je dobrou volbou pro nové webové, mobilní a herní zařízení, a je důležité, aplikace IoT, kde automatické škálování, předvídatelný výkon, rychlé pořadí doby odezvy milisekund a schopnost dotazovat data bez schémat. Azure Cosmos DB slouží k rychlému vývoji a podpoře nepřetržitých iterací modelů dat aplikace. Aplikace, které spravují uživatelem generovaný obsah a data, jsou [běžné případy použití pro Azure Cosmos DB](use-cases.md).

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Jak služby Azure Cosmos DB nabízí předvídatelný výkon?

[Jednotka žádosti](request-units.md) (ru) je mírou propustnosti v Azure Cosmos DB. Propustnost 1RU odpovídá propustnosti získání dokumentu o 1 KB. Všechny operace ve službě Azure Cosmos DB, včetně čtení, zápisů, dotazů SQL a spouštění uložených procedur, je přiřazená deterministická RU hodnota, která je založena na požadované propustnosti pro dokončení operace. Místo přemýšlení o procesoru, vstupu/výstupu a paměti a jak každý ovlivňují propustnost aplikace, si můžete představit jako jedinou měrnou RU.

U každého kontejneru Azure Cosmos můžete nakonfigurovat zřízenou propustnost v podobě ru propustnosti za sekundu. Pro aplikace jakéhokoli rozsahu můžete srovnávací testy k měření své RU hodnoty jednotlivých požadavků a zřídit kontejner pro zpracování celkový součet jednotek žádosti napříč všemi požadavky. Můžete také vertikálně navýšit kapacitu nebo snížit kapacitu propustnosti vašeho kontejneru jako potřebám vaší aplikace. Pokud chcete získat další informace o jednotkách žádostí a nápovědu k určení potřeb kontejneru, zkuste použít [kalkulačku propustnosti](https://www.documentdb.com/capacityplanner).

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Jak služby Azure Cosmos DB podporuje různé datové modely, jako je například klíč/hodnota, úložiště se sloupcovou strukturou, dokument a graf?

Klíč/hodnota (tabulka), úložiště se sloupcovou strukturou, dokument a data grafu modely jsou podporována všechna nativně z důvodu ARS (atomů, záznamů a pořadí) návrh postavena služby Azure Cosmos DB. Atomů, záznamů a pořadí lze snadno mapovat a promítá do různých datových modelů. Rozhraní API pro podmnožinu modelů jsou nyní k dispozici (SQL, MongoDB, Table a Gremlin) a další specifické pro další datové modely budou v budoucnu k dispozici.

Azure Cosmos DB má schéma nezávislá indexovací modul dokáže automaticky indexovat všechna data, která se ingestují bez vyžadování jakéhokoli schématu nebo sekundárních indexů od vývojáře. Modul spoléhá na sadu logických index rozložení (obrácenou, úložiště se sloupcovou strukturou, stromu), což oddělit rozložení úložiště z indexu a subsystémy zpracování dotazů. Cosmos DB má také možnost pro podporu sady přenosu protokolů a rozhraní API postupně přibývají a efektivně překládat základní datový model (1) a logické index rozložení (2) tak jednoznačně schopný zajistit podporu více než jeden datový model nativně.

### <a name="can-i-use-multiple-apis-to-access-my-data"></a>Můžu pro přístup k datům použít více rozhraní API?

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Kde multi-model znamená Azure Cosmos DB podporuje více rozhraní API a více datových modelů, používají různá rozhraní API pro úložiště a síťový protokol různé formáty dat. Například SQL používá JSON, MongoDB používá BSON, tabulka používá EDM, Cassandra používá CQL, Gremlin používá GraphSON. V důsledku toho doporučujeme použít stejné rozhraní API pro veškerý přístup k datům v daném účtu.

Každé rozhraní API funguje nezávisle, s výjimkou Gremlin a SQL API, které jsou interoperabilní.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Je kompatibilní s Azure Cosmos DB HIPAA?

Ano, Azure Cosmos DB je HIPAA nedodržující předpisy. HIPAA zavádí požadavky na použití, zveřejnění a ochranu jednotlivě identifikovatelných zdravotních informací. Další informace najdete na webu [Centrum zabezpečení Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Jaká jsou omezení úložiště služby Azure Cosmos DB?

Neexistuje žádné omezení celkové množství dat, která může ukládat kontejneru ve službě Azure Cosmos DB.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Jaká jsou omezení propustnosti služby Azure Cosmos DB?

Neexistuje žádné omezení celkové množství propustnost, které kontejner může podporovat ve službě Azure Cosmos DB. Klíč spočívá v distribuovat vaši úlohu přibližně rovnoměrně mezi dostatečně velký počet klíčů oddílů.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>Se šifrují přímo a brány režimy připojení?

Ano oba režimy jsou vždy plně zašifrované.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Kolik stojí služby Azure Cosmos DB?

Podrobnosti najdete na stránce s [podrobnostmi o cenách Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) . Poplatky za využívání služby Azure Cosmos DB se určují podle počtu zřízených kontejnery, počet hodin, které byly v režimu online, kontejnery a zřízené propustnosti pro každý kontejner.

### <a name="is-a-free-account-available"></a>Je k dispozici bezplatný účet?

Ano, můžete registraci časově omezený účet zdarma, bez jakýchkoli závazků. Pokud se chcete zaregistrovat, navštivte web vyzkoušet [Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) nebo si přečtěte další informace v [nejčastějších dotazech k Azure Cosmos DB](#try-cosmos-db).

Pokud s Azure začínáte, můžete si zaregistrovat [bezplatný účet Azure](https://azure.microsoft.com/free/), který vám poskytne 30 dnů a kredit na to, abyste si vyzkoušeli všechny služby Azure. Máte-li předplatné sady Visual Studio, máte také nárok na používání [bezplatného kreditu Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) na jakékoli službě Azure.

[Emulátor Azure Cosmos DB](local-emulator.md) můžete také použít k místnímu vývoji a testování vaší aplikace bez nutnosti vytvářet předplatné Azure. Jakmile budete spokojeni s fungováním aplikace v emulátoru, můžete přejít na účet služby Azure Cosmos DB v cloudu.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Jak mohu získat další nápovědu pomocí služby Azure Cosmos DB?

Položit technický dotaz, příspěvek na jednu z těchto dvou otázku a odpověď fóra:

* [Fórum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Přetečení zásobníku je nejvhodnější pro otázek týkajících se programování. Ujistěte se, že je váš dotaz [v rámci vašeho tématu](https://stackoverflow.com/help/on-topic) [, a uveďte co nejvíce podrobností a vymažte otázku a odpovězte na](https://stackoverflow.com/help/how-to-ask)ně.

Pokud chcete požádat o nové funkce, vytvořte nový požadavek na [uživatelský hlas](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Pokud řešíte problém s účtem, podejte prostřednictvím webu Azure Portal [žádost o podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a id="try-cosmos-db"></a>Vyzkoušet Azure Cosmos DB předplatná

Je můžete nyní Užijte si časově omezené služby Azure Cosmos DB bez předplatného, zdarma a závazky. Pokud se chcete zaregistrovat k předplatnému vyzkoušet Azure Cosmos DB, pokračujte [Azure Cosmos DB vyzkoušet si zdarma](https://azure.microsoft.com/try/cosmosdb/) a použijte libovolné osobní účet Microsoft (MSA). Toto předplatné je nezávislé na [bezplatné zkušební verzi Azure](https://azure.microsoft.com/free/)a dá se použít společně s bezplatnou zkušební verzí Azure nebo s placeným předplatným Azure.

Zobrazovat zkuste Azure Cosmos DB předplatná na webu Azure Portal vedle jiných předplatných spojených s vaším ID uživatele.

Vyzkoušejte Azure Cosmos DB odběratelům platí následující podmínky:

* Přístup k účtu se dá udělit pro osobní účty Microsoft (MSA). Nepoužívejte účty nebo účty služby Active Directory (AAD) patřící k podnikovým klientům AAD, ale můžou mít omezení, která by mohla blokovat udělení přístupu.
* Jeden [kontejner zřízený propustností](./set-throughput.md#set-throughput-on-a-container) na předplatné pro SQL, Gremlin API a účty tabulek.
* Až tři [zajištěné propustnosti](./set-throughput.md#set-throughput-on-a-container) v rámci předplatného pro účty MongoDB.
* Jedna [zajištěná propustnost databáze](./set-throughput.md#set-throughput-on-a-database) na jedno předplatné. Databáze zřízené v propustnosti můžou obsahovat libovolný počet kontejnerů.
* Kapacita 10 GB úložiště.
* Globální replikace je dostupná v následujících [oblastech Azure](https://azure.microsoft.com/regions/): Střed USA, Severní Evropa a jihovýchodní Asie.
* Maximální propustnost 5 000 RU/s v případě zřízení na úrovni kontejneru.
* Maximální propustnost 20 000 RU/s, pokud je zajištěna na úrovni databáze.
* Platnost předplatných vyprší po 30 dnech a dají se prodloužit na maximum 31 dní.
* Pro účty vyzkoušejte Azure Cosmos DB; nelze vytvořit lístky podpory Azure Podpora je však poskytuje předplatitelům s stávajících plánech podpory.

## <a name="set-up-azure-cosmos-db"></a>Nastavení služby Azure Cosmos DB

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Jak se mohu zaregistrovat pro službu Azure Cosmos DB?

Azure Cosmos DB je k dispozici na webu Azure Portal. Nejprve si zaregistrujte předplatné Azure. Po registraci můžete do svého předplatného Azure přidat účet Azure Cosmos DB.

### <a name="what-is-a-master-key"></a>Co je hlavní klíč?

Hlavní klíč je token zabezpečení pro přístup ke všem prostředkům účtu. Uživatelé, kteří mají klíč, čtení a zápisu přístup ke všem prostředkům v databázovém účtu. Při distribuci hlavního klíče buďte opatrní. Primární hlavní klíč a sekundární hlavní klíč jsou k dispozici v okně **klíče** [Azure Portal][azure-portal]. Další informace o klíčích najdete v tématu [Zobrazení, kopírování a obnovení přístupových klíčů](manage-with-cli.md#list-account-keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Co jsou oblasti, které je možné nastavit PreferredLocations?

Hodnota PreferredLocations můžete nastavit na některou z oblasti Azure, ve kterých je k dispozici služby Cosmos DB. Seznam oblastí, které jsou k dispozici, najdete v tématu [oblasti Azure](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Je všechno, co bych měl(a) vědět při distribuci dat po celém světě prostřednictvím datových centrech Azure?

Azure Cosmos DB se nachází ve všech oblastech Azure, jak je uvedeno na stránce [oblasti Azure](https://azure.microsoft.com/regions/) . Protože je základní služby, každé nové datové centrum má přítomnost Azure Cosmos DB.

Když nastavíte oblast, mějte na paměti, že služby Azure Cosmos DB respektuje suverénních a státních cloudů. To znamená, že pokud v [oblasti svrchovaného](https://azure.microsoft.com/global-infrastructure/)účtu vytvoříte účet, nebudete moci replikovat z této [oblasti svrchovan](https://azure.microsoft.com/global-infrastructure/). Podobně nelze povolit replikaci do jiných suverénních umístění z vnějšího účtu.

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>Je možné přejít z úrovně propustnosti kontejneru zřizování zřizování úrovně propustnosti databáze? Nebo naopak

Kontejner a zřizování úrovně propustnosti databáze jsou samostatné nabídky a přepínání mezi některý z těchto vyžadují migraci dat ze zdroje do cíle. To znamená, že je potřeba vytvořit novou databázi nebo nový kontejner a pak migrovat data pomocí [knihovny hromadného prováděcího modulu](bulk-executor-overview.md) nebo [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>Služby Azure cosmos DB podporuje řadu analýza?

Ano, Azure CosmosDB podporuje analýzu časových řad. tady je ukázka pro [vzor časových řad](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns). Tento příklad ukazuje způsob použití kanálu pro agregovaná zobrazení sestavení v datech časové řady změn. Tento přístup můžete rozšířit pomocí spark streaming nebo jiném editoru datového proudu.

## <a name="what-are-the-azure-cosmos-db-service-quotas-and-throughput-limits"></a>Jaké jsou kvóty Azure Cosmos DB služeb a omezení propustnosti

Další informace najdete v článku [kvóty Azure Cosmos DB služeb](concepts-limits.md) a [v rámci limitu pro jednotlivé kontejnery a databáze](set-throughput.md#comparison-of-models) .

## <a name="sql-api"></a>SQL API

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Jak můžu začít vyvíjet s využitím rozhraní SQL API?

Nejprve musí přihlásit k odběru služeb Azure. Po registraci předplatného Azure můžete ke svému předplatnému Azure přidat kontejner rozhraní SQL API. Pokyny k přidání Azure Cosmos DB účtu najdete v tématu [Vytvoření účtu databáze Azure Cosmos](create-sql-api-dotnet.md#create-account).

Pro .NET, Python, Node.js, JavaScript a Javu jsou k dispozici sady [SDK](sql-api-sdk-dotnet.md). Vývojáři můžou také používat [rozhraní RESTFUL HTTP API](/rest/api/cosmos-db/) k interakci s Azure Cosmos DBmi prostředky z různých platforem a jazyků.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Můžete přistupovat k některé předem připravené ukázky, které se připravit?

Ukázky pro sady SDK rozhraní SQL API [.NET](sql-api-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node. js](sql-api-nodejs-samples.md)a [Python](sql-api-python-samples.md) jsou k dispozici na GitHubu.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>Podporuje databáze SQL API služby data bez schémat?

Ano, rozhraní SQL API umožňuje aplikacím ukládat libovolné dokumenty JSON bez definic schématu nebo parametrů. Data se okamžitě dotazovat přes rozhraní dotazů SQL služby Azure Cosmos DB k dispozici.

### <a name="does-the-sql-api-support-acid-transactions"></a>Podporuje rozhraní SQL API transakce ACID?

Ano, rozhraní SQL API podporuje transakce mezi dokumenty vyjádřené jako JavaScript uložených procedur a aktivačních událostí. Transakce jsou omezená na jeden oddíl v rámci každého kontejneru a jsou prováděny s odpovídající zásadám ACID sémantiku jako "všechno nebo nic," izolovaně od jiných souběžně spuštěných kódů a požadavků uživatelů. Pokud jsou výjimky vyvolány prostřednictvím provádění kód Javascriptové aplikace na straně serveru, celá transakce se vrátí zpět. 

### <a name="what-is-a-container"></a>Co je kontejner?

Kontejner je skupina dokumentů a jejich přidružené logiky Javascriptové aplikace. Kontejner je fakturovatelná entita, kde [náklady](performance-levels.md) určují propustnost a využité úložiště. Kontejnery můžou pokrývat jeden nebo více oddílů nebo serverů a můžete škálovat tak, aby zvládaly prakticky neomezené objemy úložišť a propustnosti.

* Pro rozhraní SQL API se kontejner mapuje na kontejner.
* Pro Cosmos DB rozhraní API pro účty MongoDB se kontejner mapuje na kolekci.
* Pro účty Cassandra a rozhraní Table API služby kontejneru mapuje na tabulku.
* Pro účty rozhraní Gremlin API kontejner mapuje do grafu.

Kontejnery jsou také entitami fakturace pro službu Azure Cosmos DB. Každý kontejner se účtuje po hodinách podle zřízené propustnosti a využitého prostoru úložiště. Další informace najdete v tématu [Azure Cosmos DB ceny](https://azure.microsoft.com/pricing/details/cosmos-db/).

### <a name="how-do-i-create-a-database"></a>Jak vytvořím databázi?

Databáze můžete vytvářet pomocí [Azure Portal](https://portal.azure.com), jak je popsáno v tématu [Přidání kontejneru](create-sql-api-java.md#add-a-container), jedna ze sady [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)nebo [rozhraní REST API](/rest/api/cosmos-db/).

### <a name="how-do-i-set-up-users-and-permissions"></a>Jak nastavím uživatele a oprávnění?

Uživatele a oprávnění můžete vytvořit pomocí jedné ze sady [sdk Cosmos DB API](sql-api-sdk-dotnet.md) nebo rozhraní [REST API](/rest/api/cosmos-db/).

### <a name="does-the-sql-api-support-sql"></a>Podporuje rozhraní SQL API SQL?

Dotazovací jazyk SQL podporuje účty rozhraní API SQL je vylepšená podmnožina dotazovacích funkcí, která je podporována serverem SQL Server. Dotazovací jazyk SQL služby Azure Cosmos DB nabízí bohaté hierarchické a relační operátory a rozšiřitelnost prostřednictvím založené na jazyce JavaScript, uživatelem definované funkce (UDF). Gramatika JSON umožňuje modelování dokumentů JSON ve formě stromů ve uzly s popiskem, které jsou používány Azure Cosmos DB automatických technikách indexování a dialektu dotazování SQL služby Azure Cosmos DB. Informace o použití gramatiky SQL najdete v článku [dotaz SQL][query] .

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>Podporuje rozhraní SQL API agregační funkce SQL?

Rozhraní SQL API podporuje agregaci s nízkou latencí v jakémkoli měřítku prostřednictvím agregačních funkcí `COUNT`, `MIN`, `MAX`, `AVG`a `SUM` pomocí gramatiky SQL. Další informace najdete v tématu [agregační funkce](sql-query-aggregates.md).

### <a name="how-does-the-sql-api-provide-concurrency"></a>Jak rozhraní SQL API zajišťuje souběžnost?

Rozhraní SQL API podporuje optimistické řízení souběžného (přístupu OCC) prostřednictvím značek entit HTTP, neboli Etagů. Každý prostředek rozhraní SQL API má ETag a značka ETag je nastavena na serveru, pokaždé, když se aktualizuje dokument. Záhlaví ETag a aktuální hodnota jsou součástí všechny zprávy odpovědi. Značek etag lze použít s hlavičku If-Match umožňuje rozhodnout, zda je třeba aktualizovat prostředek serveru. Hodnota If-Match je hodnota ETag, která má být znovu porovnávána vůči. Pokud hodnota ETag odpovídá hodnota ETag serveru, se aktualizuje prostředek. Pokud již není aktuální značku ETag, server zamítne operaci s "HTTP 412 selhání předběžné podmínky" kód odpovědi. Klient pak refetches prostředek, který chcete získat aktuální hodnotou ETag pro prostředek. Kromě toho značek etag lze použít s hlavičku If-None-Match k určení, jestli je potřeba znovu načíst prostředku.

Chcete-li použít optimistickou souběžnost v rozhraní .NET, použijte třídu [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) . Ukázku .NET najdete v tématu [program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) v ukázce DocumentManagement na GitHubu.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Jak provádět transakce v rozhraní SQL API?

Rozhraní SQL API podporuje transakce integrované do jazyka prostřednictvím JavaScriptu uložené procedury a triggery. Všechny databázové operace ve skriptech se spouští v izolaci snímku. Pokud se jedná o kontejner s jedním oddílem, je spuštění vymezeno na kontejner. Pokud je kontejner rozdělený, je spuštění vymezeno na dokumenty se stejnou hodnotou klíče oddílu v rámci kontejneru. Na začátku transakce se pořídí snímek verzí dokumentů (ETagy) a k potvrzení dojde pouze v případě, že skript uspěje. Pokud JavaScript vyvolá chybu, transakce se vrátí zpět. Další informace najdete v tématu [programování JavaScriptu na straně serveru pro Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Jak lze I hromadného vložení dokumenty do služby Cosmos DB?

Vám může hromadného vložení dokumenty do služby Azure Cosmos DB v jednom z následujících způsobů:

* Nástroj hromadný prováděcí modul, jak je popsáno v tématu [použití knihovny hromadného prováděcího modulu .NET](bulk-executor-dot-net.md) a [použití knihovny hromadného vykonavatele Java](bulk-executor-java.md)
* Nástroj pro migraci dat, jak je popsáno v tématu [Nástroj pro migraci databáze pro Azure Cosmos DB](import-data.md).
* Uložené procedury, jak je popsáno v tématu [programování JavaScriptu na straně serveru pro Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="does-the-sql-api-support-resource-link-caching"></a>Podporuje rozhraní SQL API podpory resource link ukládání do mezipaměti?

Ano, protože Azure Cosmos DB je služba RESTful, odkazy na prostředky jsou neměnné a lze uložit do mezipaměti. Klienti rozhraní SQL API mohou zadat hlavičku "If-None-Match" pro čtení na libovolný dokument nebo kontejner, který odpovídá prostředkům, a po změně verze serveru aktualizovat jejich místní kopie.

### <a name="is-a-local-instance-of-sql-api-available"></a>Je k dispozici místní instance systému SQL API?

Ano. [Emulátor Azure Cosmos DB](local-emulator.md) zajišťuje emulaci služby Cosmos DB s vysokou přesností. Podporuje funkci, která se shoduje s Azure Cosmos DB, včetně podpory pro vytvoření a dotazování dokumentů JSON, zřizování a škálování kolekcí a spouštění uložených procedur a aktivačních událostí. Vývoj a testování aplikací s použitím emulátor služby Azure Cosmos DB a jejich nasazení do Azure v globálním měřítku tak, že jediné konfiguraci změnit na koncový bod připojení pro službu Azure Cosmos DB.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>Proč se dlouho s plovoucí desetinnou čárkou hodnoty v dokumentu zaokrouhleno, pokud je zobrazován z Průzkumníku dat na portálu.

Toto je omezení jazyka JavaScript. JavaScript používá čísla formátu s plovoucí desetinnou čárkou s dvojitou přesností, jak je uvedeno v IEEE 754, a může bezpečně držet čísla mezi-(2<sup>53</sup> -1) a 2<sup>53</sup>-1 (tj., 9007199254740991).

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Pokud jsou povolené oprávnění v hierarchii objektů?

Vytváření oprávnění s použitím ResourceTokens je povolené na úrovni kontejneru a jeho následníky (jako jsou dokumenty, přílohy). To znamená, že při pokusu o vytvoření oprávnění v databázi nebo úrovni účtu není aktuálně povoleno.

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>Rozhraní API služby Azure Cosmos DB pro MongoDB

### <a name="what-is-the-azure-cosmos-dbs-api-for-mongodb"></a>Co je rozhraní API Azure Cosmos DB pro MongoDB?

Rozhraní API pro Azure Cosmos DB pro MongoDB je vrstva kompatibility protokolů, která umožňuje aplikacím snadno a transparentně komunikovat s nativním databázovým strojem Azure Cosmos pomocí stávajících sad SDK a ovladačů podporovaných komunitou pro MongoDB. Vývojáři teď můžou využívat stávající MongoDB sady nástrojů a dovednosti k sestavování aplikací, které využívají Azure Cosmos DB. Vývojáři mají výhodu jedinečných funkcí Azure Cosmos DB, které zahrnují globální distribuci s více hlavními replikacemi, automatické indexování, údržbu zálohování, finančně zálohovaných smluv o úrovni služeb (SLA) atd.

### <a name="how-do-i-connect-to-my-database"></a>Návody se připojit k databázi?

Nejrychlejší způsob, jak se připojit k databázi Cosmos s rozhraním API Azure Cosmos DB pro MongoDB, je přejít na [Azure Portal](https://portal.azure.com). Přejděte na svůj účet a potom v levé navigační nabídce klikněte na **rychlé zprovoznění**. Rychlý Start je nejlepší způsob, jak získat fragmenty kódu pro připojení k vaší databázi.

Azure Cosmos DB Vynutí striktní bezpečnostní požadavky a standardy. Účty Azure Cosmos DB vyžadují ověření a zabezpečenou komunikaci přes protokol SSL, proto ji nezapomeňte použít TLSv1.2.

Další informace najdete v tématu [připojení k databázi Cosmos s rozhraním API Azure Cosmos DB pro MongoDB](connect-mongodb-account.md).

### <a name="are-there-additional-error-codes-that-i-need-to-deal-with-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Existují další chybové kódy, které potřebuji řešit při používání rozhraní API Azure Cosmos DB pro MongoDB?

Spolu s běžnými kódy chyb MongoDB má rozhraní API Azure Cosmos DB pro MongoDB vlastní konkrétní kódy chyb:

| Chyba               | Kód  | Popis  | Řešení  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Celkový počet spotřebovaných jednotek žádostí je vyšší než zřízené procento požadavků a Jednotková sazba pro daný kontejner a byla omezena. | Zvažte možnost škálování propustnosti přiřazené ke kontejneru nebo sadu kontejnerů Azure portal nebo opakuje akci. |
| ExceededMemoryLimit | 16501 | Jako víceklientská služba operace přešel přes klienta přidělení paměti. | Snižte rozsah operace prostřednictvím přísnějších kritérií dotazu nebo kontaktujte podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br>Příklad: <em>&nbsp;&nbsp;&nbsp;&nbsp;DB. GetCollection (' Users '). Aggregate ([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {Name: "Andy"}} <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {Age:-1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])</em>) |

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>Je ovladač Simba pro MongoDB podporovaný pro použití s rozhraním API Azure Cosmos DB pro MongoDB?

Ano, můžete použít ovladač ODBC Simba Mongo s rozhraním API Azure Cosmos DB pro MongoDB

## <a id="table"></a>Rozhraní API pro tabulky

### <a name="how-can-i-use-the-table-api-offering"></a>Použití nabídky Table API

Rozhraní API pro tabulky Azure Cosmos DB je k dispozici v [Azure Portal][azure-portal]. Nejprve musí přihlásit k odběru služeb Azure. Poté, co jste se zaregistrovali, můžete ke svému předplatnému Azure přidat účet Azure Cosmos DB Table API a pak přidejte tabulek ke svému účtu.

Podporované jazyky a související rychlé starty najdete v [úvodu k Azure Cosmos DB rozhraní API pro tabulky](table-introduction.md).

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Potřebuji k používání rozhraní API tabulky na novou sadu SDK?

Ne, existující úložiště, které sady SDK by měla i nadále fungovat. Doporučujeme však, že jeden vždy získá nejnovější sady SDK pro nejlepší podporu a v mnoha případech vynikající výkon. Seznam dostupných jazyků najdete v tématu [Úvod do Azure Cosmos DB rozhraní API pro tabulky](table-introduction.md).

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Pokud není rozhraní Table API identické s Azure Table storage chování?

Existují některé rozdíly v chování, které uživatele z Azure Table storage, kteří chtějí vytvářet tabulky pomocí rozhraní Azure Cosmos DB Table API byste měli vědět:

* Azure Cosmos DB Table API používá model záložní kapacitu k zajištění zaručené výkonu, ale to znamená, že jeden platí za kapacitu poté, co se vytvoří v tabulce, i v případě, že kapacita se nepoužívá. S Azure Table storage, jeden pouze vydělají kapacity, který se používá. To pomáhá vysvětlit, proč rozhraní Table API služby můžou nabízet čtení 10 ms a 15 ms zápisu smlouvu SLA na úrovni 99. percentilu v průběhu Azure Table storage nabízí SLA 10 sekund. Ale i prázdné tabulky bez žádné požadavky, náklady na peníze, aby se zajistilo, že kapacitu je možné zpracovat žádné požadavky na ně na smlouvě SLA s rozhraním Table API služby tabulky, v důsledku toho nabízí služba Azure Cosmos DB.
* Výsledky dotazu vrácené rozhraní API pro tabulky nejsou seřazené v pořadí podle klíče oddílu nebo klíče řádku, protože jsou v úložišti tabulek Azure.
* Řádek klíče mohou být pouze až 255 bajtů
* Dávky můžete mít jenom až 2 MB
* CORS se momentálně nepodporuje.
* Názvy tabulek v Azure Table Storage nerozlišují velká a malá písmena, ale jsou v Azure Cosmos DB rozhraní API pro tabulky
* Některé služby Azure Cosmos DB interní formáty kódování informace, jako je například binární pole nejsou aktuálně tak efektivní jako jeden mohly líbit. Proto to může způsobit neočekávané omezení velikosti dat. Například právě jeden nelze použít úplné jeden Meg entitu tabulky k ukládání binárních dat, protože kódování zvyšuje velikost dat.
* Název vlastnosti entity ' ID ' aktuálně není podporován.
* TableQuery TakeCount se neomezuje na 1000

Z hlediska rozhraní REST API existuje několik možností dotazů koncové body, které nejsou podporovány službou Azure Cosmos DB Table API:

| Metody REST | Možnost koncový bod/dotazu REST | Adresy URL dokumentu | Vysvětlení |
| ------------| ------------- | ---------- | ----------- |
| GET, PUT | /? restype =service@comp= vlastnosti| [Nastavení vlastností služby Table](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) a [získání vlastností služby Table Service](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) | Tento koncový bod se používá k nastavení pravidel CORS, konfiguraci úložiště analýzy a nastavení protokolování. CORS není aktuálně podporováno, protokolování a analýza jsou zpracovány jinak ve službě Azure Cosmos DB než tabulky v úložišti Azure |
| MOŽNOSTI | /\<Table-Resource-Name > | [Požadavek na tabulku CORS před lety](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | Toto je část CORS, které služby Azure Cosmos DB v současné době nepodporuje. |
| GET | /? restype =service@comp= statistiky | [Získat statistiku služby Table](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | Poskytuje informace o tom, jak rychle replikuje data mezi primární a sekundární databáze. To není potřeba ve službě Cosmos DB, protože je součástí zápisy replikace. |
| GET, PUT | /mytable? kompozice = seznamu acl | [Získat seznam ACL tabulky](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) a [nastavit seznam ACL tabulky](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) | Tím získá a nastaví uložené zásady přístupu slouží ke správě sdílených přístupových podpisů (SAS). I když se SAS podporuje, nastavují a spravují jinak. |

Kromě toho rozhraní API tabulky Azure Cosmos DB podporuje pouze formátu JSON, ne ATOM.

I když službu Azure Cosmos DB podporuje sdílených přístupových podpisů (SAS) existují některé zásady, které nepodporuje, konkrétně ty související s operacemi správy, jako je například právo k vytvoření nových tabulek.

Pro sadu .NET SDK konkrétně, existují některé třídy a metody, které služby Azure Cosmos DB v současné době nepodporuje.

| Třída | Nepodporovaná – metoda |
|-------|-------- |
| CloudTableClient | \*ServiceProperties * |
|                  | \*ServiceStats * |
| CloudTable | Měli * |
|            | GetPermissions * |
| TableServiceContext | * (Tato třída je zastaralá.) |
| TableServiceEntity | " " |
| TableServiceExtensions | " " |
| TableServiceQuery | " " |

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Jak mám poskytnout zpětnou vazbu týkající se sady SDK nebo chyby?

Váš názor můžete sdílet v některém z následujících způsobů:

* [Uživatelský hlas](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [Fórum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Přetečení zásobníku je nejvhodnější pro otázek týkajících se programování. Ujistěte se, že je váš dotaz [v rámci vašeho tématu](https://stackoverflow.com/help/on-topic) [, a uveďte co nejvíce podrobností a vymažte otázku a odpovězte na](https://stackoverflow.com/help/how-to-ask)ně.

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Co je připojovací řetězec, který je potřeba použít pro připojení k rozhraní API tabulky?

Připojovací řetězec je:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

Připojovací řetězec můžete získat ze stránky připojovacího řetězce na webu Azure Portal.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Jak přepíšu nastavení konfigurace pro možnosti žádosti v sadě .NET SDK pro rozhraní Table API?

Některá nastavení jsou zpracována na CreateCloudTableClient metody a další pomocí souboru app.config v sekci appSettings v klientské aplikaci. Informace o nastavení konfigurace najdete v tématu [možnosti Azure Cosmos DB](tutorial-develop-table-dotnet.md).

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Existují jakékoli změny pro zákazníky, kteří používají existující služby Azure Table storage SDK?

Žádné. Neexistují žádné změny existujících nebo nových zákazníků, kteří používají existující služby Azure Table storage SDK.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Jak lze zobrazit data tabulky, která je uložená ve službě Azure Cosmos DB pro použití s rozhraním Table API?

Na webu Azure portal můžete procházet data. Můžete také použít kódu rozhraní API tabulky nebo nástroje uvedené v další odpovědi.

### <a name="which-tools-work-with-the-table-api"></a>Jaké nástroje pro práci s rozhraním Table API?

Můžete použít [Průzkumník služby Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

Nástroje s flexibilitou se připojovací řetězec ve formátu určeném dříve může podporovat nové rozhraní Table API. Seznam nástrojů tabulky je k dispozici na stránce [Azure Storage klientských nástrojů](../storage/common/storage-explorers.md) .

### <a name="is-the-concurrency-on-operations-controlled"></a>Je souběžnost na operace řízené?

Ano, optimistického řízení souběžnosti se poskytuje prostřednictvím použití mechanismu značka ETag.

### <a name="is-the-odata-query-model-supported-for-entities"></a>Platí pro entity model dotazu OData?

Ano, podporuje rozhraní API tabulky dotaz OData a dotazů LINQ.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Můžu se připojit k Azure Table Storage a Azure Cosmos DB Table API vedle sebe ve stejné aplikaci?

Ano, můžete připojit vytvořením dvou samostatných instancí CloudTableClient, každý odkazující na svůj vlastní identifikátor URI přes připojovací řetězec.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Jak migrovat existující aplikace Azure Table storage s touto nabídkou.

[AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) a [Nástroj pro migraci dat Azure Cosmos DB](import-data.md) jsou podporované.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Jak funguje rozšíření velikosti úložiště pro tuto službu, pokud například začínáte *n* GB dat a data se v průběhu času zvětší na 1 TB?

Azure Cosmos DB je navržená k poskytování neomezené úložiště prostřednictvím použití horizontální škálování. Službu můžete sledovat a zvýšit efektivní úložiště.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Jak můžu monitorovat nabídky Table API?

K monitorování požadavků a využití úložiště můžete použít podokno **metriky** rozhraní API pro tabulky.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Výpočet propustnosti, které můžu vyžadovat

Odhaduje kapacity můžete použít k výpočtu TableThroughput potřebná pro operace. Další informace najdete v tématu [odhad jednotek žádostí a úložiště dat](https://www.documentdb.com/capacityplanner). Obecně platí můžete zobrazit vaší entity jako dokumenty JSON a zadejte čísla pro operace.

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Můžu použít sadou Table API SDK místně v emulátoru?

V tuto chvíli to není možné.

### <a name="can-my-existing-application-work-with-the-table-api"></a>Můžete mé existující aplikace pracovat s rozhraním Table API?

Ano, je podporováno stejného rozhraní API.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>Je potřeba migrovat svoje existující aplikace Azure Table storage SDK, pokud nechcete používat funkce rozhraní Table API?

Ne, můžete vytvořit a použít stávající prostředky Azure Table storage bez přerušení jakéhokoli druhu. Nicméně pokud nepoužíváte rozhraní Table API, můžete nemohou využívat automatických indexů, možnost Další konzistence nebo globální distribuci.

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Jak mohu přidat replikaci dat do rozhraní Table API napříč více než jedné oblasti Azure?

[Nastavení globální replikace](tutorial-global-distribution-sql-api.md#portal) Azure Cosmos DBového portálu můžete použít k přidání oblastí, které jsou vhodné pro vaši aplikaci. K vývoji globálně distribuované aplikace, měli byste také přidat aplikaci s PreferredLocation informací nastavenou na místní oblasti pro zajištění nízké latence čtení.

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Jak mohu změnit primární oblast pro zápis pro tento účet v rozhraní Table API?

V podokně portálu globální replikace služby Azure Cosmos DB můžete použít k přidání oblasti a převzetí služeb při selhání do požadované oblasti. Pokyny najdete v tématu [vývoj s využitím účtů Azure Cosmos dB ve více oblastech](high-availability.md).

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Jak se při distribuovat data nakonfigurovat své upřednostňované oblastí pro zajištění nízké latence čtení?

Pokud chcete pomoci při čtení z místního umístění, použijte PreferredLocation klíč v souboru app.config. Pro existující aplikace rozhraní Table API vyvolá chybu, pokud je LocationMode nastavit. Tento kód, odeberte, protože rozhraní Table API převezme tyto informace ze souboru app.config. 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Jak byste uvažovat o úrovních konzistence do rozhraní Table API?

Azure Cosmos DB poskytuje dobře odůvodněné kompromisy mezi konzistencí, dostupností a latencí. Azure Cosmos DB nabízí pět úrovní konzistence pro vývojáře rozhraní Table API vám umožní vybrat model správné konzistence na úrovni tabulky a vytvoření jednotlivých požadavků při dotazování na data. Když se klient připojí, můžete zadat úroveň konzistence. Můžete změnit úroveň prostřednictvím consistencyLevel argument CreateCloudTableClient.

Rozhraní Table API poskytuje nízkou latenci čtení s "čtení vlastních zápisů" s konzistenci omezená neaktuálnost jako výchozí. Další informace najdete v tématu [úrovně konzistence](consistency-levels.md).

Ve výchozím nastavení, Azure Table storage nabízí silnou konzistenci v rámci oblasti a konečná konzistence v sekundárním umístění.

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Nabízí rozhraní API tabulky Azure Cosmos DB další úrovně konzistence, než Azure Table storage?

Ano, informace o tom, jak těžit z distribuované povahy Azure Cosmos DB, najdete v tématu [úrovně konzistence](consistency-levels.md). Protože jsou k dispozici záruky pro úrovně konzistence, můžete je s jistotou.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Když je povolené globální distribuci, jak dlouho trvá replikovat data?

Azure Cosmos DB potvrzení data trvale v místní oblasti a odesílá data do jiných oblastí okamžitě v řádu milisekund. Tato replikace je závislé pouze na dobu odezvy (požadavku) datového centra. Další informace o schopnostech globální distribuce Azure Cosmos DB najdete v tématu [Azure Cosmos DB: globálně distribuovaná databázová služba v Azure](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>Je možné změnit úroveň konzistence požadavek na čtení?

Pomocí služby Azure Cosmos DB můžete nastavit úroveň konzistence na úrovni kontejneru (v tabulce). Pomocí sady .NET SDK, můžete změnit úroveň tím, že poskytuje hodnotu pro klíč TableConsistencyLevel v souboru app.config. Možné hodnoty jsou: silná, omezená Neaktuálnost, relace, konzistentní Předpona a konečná. Další informace najdete v tématu [přizpůsobitelné úrovně konzistence dat v Azure Cosmos DB](consistency-levels.md). Klíč spočívá v nelze nastavit požadavek konzistence úrovně více než nastavení pro tabulku. Například nelze nastavit úroveň konzistence v tabulce na úrovni konzistence požadavek na silné a konečná.

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Jak rozhraní Table API zpracovává převzetí služeb při selhání Pokud oblast přestane fungovat?

Rozhraní Table API využívá globálně distribuovanou platformu, služby Azure Cosmos DB. Aby bylo zajištěno, že vaše aplikace může tolerovat výpadky datového centra, povolte alespoň jednu oblast pro účet na portálu Azure Cosmos DB, [vývoj s využitím Azure Cosmos DBch účtů ve více oblastech](high-availability.md). Prioritu oblasti můžete nastavit pomocí portálu, který [se vyvíjí s Azure Cosmos DBmi účty ve více oblastech](high-availability.md).

Můžete přidat jakýkoli počet oblastí pro účet a řídit, kdy se převzít služby při selhání tím, že poskytuje priority převzetí služeb při selhání. Použít databázi, budete muset poskytnout aplikaci existuje příliš. Pokud tak učiníte, nebudou vaši zákazníci setkávat s výpadky. [Nejnovější klientská sada SDK pro .NET](table-sdk-dotnet.md) je automatická odstavování, ale ostatní sady SDK ne. To znamená může zjistit oblasti, která je mimo provoz a automatické převzetí služeb při selhání do nové oblasti.

### <a name="is-the-table-api-enabled-for-backups"></a>Je rozhraní API tabulky povolené pro zálohování?

Ano, rozhraní Table API využívá platformu Azure Cosmos DB pro zálohy. Zálohy jsou vytvářeny automaticky. Další informace najdete v tématu [zálohování a obnovení online pomocí Azure Cosmos DB](online-backup-and-restore.md).

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>Rozhraní Table API všechny atributy indexu entity ve výchozím nastavení?

Ano, všechny atributy entity jsou indexovány ve výchozím nastavení. Další informace najdete v tématu [Azure Cosmos DB: zásady indexování](index-policy.md).

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>Dělá to znamená, které nemusíte vytvářet více než jeden index splňovat dotazy?

Ano, Azure Cosmos DB Table API poskytuje, automatické indexování všechny atributy bez jakékoli definice schématu. Tato automatizace uvolní vývojářům zaměřit na aplikace a nikoli na vytvoření indexu a správu. Další informace najdete v tématu [Azure Cosmos DB: zásady indexování](index-policy.md).

### <a name="can-i-change-the-indexing-policy"></a>Můžete změnit zásady indexování?

Ano, můžete změnit zásady indexování tím, že poskytuje definici indexu. Budete muset správně kódovat a escape nastavení.

V případě sad non-.NET SDK se dá zásada indexování nastavit jenom na portálu na **Průzkumník dat**, přejděte na konkrétní tabulku, kterou chcete změnit, a potom přejděte do části **škálování & nastavení**– > zásadu indexování, proveďte požadovanou změnu a pak ji **uložte**.

Ze sady .NET SDK můžete odeslat v souboru app.config:

```JSON
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        }
      ]
    }
  ],
  "excludedPaths":
[
 {
      "path": "/anotherpath"
 }
]
}
```

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Azure Cosmos DB jako platformu zdá se, že máte spoustu možností, jako je například řazení, agregace, hierarchie a další funkce. Můžete přidávat tyto funkce do rozhraní Table API?

Rozhraní Table API poskytuje stejné funkce dotazů jako Azure Table storage. Azure Cosmos DB podporuje také řazení, agregace, geoprostorové dotazy, hierarchie a širokou škálu integrovaných funkcí. Další informace najdete v tématu [SQL dotazy](how-to-sql-query.md).

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Při změně TableThroughput rozhraní Table API?

Měli byste změnit TableThroughput, pokud platí některá z následujících podmínek:

* Při provádění extrakce, transformace a načítání (ETL) dat, nebo chcete nahrát velké množství dat v krátkém čase.
* Budete potřebovat větší propustnost z kontejneru nebo ze skupiny kontejnerů na back-endu. Například uvidíte, že používá propustnost je větší než zřízenou propustnost a jste omezovaní. Další informace najdete v tématu [nastavení propustnosti pro kontejnery Azure Cosmos](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Můžete vertikálně navýšit kapacitu nebo snížit kapacitu propustnosti rozhraní Table API služby tabulky?

Ano, podokno škálování na portálu služby Azure Cosmos DB můžete škálovat propustnost. Další informace najdete v tématu [nastavení propustnosti](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Je výchozí pro nově zřízené tabulky nastavit TableThroughput?

Ano, pokud nepřepíšete TableThroughput pomocí souboru app.config a nepoužívejte předem vytvořený kontejner ve službě Azure Cosmos DB, služba vytvoří tabulku s propustností 400.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Je k dispozici žádné změnit cenový model pro stávající zákazníky služby Azure Table storage?

Žádné. Není žádná změna ceny pro stávající zákazníky služby Azure Table storage.

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Jak se počítá cena pro rozhraní Table API?

Cena závisí na přidělené TableThroughput.

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Jak mám postupovat při libovolné omezení rychlosti pro tabulky v rámci nabídky Table API?

Pokud je frekvence požadavků je větší než kapacita zřízená propustnost pro základní kontejner nebo sadu kontejnerů, dojde k chybě a sady SDK opakuje volání s použitím zásady opakování.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Proč je potřeba zvolit propustnost kromě PartitionKey a RowKey a využijte nabídky Table API služby Azure Cosmos DB?

Azure Cosmos DB nastaví výchozí propustnost pro váš kontejner, pokud nezadáte, jeden v souboru app.config nebo prostřednictvím portálu.

Azure Cosmos DB poskytuje záruky pro výkon a latenci a s horní mez pro operaci. Záruka je možné, pokud modul můžete vynutit zásady správného řízení pro vašeho tenanta operace. Nastavení TableThroughput zajišťuje získat garantovanou propustnost a latence, protože platforma rezervuje kapacita a zaručuje provozní úspěch.

S použitím specifikace propustnost, můžete Elasticky změnit těžit z sezónnosti vaší aplikace, potřebám propustnosti a nižších nákladů.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>Azure Table storage bylo pro mě, cenově dostupné, protože platí pouze pro ukládání dat a já zřídka dotazu. Nabídka Azure Cosmos DB Table API se zdá být účtování mě, i když mám ještě provést jedné transakce a nic uložené. Můžete je vysvětlit?

Azure Cosmos DB je navržená jako systém globálně distribuovaná, na základě smlouvy SLA se zárukou dostupnosti, latence a propustnosti. Když rezervujete propustnost ve službě Azure Cosmos DB, není zaručeno, na rozdíl od jiných systémů propustnost. Azure Cosmos DB nabízí další možnosti, které jste požadovali zákazníků, jako jsou sekundární indexy a globální distribuci.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Můžu nikdy se úplná kvóty"oznámení (což znamená, že oddíl je úplná) při ingestovat data do služby Azure Table storage. S rozhraním Table API se zobrazí tato zpráva. Je to nabídka omezení me a vynucení změnit existující aplikaci?

Azure Cosmos DB je systém, který poskytuje neomezené škálování garantuje latence, propustnosti, dostupnosti a konzistence na základě smlouvy SLA. K zajištění výkonu garantované úrovně premium, ujistěte se, že velikost dat a indexu jsou škálovatelné a spravovatelné. 10 GB limitu počtu entit nebo položky podle klíče oddílu je zajistit, že můžeme poskytnout vynikající výkon vyhledávání a dotazu. Aby se zajistilo, že se vaše aplikace dobře škáluje, i když Azure Storage, doporučujeme, abyste nevytvořili aktivní oddíl tím, *že budete ukládat* všechny informace v jednom oddílu a dotazovat se na něj.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Proto PartitionKey a RowKey jsou stále vyžadovány s rozhraním Table API?

Ano. Protože plochy rozhraní API tabulky je podobná Azure Table storage SDK, klíč oddílu poskytuje efektivní způsob, jak distribuovat data. Klíč řádku je jedinečný v rámci daného oddílu. Klíč řádku musí být k dispozici a nemůže mít hodnotu null jako standardní sadu SDK. Délka RowKey je 255 bajtů a délka PartitionKey je 1 KB.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Jaké jsou chybové zprávy pro rozhraní Table API?

Azure Table storage a Azure Cosmos DB Table API používat stejné sady SDK, takže většina chyb budou stejné.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Proč můžu získat omezit při pokusu vytvořit mnoho tabulek jeden po druhém do rozhraní Table API?

Azure Cosmos DB je systém, který poskytuje latence, propustnosti, dostupnosti a záruky konzistence na základě smlouvy SLA. Protože je zřízená systému, vyhrazuje prostředky k zajištění těchto požadavků. Rychlý kurz vytvoření tabulek je zjištěna a omezuje. Doporučujeme podívat se na kurz vytváření tabulek a snížit na méně než 5 za minutu. Mějte na paměti, že rozhraní API tabulky je zřízené systému. V okamžiku, zřídíte brzy dál za něj platit.

## <a name="gremlin-api"></a>Rozhraní Gremlin API

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>Pro jazyk C# / .NET development použití Gremlin.NET i balíček Microsoft.Azure.Graphs?

Gremlin API služby Azure Cosmos DB využívá open source ovladače jako hlavní konektory pro službu. Doporučenou možností je použít [ovladače, které podporuje Apache Tinkerpop](https://tinkerpop.apache.org/).

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Jak RU/s se účtují při spouštění dotazů v databázi grafu?

Graf objektů, vrcholy a hrany, se zobrazují jako dokumenty JSON do back-endu. Protože jednoho dotazu Gremlin můžete změnit jeden nebo mnoho grafu objektů v čase, náklady spojené s ním přímo souvisí s objekty, hrany, které se zpracovávají v dotazu. Toto je stejný proces, který používá službu Azure Cosmos DB pro jiná rozhraní API. Další informace najdete v tématu [o jednotkách žádostí v Azure Cosmos DB](request-units.md).

Poplatek za RU vychází z pracovní sady dat procházení a nastavte není výsledek. Například pokud dotaz má za cíl získat jeden vrchol kvůli tomu ale potřeba procházejí více než jeden další objekt na cestě, potom náklady bude založena na všechny objekty grafu, které potřebuje pro výpočet vrcholů jeden výsledek.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Jaký je maximální rozsah, který může mít databázi grafu v rozhraní Gremlin API služby Azure Cosmos DB?

Azure Cosmos DB využívá [horizontální dělení](partition-data.md) k automatickému zvýšení počtu požadavků na úložiště a propustnost. Maximální propustnost a kapacita úložiště úloh se určují podle počtu oddílů, které jsou přidružené k danému kontejneru. Kontejner rozhraní API Gremlin ale obsahuje specifickou sadu pokynů, které zajistí správné prostředí výkonu ve velkém měřítku. Další informace o dělení a osvědčených postupech najdete [v tématu dělení v Azure Cosmos DB](partition-data.md) článku.

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Jak můžete chránit před útoky prostřednictvím injektáže pomocí Gremlin ovladače?

Většina nativních ovladačů Apache Tinkerpop Gremlin umožňuje, aby možnost poskytovala slovník parametrů pro provádění dotazů. Toto je příklad, jak to provést v [Gremlin.NET](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) a v [Gremlin-JavaScript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js).

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Proč se zobrazuje "chybě kompilace dotazu Gremlin: nepovedlo se najít žádné metody" Chyba?

Gremlin API služby Azure Cosmos DB implementuje podmnožinu funkce je definována v útoku na Gremlin. Podporované kroky a další informace najdete v článku [Gremlin support](gremlin-support.md) .

Nejlepším alternativním řešením je přepsání požadovaných kroků konzoly Gremlin s podporované funkce, protože všechny základních kroků konzoly Gremlin jsou podporovány službou Azure Cosmos DB.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Proč se zobrazuje "WebSocketException: server vrátil stavový kód"200", když byl očekáván stavový kód"101"" Chyba?

Tato chyba je pravděpodobně vyvolána při nesprávné koncový bod se používá. Koncový bod, který generuje tato chyba má následujícímu vzoru:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

Toto je koncový bod dokumenty pro vaši databázi grafu.  Použít na správný koncový bod je koncový bod Gremlin, který má následující formát:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Proč se zobrazuje chyba "RequestRateIsTooLarge"?

Tato chyba znamená, že alokovaných jednotek žádosti za sekundu nestačí k tomu slouží dotazu. K této chybě obvykle dochází, když spustíte dotaz, který získá všechny vrcholy:

```
// Query example:
g.V()
```

Tento dotaz se pokusí o načtení všech vrcholů v grafu. Proto náklady tohoto dotazu bude rovna hodnotě minimální počet vrcholů z hlediska RU. K vyřešení tohoto dotazu je třeba upravit nastavení RU/s.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Proč moje připojení ovladače Gremlin získáte nakonec?

Připojení konzoly Gremlin se provádí prostřednictvím připojení soketu WebSocket. I když nemáte k dispozici připojení pomocí protokolu WebSocket určitý čas TTL, Gremlin API služby Azure Cosmos DB bude ukončen nečinných připojení po 30 minutách nečinnosti.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Proč nelze použít fluent volání rozhraní API v nativním ovladače Gremlin?

Fluent volání rozhraní API ještě nepodporuje Gremlin API služby Azure Cosmos DB. Fluent volání rozhraní API vyžaduje vnitřní funkce formátování, která označuje jako bajtový kód podpory, která se aktuálně nepodporuje rozhraní Gremlin API služby Azure Cosmos DB. Z důvodu ze stejného důvodu nejnovější ovladače Gremlin-JavaScript také aktuálně nepodporuje.

### <a name="how-can-i-evaluate-the-efficiency-of-my-gremlin-queries"></a>Jak můžu posoudit efektivitu Moje dotazy Gremlin?

Krok **executionProfile ()** Preview lze použít k poskytnutí analýzy plánu spouštění dotazů. Tento krok je potřeba přidat na konec objektu jakéhokoli dotazu Gremlin, jak je znázorněno v následujícím příkladu:

**Příklad dotazu**

```
g.V('mary').out('knows').executionProfile()
```

**Příklad výstupu**

```json
[
  {
    "gremlin": "g.V('mary').out('knows').executionProfile()",
    "totalTime": 8,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 3,
        "annotations": {
          "percentTime": 37.5
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 62.5
        },
        "counts": {
          "resultCount": 0
        },
        "storeOps": [
          {
            "count": 0,
            "size": 0,
            "time": 0.6
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      }
    ]
  }
]
```

Výstup výše uvedeného profilu ukazuje, kolik času stráví získáním objektů vrcholu, objektů Edge a velikosti pracovní sady dat. To se týká měření pevné ceny za dotazy na službu Azure Cosmos DB.

## <a id="cassandra"></a>rozhraní API Cassandra

### <a name="what-is-the-protocol-version-supported-by-azure-cosmso-db-cassandra-api-is-there-a-plan-to-support-other-protocols"></a>Jakou verzi protokolu podporuje služba Azure Cosmso DB rozhraní API Cassandra? Existuje plán pro podporu dalších protokolů?

Rozhraní Apache Cassandra API pro službu Azure Cosmos DB podporuje dnes CQL verze 4. Pokud máte zpětnou vazbu k podpoře dalších protokolů, dejte nám vědět prostřednictvím [zpětné vazby uživatele](https://feedback.azure.com/forums/263030-azure-cosmos-db) nebo odesláním e-mailu [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Proč je výběr požadavek propustnost pro tabulku?

Azure Cosmos DB nastaví výchozí propustnost pro vaše kontejnery založené na kde vytvořit tabulku ze – portál nebo CQL.
Azure Cosmos DB poskytuje záruky pro výkon a latenci a s horní mez pro operaci. Záruka je možné, pokud modul můžete vynutit zásady správného řízení pro vašeho tenanta operace. Nastavení propustnosti zajišťuje získat garantovanou propustnost a latence, protože platforma rezervuje kapacita a zaručuje operace byla úspěšná.
Můžete Elasticky změnit propustnost mít prospěch z sezónnosti vaší aplikace a nižších nákladů.

Koncept propustnosti je vysvětlen v tématu [jednotky žádostí v Azure Cosmos DB](request-units.md) . Propustnost pro tabulku je rovnoměrně mezi základní fyzické oddíly.

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>Co je výchozí RU/s při vytvoření přes CQL tabulky? Co když budu potřebovat změnit?

Azure Cosmos DB používá k zajištění propustnosti jednotek žádostí za sekundu (RU/s) jako měnu. Tabulky vytvořené přes CQL mít 400 RU. Z portálu můžete změnit RU.

CQL

```
CREATE TABLE keyspaceName.tablename (user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=1200
```

.NET

```csharp
int provisionedThroughput = 400;
var simpleStatement = new SimpleStatement($"CREATE TABLE {keyspaceName}.{tableName} (user_id int PRIMARY KEY, lastname text)");
var outgoingPayload = new Dictionary<string, byte[]>();
outgoingPayload["cosmosdb_provisioned_throughput"] = Encoding.UTF8.GetBytes(provisionedThroughput.ToString());
simpleStatement.SetOutgoingPayload(outgoingPayload);
```

### <a name="what-happens-when-throughput-is-used-up"></a>Co se stane, když je vyčerpání propustnost?

Azure Cosmos DB poskytuje záruky pro výkon a latenci a s horní mez pro operaci. Záruka je možné, pokud modul můžete vynutit zásady správného řízení pro vašeho tenanta operace. To je možné, podle nastavení propustnosti, které zajišťuje získat garantovanou propustnost a latence, protože platforma rezervuje kapacita a zaručuje operace byla úspěšná.
Pokud překročíte tuto kapacitu, získáte přetížené chybová zpráva oznamující, použita kapacitu až.
přetížené 0x1001: požadavek nelze zpracovat, protože "Rychlost požadavků je velká". V tomto okamžiku je nutné zobrazit jaké operace a jejich objemu způsobí, že tento problém. Můžete získat představu o využité kapacitě probíhá přes zřízené kapacity pomocí metrik na portálu. Pak je potřeba zajistit, je kapacita spotřebovaná téměř rovnoměrně mezi všechny oddíly. Pokud se většina propustnost je využívána jeden oddíl, máte nerovnoměrné rozdělení zatížení.

Metriky jsou k dispozici, která ukazují, jak propustnost se používá během hodin, dnů a za sedm dní, do oddílů nebo v agregaci. Další informace najdete v tématu [monitorování a ladění pomocí metrik v Azure Cosmos DB](use-metrics.md).

Diagnostické protokoly jsou vysvětleny v článku [Azure Cosmos DB diagnostické protokolování](logging.md) .

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>Podporuje primární klíče mapě oddílu klíčovým konceptem ve službě Azure Cosmos DB?

Ano, klíč oddílu se používá k umístění entity do správného umístění. Ve službě Azure Cosmos DB se používá k vyhledání přímo logický oddíl, který je uložený na fyzický oddíl. Koncept dělení se dobře vysvětluje v [oddílu a měřítku v Azure Cosmos DB](partition-data.md) článku. Proveďte nezbytné okamžitě zde je, že logického oddílu by neměl přes 10 GB limitu ještě dnes.

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>Co se stane, když získat úplné kvóty"oznámení o tom, že oddíl je úplné?

Azure Cosmos DB je systém na základě smlouvy SLA, která nabízí neomezené škálování garantuje latence, propustnosti, dostupnosti a konzistence. Tato neomezené úložiště se odvíjí horizontálního škálování z dat s využitím dělení jako klíčovým konceptem. Koncept dělení se dobře vysvětluje v [oddílu a měřítku v Azure Cosmos DB](partition-data.md) článku.

10 GB limitu počtu entit nebo položky na logický oddíl, který by měl splňovat. Aby se zajistilo, že se vaše aplikace dobře škáluje, doporučujeme, abyste nevytvořili aktivní oddíl tím, *že budete ukládat* všechny informace v jednom oddílu a dotazovat se na něj. Tato chyba se může nacházet jenom v případě, že jsou vaše data nakloněná: to znamená, že máte spoustu dat pro jeden klíč oddílu (víc než 10&nbsp;GB). Můžete najít distribuce dat pomocí portálu pro úložiště. Chcete znovu vytvořit v tabulce a zvolte detailní primární (klíč oddílu), která umožňuje lepší distribuci dat je způsob, jak tuto chybu opravit.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>Je možné použít rozhraní Cassandra API jako hodnotu klíče úložiště s milióny nebo miliardy klíče jednotlivých oddílů?

Azure Cosmos DB neomezené data můžete ukládat horizontálním navýšením kapacity úložiště. Toto je nezávislé na propustnost. Ano vždy pouze vám pomůže rozhraní Cassandra API ukládají a načítají zadáním klíče správné primární/oddílu klíč/hodnota. Tyto jednotlivé klíče získat vlastní logický oddíl a nacházejí imitovaná fyzický oddíl bez problémů.

### <a name="is-it-possible-to-create-more-than-one-table-with-apache-cassandra-api-of-azure-cosmos-db"></a>Je možné vytvořit více než jednu tabulku s Apache Cassandra API z Azure Cosmos DB?

Ano, je možné vytvořit více než jedné tabulky pomocí rozhraní Apache Cassandra API. Každá z těchto tabulek je považován za jednotky propustnosti a úložiště.

### <a name="is-it-possible-to-create-more-than-one-table-in-succession"></a>Je možné vytvořit více než jedné tabulky v daný okamžik?

Azure Cosmos DB je netrvá systému pro aktivity roviny dat a ovládací prvek. Kontejnery stejně jako kolekce tabulky jsou runtime entity, které jsou zřízené pro danou kapacitu propustnosti. Vytvoření těchto kontejnerů rychle po sobě není očekávaný aktivity a omezuje. Pokud máte testy, které přetažení a vytváření tabulek okamžitě, zkuste místo navýšení kapacity.

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>Jaký je maximální počet tabulek, které lze vytvořit?

Neexistuje žádný fyzický limit počtu tabulek, odeslání e-mailu na [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) , pokud máte velký počet tabulek (kde celková stálá velikost překročí 10 TB dat), která musí být vytvořená z normálního desítkách nebo 100s.

### <a name="what-is-the-maximum--of-keyspace-that-we-can-create"></a>Jaký je maximální počet prostor klíčů, které můžeme vytvořit?

V případě, že se jedná o kontejnery metadat, neexistuje fyzický limit počtu klíčů a v případě, že z nějakého důvodu máte větší počet míst, pošlete e-mail na [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>Je možné pro velké množství dat po spuštění z normální tabulky?

Kapacita úložiště se automaticky spravuje a vložíte změny do víc dat se zvyšuje. Proto můžete bez obav importovat co nejvíce dat podle potřeby bez Správa a zřizování uzly a další.

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>Je možné zadat nastavení soubor yaml pro konfiguraci chování Apache Casssandra rozhraní API z Azure Cosmos DB?

Apache Cassandra API z Azure Cosmos DB je služba platformy. Poskytuje kompatibilitu na úrovni protokolu pro provádění operací. Skryje okamžitě složitosti správy, monitorování a konfigurace. Jako vývojář nebo uživatel nemusíte starat o dostupnosti, značek odstraněných položek, klíčů mezipaměti, řádek mezipaměti, filtr Standard a velké množství dalších nastavení. Azure Cosmos DB rozhraní Apache Cassandra API se zaměřuje na zajištění čtení a zápis výkonu se vyžadovat, bez režie konfigurace a správa.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>Rozhraní Apache Cassandra API pro službu Azure Cosmos DB bude podporovat uzel Přidání/clusteru stav za uzel stav příkazy?

Rozhraní Apache Cassandra API je služba platformy, která umožňuje plánování kapacity, reaguje na požadavky elasticitu pro propustnost a úložiště podrobným. Pomocí služby Azure Cosmos DB můžete zřídit propustnost, budete potřebovat. Potom je možné škálovat ho nahoru a dolů libovolný počet prostřednictvím dne bez starostí o přidání nebo odstranění uzlů a jejich správu. Z toho vyplývá, že není nutné používat příliš uzlu, nástroj pro správu clusteru.

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>Co se stane s ohledem na různá nastavení konfigurace pro vytvoření prostoru klíčů jako jednoduchý/network?

Azure Cosmos DB poskytuje globální distribuce mimo pole z důvodů nízkou latenci a dostupnosti. Není nutné nastavení repliky nebo další věci. Všechna zápisy jsou vždy trvale kvorum potvrzené v jakékoli oblasti, do které zapisujete a zároveň zadáte záruky výkonu.

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gc_grace-compression-memtable_flush_period-and-more"></a>Co se stane s ohledem na různá nastavení pro metadata tabulky jako filtr standard pro ukládání do mezipaměti, číst změny opravit, gc_grace, memtable_flush_period komprese a další?

Azure Cosmos DB nabízí výkon pro čtení/zápisu a propustnost bez nutnosti zásahu nastavení konfigurace a neúmyslně manipulaci s nimi.

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Platí pro tabulky Cassandra time to live (TTL)?

Hodnota TTL Ano, podporuje se.

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>Je možné monitorování uzel stav, stav repliky, uvolňování paměti a operační systém parametry dříve pomocí různých nástrojů? Co je potřeba monitorovat nyní?

Azure Cosmos DB je služba platformy, která vám pomůže zvýšit produktivitu a bez starostí o správu a sledování infrastruktury. Stačí postará o propustnosti, které je k dispozici na portálu metriky k vyhledání, pokud jste omezovaní a zvýšit nebo snížit, že propustnost.
Monitorujte [SLA](monitor-accounts.md).
Používejte [metriky](use-metrics.md) , které používají [diagnostické protokoly](logging.md).

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Které klientské sady SDK můžete pracovat s Apache Cassandra API z Azure Cosmos DB?

Ovladače klienta sady Apache Cassandra SDK, které používají CQLv3, byly použity pro klientské programy. Pokud máte jiné ovladače, které používáte, nebo pokud máte potíže, odešlete e-mail na [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="is-composite-partition-key-supported"></a>Klíč oddílu složené podporovány?

Ano, pomocí syntaxe regulárních k vytvoření oddílu složený klíč.

### <a name="can-i-use-sstableloader-for-data-loading"></a>Můžu použít sstableloader k načítání dat?

Ne, sstableloader se nepodporuje.

### <a name="can-an-on-premises-apache-cassandra-cluster-be-paired-with-azure-cosmos-dbs-cassandra-api"></a>Je možné místní cluster Apache Cassandra spárovat s rozhraní API Cassandraem Azure Cosmos DB?

V této službě Azure Cosmos DB má optimalizované prostředí pro prostředí cloud bez režijní náklady na operace. Pokud požadujete párování, pošlete e-mail na [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) s popisem vašeho scénáře. Pracujeme na tom, abychom pomohli spárovat místní nebo jiný cloudový cluster Cassandra s rozhraní API Cassandraem Cosomos DB.

### <a name="does-cassandra-api-provide-full-backups"></a>Rozhraní Cassandra API poskytuje úplné zálohy?

Azure Cosmos DB poskytuje dva bezplatné úplné zálohy pořízené v intervalu čtyři hodiny ještě dnes na všechna rozhraní API. Tím se zajistí, že není nutné nastavit plán zálohování a další věci.
Pokud chcete upravit dobu uchovávání a četnosti, pošlete e-mail na [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) nebo zvyšte případ podpory. Informace o možnostech zálohování najdete v článku [automatické online zálohování a obnovení pomocí Azure Cosmos DB](online-backup-and-restore.md) .

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Jak účet Cassandra API zpracovává převzetí služeb při selhání Pokud oblast přestane fungovat?

Azure Cosmos DB Cassandra API vypůjčí z globálně distribuovanou platformu Azure Cosmos DB. Aby bylo zajištěno, že vaše aplikace může tolerovat výpadky datového centra, povolte alespoň jednu oblast pro účet na portálu Azure Cosmos DB, [vývoj s využitím Azure Cosmos DBch účtů ve více oblastech](high-availability.md). Prioritu oblasti můžete nastavit pomocí portálu, který [se vyvíjí s Azure Cosmos DBmi účty ve více oblastech](high-availability.md).

Můžete přidat jakýkoli počet oblastí pro účet a řídit, kdy se převzít služby při selhání tím, že poskytuje priority převzetí služeb při selhání. Použít databázi, budete muset poskytnout aplikaci existuje příliš. Pokud tak učiníte, nebudou vaši zákazníci setkávat s výpadky.

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Rozhraní Apache Cassandra API všechny atributy indexu entity ve výchozím nastavení?

Rozhraní API Cassandra plánuje podporovat sekundární indexování, aby usnadnila vytváření selektivního indexu u určitých atributů. 


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Můžu používat sadu SDK Cassandra API místně v emulátoru?

Ano, to je podporováno.

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-change-feed-and-other-functionality-will-these-capabilities-be-added-to-the-cassandra-api"></a>Azure Cosmos DB jako platforma se jeví mít spoustu možností, jako je například změna kanálu a další funkce. Tyto možnosti přidáme k rozhraní Cassandra API?

Rozhraní Apache Cassandra API poskytuje stejné funkce jako Apache Cassandra CQL. Plánujeme viděl proveditelnosti podporu různých funkcí v budoucnosti.

### <a name="feature-x-of-regular-cassandra-api-isnt-working-as-today-where-can-the-feedback-be-provided"></a>Funkce x regulární Apache Cassandra API služby jako dnes nefunguje, ve kterém můžete zadat zpětnou vazbu?

Poskytněte zpětnou vazbu pomocí [hlasu pro uživatele](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md
