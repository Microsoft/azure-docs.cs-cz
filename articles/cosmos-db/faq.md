---
title: Nejčastější dotazy k různým rozhraním API v Azure Cosmos DB
description: Získejte odpovědi na nejčastější dotazy týkající se Azure Cosmos DB globálně distribuované databázové služby pro více modelů. Přečtěte si o kapacitě, úrovních výkonu a škálování.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 15e4fc3ce412807908c75fe25acecac0fe86d261
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72262794"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Nejčastější dotazy k různým rozhraním API v Azure Cosmos DB

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Jaké jsou typické případy použití pro Azure Cosmos DB?

Azure Cosmos DB je vhodná volba pro nové webové, mobilní a herní aplikace a aplikace IoT, kde je důležité automatické škálování, předvídatelný výkon, rychlé řazení dob odezvy milisekund a možnost dotazování na data bez schémat. Azure Cosmos DB sám o sobě poskytovat rychlý vývoj a podporu průběžné iterace datových modelů aplikací. Aplikace, které spravují uživatelem generovaný obsah a data, jsou [běžné případy použití pro Azure Cosmos DB](use-cases.md).

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Jak Azure Cosmos DB nabízí předvídatelný výkon?

[Jednotka žádosti](request-units.md) (ru) je mírou propustnosti v Azure Cosmos DB. Propustnost 1RU odpovídá propustnosti získání dokumentu o 1 KB. Každá operace v Azure Cosmos DB, včetně čtení, zápisu, dotazů SQL a spuštění uložených procedur, má deterministické RU, který je založen na propustnosti potřebné k dokončení operace. Místo toho, abyste se přemysleli o procesoru, vstupně-výstupních operacích a paměti a o tom, jak mají vliv na propustnost vaší aplikace, si můžete představit v souvislosti s jedním mírou RU.

U každého kontejneru Azure Cosmos můžete nakonfigurovat zřízenou propustnost v podobě ru propustnosti za sekundu. U aplikací v jakémkoli měřítku můžete určit srovnávací testy jednotlivých požadavků a zajistit, aby se v rámci všech požadavků vytvořil kontejner pro zpracování celkových jednotek požadavků. Propustnost kontejneru můžete také škálovat směrem nahoru nebo dolů podle potřeb vaší aplikace. Pokud chcete získat další informace o jednotkách žádostí a nápovědu k určení potřeb kontejneru, zkuste použít [kalkulačku propustnosti](https://www.documentdb.com/capacityplanner).

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Jak Azure Cosmos DB podporuje různé datové modely, jako je klíč/hodnota, sloupcový, dokument a graf?

Datové modely klíč/hodnota (tabulka), sloupcové, dokumentové a graf jsou nativně podporovány v důsledku návrhu ARS (atoms, Records a sequences), na který Azure Cosmos DB je postaven. Atomy, záznamy a sekvence je možné snadno namapovat a promítnout na různé datové modely. Rozhraní API pro podmnožinu modelů jsou nyní k dispozici (SQL, MongoDB, Table a Gremlin) a další specifické pro další datové modely budou v budoucnu k dispozici.

Azure Cosmos DB má modul indexování nezávislá schématu, který umožňuje automaticky indexovat všechna data, která ingestuje, bez nutnosti jakéhokoli schématu nebo sekundárních indexů od vývojáře. Tento modul spoléhá na sadu rozložení logických indexů (obrácený, sloupcový, strom), která odděluje rozložení úložiště od subsystémů zpracování dotazů a indexů. Cosmos DB má také možnost podporovat sadu přenosových protokolů a rozhraní API rozšiřitelným způsobem a efektivně je přeložit na základní datový model (1) a na rozložení logických indexů (2), což umožňuje jednoznačně podporovat více než jeden datový model nativně.

### <a name="can-i-use-multiple-apis-to-access-my-data"></a>Můžu pro přístup k datům použít více rozhraní API?

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Kde multi-model znamená Azure Cosmos DB podporuje více rozhraní API a více datových modelů, používají různá rozhraní API pro úložiště a síťový protokol různé formáty dat. Například SQL používá JSON, MongoDB používá BSON, tabulka používá EDM, Cassandra používá CQL, Gremlin používá GraphSON. V důsledku toho doporučujeme použít stejné rozhraní API pro veškerý přístup k datům v daném účtu.

Každé rozhraní API funguje nezávisle, s výjimkou Gremlin a SQL API, které jsou interoperabilní.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Je Azure Cosmos DB HIPAA kompatibilní?

Ano, Azure Cosmos DB je kompatibilní s HIPAA. HIPAA zavádí požadavky na použití, zveřejňování a zabezpečení informací o jednotlivých identifikovatelných stavech. Další informace najdete na [webu Microsoft Trust Center](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Jaká jsou omezení úložiště Azure Cosmos DB?

Neexistuje žádné omezení na celkové množství dat, které kontejner může ukládat v Azure Cosmos DB.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Jaká jsou omezení propustnosti Azure Cosmos DB?

Neexistuje žádné omezení na celkovou míru propustnosti, kterou může kontejner podporovat v Azure Cosmos DB. Klíčovým nápadem je distribuce úloh zhruba rovnoměrně mezi dostatečně velkým počtem klíčů oddílů.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>Jsou zašifrované režimy přímého připojení a připojení brány?

Ano oba režimy jsou vždy plně šifrované.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Kolik se Azure Cosmos DB náklady?

Podrobnosti najdete na stránce s [podrobnostmi o cenách Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) . Poplatky za použití Azure Cosmos DB se určují podle počtu zřízených kontejnerů, počtu hodin, po které byly kontejnery online, a zřízené propustnosti pro každý kontejner.

### <a name="is-a-free-account-available"></a>Je k dispozici bezplatný účet?

Ano, můžete si zdarma zaregistrovat účet s časovým limitem bez jakýchkoli závazků. Pokud se chcete zaregistrovat, navštivte web vyzkoušet [Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) nebo si přečtěte další informace v [nejčastějších dotazech k Azure Cosmos DB](#try-cosmos-db).

Pokud s Azure začínáte, můžete si zaregistrovat [bezplatný účet Azure](https://azure.microsoft.com/free/), který vám poskytne 30 dnů a kredit na to, abyste si vyzkoušeli všechny služby Azure. Máte-li předplatné sady Visual Studio, máte také nárok na používání [bezplatného kreditu Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) na jakékoli službě Azure.

[Emulátor Azure Cosmos DB](local-emulator.md) můžete také použít k místnímu vývoji a testování vaší aplikace bez nutnosti vytvářet předplatné Azure. Až budete spokojeni s tím, jak vaše aplikace funguje v emulátoru Azure Cosmos DB, můžete přepnout na používání účtu Azure Cosmos DB v cloudu.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Jak můžu získat další pomoc s Azure Cosmos DB?

Pokud se chcete zeptat na technický dotaz, můžete publikovat na jedno z těchto dvou fór otázek a odpovědí:

* [Fórum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow je nejvhodnější pro otázky týkající se programování. Ujistěte se, že je váš dotaz [v rámci vašeho tématu](https://stackoverflow.com/help/on-topic) [, a uveďte co nejvíce podrobností a vymažte otázku a odpovězte na](https://stackoverflow.com/help/how-to-ask)ně.

Pokud chcete požádat o nové funkce, vytvořte nový požadavek na [uživatelský hlas](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Pokud chcete vyřešit problém s vaším účtem, zapište do Azure Portal [žádost o podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) .

Další dotazy je možné odeslat týmu v [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com); Nejedná se však o alias technické podpory.

## <a id="try-cosmos-db"></a>Vyzkoušet Azure Cosmos DB předplatná

Nyní můžete využívat časově omezené prostředí Azure Cosmos DB bez předplatného, zdarma a závazky. Pokud se chcete zaregistrovat k předplatnému vyzkoušet Azure Cosmos DB, Projděte si [Azure Cosmos DB Vyzkoušejte zdarma](https://azure.microsoft.com/try/cosmosdb/). Toto předplatné je nezávislé na [bezplatné zkušební verzi Azure](https://azure.microsoft.com/free/)a dá se použít společně s bezplatnou zkušební verzí Azure nebo s placeným předplatným Azure.

Vyzkoušejte Azure Cosmos DB odběry se zobrazí v Azure Portal další předplatná přidružená k vašemu ID uživatele.

Následující podmínky se vztahují na zkoušení Azure Cosmos DB předplatných:

* Jeden [kontejner zřízený propustností](./set-throughput.md#set-throughput-on-a-container) na předplatné pro SQL, Gremlin API a účty tabulek.
* Až tři [zajištěné propustnosti](./set-throughput.md#set-throughput-on-a-container) v rámci předplatného pro účty MongoDB.
* Jedna [zajištěná propustnost databáze](./set-throughput.md#set-throughput-on-a-database) na jedno předplatné. Databáze zřízené v propustnosti můžou obsahovat libovolný počet kontejnerů.
* kapacita úložiště o velikosti 10 GB.
* Globální replikace je dostupná v následujících [oblastech Azure](https://azure.microsoft.com/regions/): Střed USA, Severní Evropa a jihovýchodní Asie.
* Maximální propustnost 5 000 RU/s v případě zřízení na úrovni kontejneru.
* Maximální propustnost 20 000 RU/s, pokud je zajištěna na úrovni databáze.
* Platnost předplatných vyprší po 30 dnech a dají se prodloužit na maximum 31 dní.
* Pro účty try Azure Cosmos DB se nedají vytvořit lístky podpory Azure; Nicméně podpora je poskytována pro předplatitele se stávajícími plány podpory.

## <a name="set-up-azure-cosmos-db"></a>Nastavit Azure Cosmos DB

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Návody se zaregistrovat Azure Cosmos DB?

Azure Cosmos DB je k dispozici v Azure Portal. Nejdřív si zaregistrujte předplatné Azure. Po registraci můžete do svého předplatného Azure přidat účet Azure Cosmos DB.

### <a name="what-is-a-master-key"></a>Co je hlavní klíč?

Hlavní klíč je token zabezpečení pro přístup ke všem prostředkům pro účet. Jednotlivci s klíčem mají přístup pro čtení a zápis ke všem prostředkům v databázovém účtu. Při distribuci hlavních klíčů buďte opatrní. Primární hlavní klíč a sekundární hlavní klíč jsou k dispozici v okně **klíče** [Azure Portal][azure-portal]. Další informace o klíčích najdete v tématu [zobrazení, kopírování a opětovné vygenerování přístupových klíčů](manage-with-cli.md#list-account-keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Jaké jsou oblasti, na které se PreferredLocations dá nastavit?

Hodnotu PreferredLocations lze nastavit na libovolnou oblast Azure, ve které je Cosmos DB k dispozici. Seznam oblastí, které jsou k dispozici, najdete v tématu [oblasti Azure](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Je potřeba vědět o tom, co byste měli vědět při distribuci dat napříč celým světem přes datacentra Azure?

Azure Cosmos DB se nachází ve všech oblastech Azure, jak je uvedeno na stránce [oblasti Azure](https://azure.microsoft.com/regions/) . Vzhledem k tomu, že se jedná o základní službu, má každé nové datové centrum Azure Cosmos DBou přítomnost.

Při nastavování oblasti Pamatujte na to, že Azure Cosmos DB ctí cloudy svrchovaného a státní správy. To znamená, že pokud v [oblasti svrchovaného](https://azure.microsoft.com/global-infrastructure/)účtu vytvoříte účet, nebudete moci replikovat z této [oblasti svrchovan](https://azure.microsoft.com/global-infrastructure/). Podobně nemůžete povolit replikaci do jiných umístění svrchovaného účtu mimo účet.

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>Je možné přepínat z zřizování propustnosti na úrovni kontejneru do zřizování propustnosti na úrovni databáze? Nebo naopak

Zřizování propustnosti na úrovni kontejneru a databáze jsou oddělené nabídky a přepínání mezi nimi vyžaduje migraci dat ze zdroje do cíle. To znamená, že je potřeba vytvořit novou databázi nebo nový kontejner a pak migrovat data pomocí [knihovny hromadného prováděcího modulu](bulk-executor-overview.md) nebo [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>Podporuje Azure CosmosDB analýzu časových řad?

Ano, Azure CosmosDB podporuje analýzu časových řad. tady je ukázka pro [vzor časových řad](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns). V této ukázce se dozvíte, jak pomocí kanálu změn sestavovat agregovaná zobrazení v datech časových řad. Tento přístup můžete roztáhnout pomocí streamování Sparku nebo jiného procesoru streamování dat.

## <a name="what-are-the-azure-cosmos-db-service-quotas-and-throughput-limits"></a>Jaké jsou kvóty Azure Cosmos DB služeb a omezení propustnosti

Další informace najdete v článku [kvóty Azure Cosmos DB služeb](concepts-limits.md) a [v rámci limitu pro jednotlivé kontejnery a databáze](set-throughput.md#comparison-of-models) .

## <a name="sql-api"></a>ROZHRANÍ SQL API

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Návody začít vyvíjet s rozhraním SQL API?

Nejdřív si musíte zaregistrovat předplatné Azure. Po registraci předplatného Azure můžete k předplatnému Azure přidat kontejner rozhraní SQL API. Pokyny k přidání Azure Cosmos DB účtu najdete v tématu [Vytvoření účtu databáze Azure Cosmos](create-sql-api-dotnet.md#create-account).

Sady [SDK](sql-api-sdk-dotnet.md) jsou dostupné pro .NET, Python, Node. js, JavaScript a Java. Vývojáři můžou také používat [rozhraní RESTFUL HTTP API](/rest/api/cosmos-db/) k interakci s Azure Cosmos DBmi prostředky z různých platforem a jazyků.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Můžu získat přístup k některým předem připraveným ukázkám, aby bylo možné začít?

Ukázky pro sady SDK rozhraní SQL API [.NET](sql-api-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node. js](sql-api-nodejs-samples.md)a [Python](sql-api-python-samples.md) jsou k dispozici na GitHubu.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>Podporuje databáze rozhraní SQL API data bez schémat?

Ano, rozhraní SQL API umožňuje aplikacím ukládat libovolné dokumenty JSON bez definic schémat nebo tipů. Data jsou okamžitě k dispozici pro dotazování prostřednictvím rozhraní Azure Cosmos DB SQL Query.

### <a name="does-the-sql-api-support-acid-transactions"></a>Podporuje rozhraní SQL API transakce s KYSELINou?

Ano, rozhraní SQL API podporuje transakce mezi dokumenty vyjádřené jako procedury a triggery uložené v JavaScriptu. Transakce jsou vymezeny na jeden oddíl v rámci každého kontejneru a jsou spouštěny s sémantikou kyseliny jako "vše nebo nic" izolované od ostatních souběžně prováděných požadavků kódu a uživatelů. Pokud jsou výjimky vyvolány prostřednictvím spuštění kódu aplikace JavaScriptu na straně serveru, celá transakce je vrácena zpět. 

### <a name="what-is-a-container"></a>Co je kontejner?

Kontejner je skupina dokumentů a jejich přidružená logika aplikace JavaScriptu. Kontejner je fakturovatelná entita, kde [náklady](performance-levels.md) určují propustnost a využité úložiště. Kontejnery můžou zahrnovat jeden nebo víc oddílů nebo serverů a můžou se škálovat na zpracování prakticky neomezených objemů úložiště nebo propustnosti.

* Pro rozhraní SQL API se kontejner mapuje na kontejner.
* Pro Cosmos DB rozhraní API pro účty MongoDB se kontejner mapuje na kolekci.
* Pro účty Cassandra a rozhraní API pro tabulky se kontejner mapuje na tabulku.
* V případě účtů rozhraní Gremlin API se kontejner mapuje do grafu.

Kontejnery jsou také fakturačními entitami pro Azure Cosmos DB. Každý kontejner se účtuje po hodinách na základě zřízené propustnosti a využitého prostoru úložiště. Další informace najdete v tématu [Azure Cosmos DB ceny](https://azure.microsoft.com/pricing/details/cosmos-db/).

### <a name="how-do-i-create-a-database"></a>Návody vytvořit databázi?

Databáze můžete vytvářet pomocí [Azure Portal](https://portal.azure.com), jak je popsáno v tématu [Přidání kontejneru](create-sql-api-java.md#add-a-container), jedna ze sady [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)nebo [rozhraní REST API](/rest/api/cosmos-db/).

### <a name="how-do-i-set-up-users-and-permissions"></a>Návody nastavit uživatele a oprávnění?

Uživatele a oprávnění můžete vytvořit pomocí jedné ze sady [sdk Cosmos DB API](sql-api-sdk-dotnet.md) nebo rozhraní [REST API](/rest/api/cosmos-db/).

### <a name="does-the-sql-api-support-sql"></a>Podporuje rozhraní SQL API SQL?

Dotazovací jazyk SQL podporovaný účty SQL API je rozšířená podmnožina funkcí dotazů, které podporuje SQL Server. Azure Cosmos DB dotazovací jazyk SQL poskytuje bohatý hierarchické a relační operátory a rozšiřitelnost prostřednictvím uživatelsky definovaných funkcí (UDF) založených na JavaScriptu. Gramatika JSON umožňuje modelování dokumentů JSON jako stromů s označenými uzly, které jsou používány postupy Azure Cosmos DB automatické indexování a dialektem dotazu SQL Azure Cosmos DB. Informace o použití gramatiky SQL najdete v článku [dotaz SQL][query] .

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>Podporuje rozhraní SQL API agregační funkce SQL?

Rozhraní SQL API podporuje agregaci s nízkou latencí v jakémkoli měřítku prostřednictvím agregačních funkcí `COUNT`, `MIN`, `MAX`, `AVG` a `SUM` pomocí gramatiky SQL. Další informace najdete v tématu [agregační funkce](sql-query-aggregates.md).

### <a name="how-does-the-sql-api-provide-concurrency"></a>Jak rozhraní API SQL zajišťuje souběžnost?

Rozhraní SQL API podporuje optimistické řízení souběžnosti (OCC) prostřednictvím značek entit HTTP nebo značek ETag. Každý prostředek rozhraní API SQL má ETag a značka ETag je nastavená na serveru pokaždé, když se dokument aktualizuje. Hlavička ETag a aktuální hodnota jsou zahrnuty ve všech zprávách s odpovědí. Značky ETag se dají použít spolu s hlavičkou If-Match, která umožňuje serveru rozhodnout, jestli se má prostředek aktualizovat. Hodnota If-Match je hodnota ETag, která se má zkontrolovat. Pokud hodnota ETag odpovídá hodnotě ETag serveru, prostředek se aktualizuje. Pokud značka ETag již není aktuální, server odmítne operaci s kódem odpovědi "selhání předběžné podmínky HTTP 412". Klient pak znovu načte prostředek, aby získal aktuální hodnotu ETag pro daný prostředek. Značky ETag lze navíc použít s hlavičkou If-None-Match k určení, zda je nutné znovu načíst prostředek.

Chcete-li použít optimistickou souběžnost v rozhraní .NET, použijte třídu [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) . Ukázku .NET najdete v tématu [program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) v ukázce DocumentManagement na GitHubu.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Návody provádět transakce v rozhraní SQL API?

Rozhraní SQL API podporuje transakce integrované s jazykem prostřednictvím procedur a triggerů uložených v JavaScriptu. Všechny databázové operace uvnitř skriptů jsou spouštěny při izolaci snímku. Pokud se jedná o kontejner s jedním oddílem, je spuštění vymezeno na kontejner. Pokud je kontejner rozdělený, je spuštění vymezeno na dokumenty se stejnou hodnotou klíče oddílu v rámci kontejneru. Snímek verzí dokumentu (ETags) je proveden na začátku transakce a potvrzen pouze v případě, že skript je úspěšný. Pokud JavaScript vyvolá chybu, transakce se vrátí zpět. Další informace najdete v tématu [programování JavaScriptu na straně serveru pro Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Jak můžu hromadně vkládat dokumenty do Cosmos DB?

Dokumenty můžete do Azure Cosmos DB hromadně vkládat jedním z následujících způsobů:

* Nástroj hromadný prováděcí modul, jak je popsáno v tématu [použití knihovny hromadného prováděcího modulu .NET](bulk-executor-dot-net.md) a [použití knihovny hromadného vykonavatele Java](bulk-executor-java.md)
* Nástroj pro migraci dat, jak je popsáno v tématu [Nástroj pro migraci databáze pro Azure Cosmos DB](import-data.md).
* Uložené procedury, jak je popsáno v tématu [programování JavaScriptu na straně serveru pro Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="does-the-sql-api-support-resource-link-caching"></a>Podporuje rozhraní API SQL odkaz na prostředek do mezipaměti?

Ano, protože Azure Cosmos DB je služba RESTful, odkazy na prostředky jsou neměnné a dají se ukládat do mezipaměti. Klienti rozhraní SQL API mohou zadat hlavičku "If-None-Match" pro čtení na libovolný dokument nebo kontejner, který odpovídá prostředkům, a po změně verze serveru aktualizovat jejich místní kopie.

### <a name="is-a-local-instance-of-sql-api-available"></a>Je k dispozici místní instance rozhraní SQL API?

Ano. [Emulátor Azure Cosmos DB](local-emulator.md) zajišťuje emulaci služby Cosmos DB s vysokou přesností. Podporuje funkce, které jsou stejné jako Azure Cosmos DB, včetně podpory pro vytváření a dotazování dokumentů JSON, zřizování a škálování kolekcí a spouštění uložených procedur a triggerů. Můžete vyvíjet a testovat aplikace pomocí emulátoru Azure Cosmos DB a nasadit je do Azure v globálním měřítku, a to tak, že pro Azure Cosmos DB provedete jednu změnu konfigurace koncového bodu připojení.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>Proč jsou při zobrazení v Průzkumníku dat na portálu dlouhé hodnoty s plovoucí desetinnou čárkou v dokumentu zaokrouhlené.

Toto je omezení JavaScriptu. JavaScript používá čísla formátu s plovoucí desetinnou čárkou s dvojitou přesností, jak je uvedeno v IEEE 754, a může bezpečně držet čísla mezi-(2<sup>53</sup> -1) a 2<sup>53</sup>-1 (tj., 9007199254740991).

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Kde jsou oprávnění povolená v hierarchii objektů?

Vytváření oprávnění pomocí ResourceTokens je povoleno na úrovni kontejneru a jejích potomků (například dokumenty, přílohy). To znamená, že pokus o vytvoření oprávnění v databázi nebo na úrovni účtu není aktuálně povolen.

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>Rozhraní API pro MongoDB Azure Cosmos DB

### <a name="what-is-the-azure-cosmos-dbs-api-for-mongodb"></a>Co je rozhraní API Azure Cosmos DB pro MongoDB?

Rozhraní API pro Azure Cosmos DB pro MongoDB je vrstva kompatibility protokolů, která umožňuje aplikacím snadno a transparentně komunikovat s nativním databázovým strojem Azure Cosmos pomocí stávajících sad SDK a ovladačů podporovaných komunitou pro MongoDB. Vývojáři teď můžou využívat stávající MongoDB sady nástrojů a dovednosti k sestavování aplikací, které využívají Azure Cosmos DB. Vývojáři mají výhodu jedinečných funkcí Azure Cosmos DB, které zahrnují globální distribuci s více hlavními replikacemi, automatické indexování, údržbu zálohování, finančně zálohovaných smluv o úrovni služeb (SLA) atd.

### <a name="how-do-i-connect-to-my-database"></a>Návody se připojit k databázi?

Nejrychlejší způsob, jak se připojit k databázi Cosmos s rozhraním API Azure Cosmos DB pro MongoDB, je přejít na [Azure Portal](https://portal.azure.com). Přejděte na svůj účet a potom v levé navigační nabídce klikněte na **rychlé zprovoznění**. Rychlý Start je nejlepší způsob, jak získat fragmenty kódu pro připojení k vaší databázi.

Azure Cosmos DB vynutil přísné požadavky na zabezpečení a standardy. Účty Azure Cosmos DB vyžadují ověřování a zabezpečenou komunikaci prostřednictvím protokolu SSL, proto nezapomeňte používat TLSv 1.2.

Další informace najdete v tématu [připojení k databázi Cosmos s rozhraním API Azure Cosmos DB pro MongoDB](connect-mongodb-account.md).

### <a name="are-there-additional-error-codes-that-i-need-to-deal-with-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Existují další chybové kódy, které potřebuji řešit při používání rozhraní API Azure Cosmos DB pro MongoDB?

Spolu s běžnými kódy chyb MongoDB má rozhraní API Azure Cosmos DB pro MongoDB vlastní konkrétní kódy chyb:

| Chyba               | Kód  | Popis  | Řešení  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Celkový počet spotřebovaných jednotek žádostí je vyšší než zřízené procento požadavků a Jednotková sazba pro daný kontejner a byla omezena. | Zvažte možnost škálování propustnosti přiřazené kontejneru nebo sady kontejnerů z Azure Portal nebo opakujte pokus. |
| ExceededMemoryLimit | 16501 | Jako služba pro více tenantů se operace převzala v průběhu plnění paměti klienta. | Snižte rozsah operace prostřednictvím přísnějších kritérií dotazu nebo kontaktujte podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br>Příklad: <em> @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4DB. GetCollection (' Users '). Aggregate ([<br>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 {$match: {Name: "Andy"}} <br>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 {$sort: {Age:-1}}<br>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3]) </em>) |

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>Je ovladač Simba pro MongoDB podporovaný pro použití s rozhraním API Azure Cosmos DB pro MongoDB?

Ano, můžete použít ovladač ODBC Simba Mongo s rozhraním API Azure Cosmos DB pro MongoDB

## <a id="table"></a>rozhraní API pro tabulky

### <a name="how-can-i-use-the-table-api-offering"></a>Jak můžu použít nabídku rozhraní API pro tabulky?

Rozhraní API pro tabulky Azure Cosmos DB je k dispozici v [Azure Portal][azure-portal]. Nejdřív si musíte zaregistrovat předplatné Azure. Po registraci můžete do svého předplatného Azure přidat účet Azure Cosmos DB rozhraní API pro tabulky a pak do svého účtu přidat tabulky.

Podporované jazyky a související rychlé starty najdete v [úvodu k Azure Cosmos DB rozhraní API pro tabulky](table-introduction.md).

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Potřebuji pro používání rozhraní API pro tabulky novou sadu SDK?

Ne, existující sady SDK úložiště by měly pořád fungovat. Doporučuje se ale, aby jeden vždy dostal nejnovější sady SDK na nejlepší podporu a v mnoha případech vynikající výkon. Seznam dostupných jazyků najdete v tématu [Úvod do Azure Cosmos DB rozhraní API pro tabulky](table-introduction.md).

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Kde se rozhraní API pro tabulky neshodné s chováním úložiště tabulek v Azure?

Existují některé rozdíly v chování, které uživatelé přicházejí z Azure Table Storage, a chtějí vytvářet tabulky s Azure Cosmos DB rozhraní API pro tabulky by měli mít na paměti:

* Azure Cosmos DB rozhraní API pro tabulky používá rezervovaný model kapacity, aby zajistil Zaručený výkon, ale to znamená, že po vytvoření tabulky platí za kapacitu, a to i v případě, že se kapacita nepoužívá. S Azure Table Storage jedna platí jenom pro využitou kapacitu. To pomáhá vysvětlit, proč rozhraní API pro tabulky může nabízet smlouvu SLA 10 MS pro čtení a 15 MS Write na 99 percentilu, zatímco Azure Table Storage nabízí 10 sekund SLA. Ale jako důsledek s rozhraní API pro tabulky tabulek, a to i s prázdnými tabulkami bez jakýchkoli požadavků, a to za účelem zajištění dostupnosti kapacity pro zpracování všech žádostí o smlouvu SLA nabízených Azure Cosmos DB.
* Výsledky dotazu vrácené rozhraní API pro tabulky nejsou seřazené v pořadí podle klíče oddílu nebo klíče řádku, protože jsou v úložišti tabulek Azure.
* Klíče řádků můžou být jenom až 255 bajtů.
* Dávky můžou mít až 2 MB.
* CORS není momentálně podporovaná.
* Názvy tabulek v Azure Table Storage nerozlišují velká a malá písmena, ale jsou v Azure Cosmos DB rozhraní API pro tabulky
* Některé z interních formátů Azure Cosmos DB pro kódování informací, jako jsou například binární pole, nejsou aktuálně tak efektivní jako jedna. Proto to může způsobit neočekávané omezení velikosti dat. Aktuálně jedna z nich například nemůže použít celý jeden typ MB entity tabulky pro ukládání binárních dat, protože kódování zvyšuje velikost dat.
* Název vlastnosti entity ' ID ' aktuálně není podporován.
* TableQuery TakeCount není omezen na 1000

V souvislosti s REST API je k dispozici několik možností pro koncové body nebo dotazy, které nepodporují Azure Cosmos DB rozhraní API pro tabulky:

| Metody REST | Možnost koncový bod nebo dotaz REST | Adresy URL dokumentů | Vysvětlení |
| ------------| ------------- | ---------- | ----------- |
| ZÍSKAT, PUT | /? restype = service@comp = vlastnosti| [Nastavení vlastností služby Table](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) a [získání vlastností služby Table Service](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) | Tento koncový bod se používá k nastavení pravidel CORS, konfigurace analýzy úložiště a nastavení protokolování. CORS není momentálně podporovaná a analýzy a protokolování se v Azure Cosmos DB než v Azure Storage tabulkách zpracovávají jinak. |
| NASTAVENÍ | / @ no__t-1table-Resource-Name > | [Požadavek na tabulku CORS před lety](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | Toto je součást CORS, kterou Azure Cosmos DB v současnosti nepodporuje. |
| Čtěte | /? restype = service@comp = statistiky | [Získat statistiku služby Table](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | Poskytuje informace o tom, jak rychle se data replikují mezi primárním a sekundárním serverem. V Cosmos DB to není potřeba, protože replikace je součástí zápisů. |
| ZÍSKAT, PUT | /myTable? COMP = ACL | [Získat seznam ACL tabulky](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) a [nastavit seznam ACL tabulky](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) | Tím se získá a nanastaví uložené zásady přístupu používané ke správě sdílených přístupových podpisů (SAS). I když se SAS podporuje, nastavují a spravují jinak. |

Kromě Azure Cosmos DB rozhraní API pro tabulky podporuje jenom formát JSON, ne ATOM.

I když Azure Cosmos DB podporuje sdílené přístupové podpisy (SAS), existují určité zásady, které nepodporují, konkrétně ty, které se týkají operací správy, jako je právo vytvářet nové tabulky.

Konkrétně pro sadu .NET SDK existují některé třídy a metody, které Azure Cosmos DB aktuálně nepodporují.

| Třída | Nepodporovaná metoda |
|-------|-------- |
| Cloudtableclient vám | @no__t – 0ServiceProperties * |
|                  | @no__t – 0ServiceStats * |
| CloudTable | SetPermissions* |
|            | GetPermissions * |
| TableServiceContext | * (Tato třída je zastaralá.) |
| TableServiceEntity | " " |
| TableServiceExtensions | " " |
| TableServiceQuery | " " |

Pokud některý z těchto rozdílů představuje problém pro váš projekt, obraťte se na [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) a sdělte nám, jak nás znáte.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Návody poskytnout zpětnou vazbu k sadě SDK nebo chybám?

Svůj názor můžete sdílet některým z následujících způsobů:

* [Uživatelský hlas](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [Fórum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow je nejvhodnější pro otázky týkající se programování. Ujistěte se, že je váš dotaz [v rámci vašeho tématu](https://stackoverflow.com/help/on-topic) [, a uveďte co nejvíce podrobností a vymažte otázku a odpovězte na](https://stackoverflow.com/help/how-to-ask)ně.

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Jaký je připojovací řetězec, který potřebuji použít pro připojení k rozhraní API pro tabulky?

Připojovací řetězec:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

Připojovací řetězec můžete získat na stránce připojovací řetězec v Azure Portal.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Návody přepsat nastavení konfigurace pro možnosti žádosti v sadě .NET SDK pro rozhraní API pro tabulky?

Některá nastavení jsou zpracována v metodě CreateCloudTableClient a další prostřednictvím souboru App. config v části appSettings v klientské aplikaci. Informace o nastavení konfigurace najdete v tématu [možnosti Azure Cosmos DB](tutorial-develop-table-dotnet.md).

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Existují nějaké změny pro zákazníky, kteří používají existující sady SDK služby Azure Table Storage?

Žádné Pro stávající nebo nové zákazníky, kteří používají existující sady SDK služby Azure Table Storage, nejsou žádné změny.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Návody zobrazit tabulková data uložená v Azure Cosmos DB pro použití s rozhraní API pro tabulky?

K procházení dat můžete použít Azure Portal. Můžete také použít kód rozhraní API pro tabulky nebo nástroje, které jsou uvedené v další odpovědi.

### <a name="which-tools-work-with-the-table-api"></a>Které nástroje pracují s rozhraní API pro tabulky?

Můžete použít [Průzkumník služby Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

Nástroje s flexibilitou pro povýšení připojovacího řetězce ve výše zadaném formátu mohou podporovat nové rozhraní API pro tabulky. Seznam nástrojů tabulky je k dispozici na stránce [Azure Storage klientských nástrojů](../storage/common/storage-explorers.md) .

### <a name="is-the-concurrency-on-operations-controlled"></a>Je souběžnost řízených operací?

Ano, Optimistická souběžnost je poskytována prostřednictvím použití mechanismu ETag.

### <a name="is-the-odata-query-model-supported-for-entities"></a>Je model dotazů OData podporovaný pro entity?

Ano, rozhraní API pro tabulky podporuje dotazy OData a dotaz LINQ.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Můžu se připojit k Azure Table Storage a Azure Cosmos DB rozhraní API pro tabulky vedle sebe ve stejné aplikaci?

Ano, můžete se připojit tak, že vytvoříte dvě samostatné instance Cloudtableclient vám, z nichž každý odkazuje na vlastní identifikátor URI prostřednictvím připojovacího řetězce.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Návody migrovat existující aplikaci Azure Table Storage do této nabídky?

[AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) a [Nástroj pro migraci dat Azure Cosmos DB](import-data.md) jsou podporované.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Jak funguje rozšíření velikosti úložiště pro tuto službu, pokud například začínáte *n* GB dat a data se v průběhu času zvětší na 1 TB?

Azure Cosmos DB je navržený tak, aby poskytoval neomezené úložiště pomocí horizontálního škálování. Služba může monitorovat a efektivně rozšiřovat vaše úložiště.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Návody monitorovat nabídku rozhraní API pro tabulky?

K monitorování požadavků a využití úložiště můžete použít podokno **metriky** rozhraní API pro tabulky.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Návody vypočítat požadovanou propustnost?

Kapacitu Estimator můžete použít k výpočtu TableThroughput, který je potřeba pro operace. Další informace najdete v tématu [odhad jednotek žádostí a úložiště dat](https://www.documentdb.com/capacityplanner). Obecně můžete zobrazit vaši entitu jako JSON a zadat čísla pro vaše operace.

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Můžu použít sadu rozhraní API pro tabulky SDK lokálně s emulátorem?

V tuto chvíli ne.

### <a name="can-my-existing-application-work-with-the-table-api"></a>Může moje existující aplikace pracovat s rozhraní API pro tabulky?

Ano, je podporováno stejné rozhraní API.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>Potřebuji migrovat stávající aplikace služby Azure Table Storage do sady SDK, pokud nechcete používat funkce rozhraní API pro tabulky?

Ne, můžete vytvářet a používat existující prostředky služby Azure Table Storage bez přerušení jakéhokoli druhu. Pokud ale rozhraní API pro tabulky nepoužíváte, nemůžete využít automatický index, další možnost konzistence nebo globální distribuci.

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Návody přidat replikaci dat v rozhraní API pro tabulky napříč více než jednou oblastí Azure?

[Nastavení globální replikace](tutorial-global-distribution-sql-api.md#portal) Azure Cosmos DBového portálu můžete použít k přidání oblastí, které jsou vhodné pro vaši aplikaci. Chcete-li vytvořit globálně distribuovanou aplikaci, měli byste také přidat aplikaci s informacemi PreferredLocation nastavenými do místní oblasti pro zajištění nízké latence čtení.

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Návody změnit primární oblast zápisu pro účet v rozhraní API pro tabulky?

K přidání oblasti a následnému převzetí služeb při selhání v požadované oblasti můžete použít podokno Azure Cosmos DB globálního replikačního portálu. Pokyny najdete v tématu [vývoj s využitím účtů Azure Cosmos dB ve více oblastech](high-availability.md).

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Návody nakonfigurovat preferované oblasti čtení pro nízkou latenci při distribuci dat?

K usnadnění čtení z místního umístění použijte klíč PreferredLocation v souboru App. config. U existujících aplikací rozhraní API pro tabulky vyvolá chybu, pokud je nastavené LocationMode. Odeberte tento kód, protože rozhraní API pro tabulky přebírá tyto informace ze souboru App. config. 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Jak se mám domnívat o úrovních konzistence v rozhraní API pro tabulky?

Azure Cosmos DB poskytuje dobře odůvodněné kompromisy mezi konzistencí, dostupností a latencí. Azure Cosmos DB nabízí pro rozhraní API pro tabulky vývojářům pět úrovní konzistence, takže můžete zvolit správný model konzistence na úrovni tabulky a při dotazování na data vytvořit jednotlivé požadavky. Když se klient připojí, může určit úroveň konzistence. Úroveň můžete změnit pomocí argumentu consistencyLevel pro CreateCloudTableClient.

Rozhraní API pro tabulky poskytuje s nízkou latencí čtení s "čtenými vlastními zápisy" s konzistencí s vazbou na neaktuálnost jako výchozí. Další informace najdete v tématu [úrovně konzistence](consistency-levels.md).

Ve výchozím nastavení poskytuje Azure Table Storage silnou konzistenci v rámci oblasti a konečnou konzistenci v sekundárních lokalitách.

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Nabízí Azure Cosmos DB rozhraní API pro tabulky větší úrovně konzistence než Azure Table Storage?

Ano, informace o tom, jak těžit z distribuované povahy Azure Cosmos DB, najdete v tématu [úrovně konzistence](consistency-levels.md). Vzhledem k tomu, že záruky jsou k dispozici pro úrovně konzistence, můžete je použít s jistotou.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Pokud je povolená globální distribuce, jak dlouho trvá replikace dat?

Azure Cosmos DB potvrdí data trvale v místní oblasti a hned je pošle do jiných oblastí, a to v milisekundách. Tato replikace závisí jenom na době odezvy datacentra (RTT). Další informace o schopnostech globální distribuce Azure Cosmos DB najdete v tématu [Azure Cosmos DB: globálně distribuovaná databázová služba v Azure](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>Je možné změnit úroveň konzistence žádosti o čtení?

Pomocí Azure Cosmos DB můžete nastavit úroveň konzistence na úrovni kontejneru (v tabulce). Pomocí sady .NET SDK můžete změnit úroveň poskytnutím hodnoty pro klíč TableConsistencyLevel v souboru App. config. Možné hodnoty jsou: silná, ohraničená neaktuálnost, relace, konzistentní předpona a případné. Další informace najdete v tématu [přizpůsobitelné úrovně konzistence dat v Azure Cosmos DB](consistency-levels.md). Klíčovým nápadem je, že úroveň konzistence žádosti nemůžete nastavit na více než nastavení pro tabulku. Například nemůžete nastavit úroveň konzistence pro tabulku, kdykoli a úroveň konzistence žádosti na silný.

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Jak rozhraní API pro tabulky převzetí služeb při selhání v případě výpadku oblasti?

Rozhraní API pro tabulky využívá globálně distribuovanou platformu Azure Cosmos DB. Aby bylo zajištěno, že vaše aplikace může tolerovat výpadky datového centra, povolte alespoň jednu oblast pro účet na portálu Azure Cosmos DB, [vývoj s využitím Azure Cosmos DBch účtů ve více oblastech](high-availability.md). Prioritu oblasti můžete nastavit pomocí portálu, který [se vyvíjí s Azure Cosmos DBmi účty ve více oblastech](high-availability.md).

Pro účet můžete přidat tolik oblastí, kolik chcete, a určit, kde při převzetí služeb při selhání bude možné převzít služby při selhání. Chcete-li použít databázi, je nutné zadat také aplikaci. Když to uděláte, vaši zákazníci nebudou mít žádný výpadek. [Nejnovější klientská sada SDK pro .NET](table-sdk-dotnet.md) je automatická odstavování, ale ostatní sady SDK ne. To znamená, že dokáže detekovat oblast, která je mimo provoz, a automaticky převezme služby při selhání nové oblasti.

### <a name="is-the-table-api-enabled-for-backups"></a>Je rozhraní API pro tabulky povoleno zálohování?

Ano, rozhraní API pro tabulky využívá platformu Azure Cosmos DB k zálohování. Zálohování se provádí automaticky. Další informace najdete v tématu [zálohování a obnovení online pomocí Azure Cosmos DB](online-backup-and-restore.md).

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>Má rozhraní API pro tabulky index všechny atributy entity ve výchozím nastavení?

Ano, všechny atributy entity jsou indexovány ve výchozím nastavení. Další informace najdete v tématu [Azure Cosmos DB: zásady indexování](index-policy.md).

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>Neznamená to, že nemusíte vytvořit více než jeden index, aby bylo možné vyhovět dotazům?

Ano, Azure Cosmos DB rozhraní API pro tabulky poskytuje automatické indexování všech atributů bez definice schématu. Tato automatizace uvolní vývojářům, aby se zaměřili na aplikaci, a ne na vytváření a správu indexů. Další informace najdete v tématu [Azure Cosmos DB: zásady indexování](index-policy.md).

### <a name="can-i-change-the-indexing-policy"></a>Můžu změnit zásady indexování?

Ano, zásady indexování můžete změnit zadáním definice indexu. Musíte správně zakódovat a napřed nastavit řídicí.

V případě sad non-.NET SDK je možné zásady indexování nastavit jenom na portálu na **Průzkumník dat**, přejít na konkrétní tabulku, kterou chcete změnit, a pak přejít na **& nastavení škálování**– > zásady indexování, proveďte požadovanou změnu a pak klikněte na **Uložit** . .

Ze sady .NET SDK je možné ji odeslat do souboru App. config:

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

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Azure Cosmos DB jako platforma se jeví mít spoustu možností, jako je řazení, agregace, hierarchie a další funkce. Budete tyto možnosti přidávat do rozhraní API pro tabulky?

Rozhraní API pro tabulky poskytuje stejné funkce dotazů jako úložiště tabulek Azure. Azure Cosmos DB podporuje také řazení, agregace, geoprostorové dotazy, hierarchii a široké spektrum integrovaných funkcí. Další informace najdete v tématu [SQL dotazy](how-to-sql-query.md).

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Kdy bych měl změnit TableThroughput rozhraní API pro tabulky?

TableThroughput byste měli změnit, pokud platí některá z následujících podmínek:

* Provádíte extrakci, transformaci a načítání dat (ETL), nebo chcete v krátké době nahrávat velké množství dat.
* Potřebujete větší propustnost z kontejneru nebo ze sady kontejnerů na back-endu. Vidíte například, že použitá propustnost je větší než zajištěná propustnost a že se vám přihlásilo omezení. Další informace najdete v tématu [nastavení propustnosti pro kontejnery Azure Cosmos](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Můžu snížit propustnost rozhraní API pro tabulky tabulky a snížit jejich velikost?

Ano, k škálování propustnosti můžete použít podokno škále Azure Cosmos DBového portálu. Další informace najdete v tématu [nastavení propustnosti](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Je výchozí TableThroughput sada pro nově zřízené tabulky?

Ano, Pokud nepřepíšete TableThroughput pomocí App. config a nepoužijete předem vytvořený kontejner v Azure Cosmos DB, služba vytvoří tabulku s propustností 400.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Je u stávajících zákazníků služby Azure Table Storage nějaká změna cen?

Žádné U stávajících zákazníků Azure Table Storage se cena nemění.

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Jak se počítá cena za rozhraní API pro tabulky?

Cena závisí na přidělených TableThroughput.

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Návody zpracování všech sazeb v tabulkách v nabídce rozhraní API pro tabulky?

Pokud je frekvence požadavků větší než kapacita zřízené propustnosti pro příslušný kontejner nebo sadu kontejnerů, zobrazí se chyba a sada SDK se znovu pokusí zavolat pomocí zásad opakování.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Proč potřebuji zvolit propustnost mimo PartitionKey a RowKey, abyste mohli využít Azure Cosmos DB rozhraní API pro tabulky nabídky?

Azure Cosmos DB nastaví výchozí propustnost pro váš kontejner, pokud ho nezadáte do souboru App. config nebo prostřednictvím portálu.

Azure Cosmos DB poskytuje záruky pro výkon a latenci a horní meze provozu. Tato záruka je možná, když modul může vyhovět zásadám správného řízení operací klienta. Nastavení TableThroughput zajistí, že získáte zaručenou propustnost a latenci, protože platforma rezervuje tuto kapacitu a zaručuje provozní úspěch.

Pomocí specifikace propustnosti je možné elasticky změnit, aby vytěžit z sezónnost vaší aplikace, splnění potřeb propustnosti a úspory nákladů.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>Služba Azure Table Storage je pro mě nenákladná, protože mám platit jenom za uložení dat a jenom zřídka dotazování. V nabídce Azure Cosmos DB rozhraní API pro tabulky se jeví, že se mi bude účtovat, i když jsem neprováděl jednu transakci nebo cokoli neuložilo. Můžete vysvětlit?

Azure Cosmos DB je navržený tak, aby byl globálně distribuovaný systém založený na smlouvě SLA se zárukami na dostupnost, latenci a propustnost. Když rezervujete propustnost v Azure Cosmos DB, zaručuje se to na rozdíl od propustnosti jiných systémů. Azure Cosmos DB poskytuje další možnosti, které si zákazníci požádali, jako jsou sekundární indexy a globální distribuce.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Nikdy nezískám plnou kvótu "oznámení" (což znamená, že oddíl je plný) při ingestování dat do služby Azure Table Storage. Po rozhraní API pro tabulky se zobrazí tato zpráva. Je tato nabídka omezená a přinutil jsem, aby mě změnila existující aplikace?

Azure Cosmos DB je systém založený na smlouvě SLA, který poskytuje neomezenou škálu, se zárukami na latenci, propustnost, dostupnost a konzistenci. Abyste zajistili Zaručený výkon Premium, ujistěte se, že je velikost a index dat spravovatelná a škálovatelná. Limit 10 GB u počtu entit nebo položek na klíč oddílu je, aby bylo zajištěno, že budeme poskytovat skvělé vyhledávání a dotaz na výkon. Aby se zajistilo, že se vaše aplikace dobře škáluje, i když Azure Storage, doporučujeme, abyste nevytvořili aktivní oddíl tím, *že budete ukládat* všechny informace v jednom oddílu a dotazovat se na něj.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Takže PartitionKey a RowKey se pořád vyžadují s rozhraní API pro tabulky?

Ano. Vzhledem k tomu, že plocha oblasti rozhraní API pro tabulky je podobná sadě SDK služby Azure Table Storage, klíč oddílu poskytuje efektivní způsob, jak distribuovat data. Klíč řádku je v rámci tohoto oddílu jedinečný. Klíč řádku musí být přítomen a nemůže být null jako v rámci standardní sady SDK. Délka RowKey je 255 bajtů a délka PartitionKey je 1 KB.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Jaké jsou chybové zprávy pro rozhraní API pro tabulky?

Azure Table Storage a Azure Cosmos DB rozhraní API pro tabulky používají stejné sady SDK, takže většina chyb bude stejná.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Proč se při pokusu o vytvoření velké části tabulek jednu po druhé v rozhraní API pro tabulky vyomezovat?

Azure Cosmos DB je systém založený na smlouvě SLA, který poskytuje latenci, propustnost, dostupnost a záruky konzistence. Vzhledem k tomu, že se jedná o zřízený systém, rezervuje prostředky, aby tyto požadavky zaručoval. Je detekována rychlá míra vytváření tabulek a omezení. Doporučujeme se podívat na rychlost vytváření tabulek a snížit ji na méně než 5 za minutu. Pamatujte, že rozhraní API pro tabulky je zřízeným systémem. Okamžik, kdy ho zřídíte, začnete platit za něj.

## <a name="gremlin-api"></a>Rozhraní API pro Gremlin

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>V C#případě vývoje/.NET byste měli použít balíček Microsoft. Azure. Graphs nebo Gremlin.NET?

Rozhraní API pro Azure Cosmos DB Gremlin využívá Open Source ovladače jako hlavní konektory pro službu. Doporučenou možností je použít [ovladače, které podporuje Apache Tinkerpop](https://tinkerpop.apache.org/).

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Jak se účtují RU/s při spouštění dotazů v databázi grafu?

Všechny objekty grafu, vrcholy a hrany se v back-endu zobrazují jako dokumenty JSON. Vzhledem k tomu, že jeden dotaz Gremlin může současně upravovat jeden nebo více objektů grafu, jsou náklady spojené s ním přímo spojeny s objekty, hranami, které jsou zpracovány dotazem. Jedná se o stejný postup, který Azure Cosmos DB používá pro všechna ostatní rozhraní API. Další informace najdete v tématu [jednotky žádostí v Azure Cosmos DB](request-units.md).

Poplatek za RU je založen na pracovní sadě dat procházení, nikoli v sadě výsledků dotazu. Například pokud se dotaz pokusí získat jeden vrchol jako výsledek, ale potřebuje projít více než jeden jiný objekt na cestě, pak budou náklady založeny na všech objektech grafu, které bude trvat, aby vypočítala jeden vrchol výsledku.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Jaká je maximální škála, kterou může databáze grafu mít Azure Cosmos DB rozhraní Gremlin API?

Azure Cosmos DB využívá [horizontální dělení](partition-data.md) k automatickému zvýšení počtu požadavků na úložiště a propustnost. Maximální propustnost a kapacita úložiště úloh se určují podle počtu oddílů, které jsou přidružené k danému kontejneru. Kontejner rozhraní API Gremlin ale obsahuje specifickou sadu pokynů, které zajistí správné prostředí výkonu ve velkém měřítku. Další informace o dělení a osvědčených postupech najdete [v tématu dělení v Azure Cosmos DB](partition-data.md) článku.

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Jak můžu chránit před útoky prostřednictvím injektáže pomocí ovladačů Gremlin?

Většina nativních ovladačů Apache Tinkerpop Gremlin umožňuje, aby možnost poskytovala slovník parametrů pro provádění dotazů. Toto je příklad, jak to provést v [Gremlin.NET](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) a v [Gremlin-JavaScript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js).

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Proč se mi zobrazuje chyba kompilace dotazu Gremlin: nepovedlo se najít žádnou metodu?

Azure Cosmos DB rozhraní API Gremlin implementuje podmnožinu funkcí definovaných v oblasti povrchu Gremlin. Podporované kroky a další informace najdete v článku [Gremlin support](gremlin-support.md) .

Nejlepším řešením je přepsat požadované Gremlin kroky s podporovanými funkcemi, protože všechny základní kroky Gremlin jsou podporovány Azure Cosmos DB.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Proč se mi zobrazuje "WebSocketException: Server vrátil stavový kód" 200 ", když se očekával stavový kód" 101 "?

Tato chyba je nejspíš vyvolána, když se používá nesprávný koncový bod. Koncový bod, který generuje tuto chybu, má následující vzor:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

Toto je koncový bod dokumentů pro vaši databázi grafu.  Správný koncový bod, který se má použít, je koncový bod Gremlin, který má následující formát:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Proč se mi zobrazuje chyba "RequestRateIsTooLarge"?

Tato chyba znamená, že přidělené jednotky žádostí za sekundu nejsou dostačující pro obsluhu dotazu. K této chybě obvykle dochází, když spustíte dotaz, který získá všechny vrcholy:

```
// Query example:
g.V()
```

Tento dotaz se pokusí načíst všechny vrcholy z grafu. Proto budou náklady na tento dotaz rovny alespoň počtu vrcholů z pohledu ru. Aby bylo možné tento dotaz vyřešit, je třeba upravit nastavení RU/s.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Proč se moje připojení ovladačů Gremlin vynechává nakonec?

Připojení Gremlin se provádí prostřednictvím připojení protokolu WebSocket. I když připojení protokolu WebSocket nemají určitou dobu provozu, Azure Cosmos DB rozhraní Gremlin API ukončí nečinné připojení po 30 minutách nečinnosti.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Proč se v nativních ovladačích Gremlin nedají používat volání rozhraní Fluent API?

Rozhraní API pro Azure Cosmos DB Gremlin ještě nepodporuje volání rozhraní API Fluent. Volání rozhraní API Fluent vyžadují funkci interního formátování známou jako podpora bytového kódu, kterou aktuálně nepodporuje Azure Cosmos DB rozhraní API pro Gremlin. Z důvodu stejného důvodu se v současné době nepodporují i nejnovější ovladače Gremlin-JavaScript.

### <a name="how-can-i-evaluate-the-efficiency-of-my-gremlin-queries"></a>Jak můžu vyhodnotit efektivitu mých dotazů Gremlin?

Krok **executionProfile ()** Preview lze použít k poskytnutí analýzy plánu spouštění dotazů. Tento krok je nutné přidat na konec kteréhokoli dotazu Gremlin, jak je znázorněno v následujícím příkladu:

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

Výstup výše uvedeného profilu ukazuje, kolik času stráví získáním objektů vrcholu, objektů Edge a velikosti pracovní sady dat. To se vztahuje na měření standardních nákladů pro Azure Cosmos DB dotazy.

## <a id="cassandra"></a>rozhraní API Cassandra

### <a name="what-is-the-protocol-version-supported-by-azure-cosmso-db-cassandra-api-is-there-a-plan-to-support-other-protocols"></a>Jakou verzi protokolu podporuje služba Azure Cosmso DB rozhraní API Cassandra? Existuje plán pro podporu jiných protokolů?

Rozhraní API pro Apache Cassandra pro Azure Cosmos DB podporuje Today CQL verze 4. Pokud máte zpětnou vazbu k podpoře dalších protokolů, dejte nám vědět prostřednictvím [zpětné vazby uživatele](https://feedback.azure.com/forums/263030-azure-cosmos-db) nebo odesláním e-mailu [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Proč zvolit propustnost pro tabulku a požadavek?

Azure Cosmos DB nastaví výchozí propustnost pro váš kontejner na základě toho, kde vytvoříte tabulku z portálu nebo CQL.
Azure Cosmos DB poskytuje záruky pro výkon a latenci a horní meze provozu. Tato záruka je možná, když modul může vyhovět zásadám správného řízení operací klienta. Nastavení propustnosti zajistí, že získáte zaručenou propustnost a latenci, protože platforma rezervuje tuto kapacitu a zaručuje úspěch operace.
Můžete elasticky měnit propustnost, abyste využili sezónnost vaší aplikace a ušetřili náklady.

Koncept propustnosti je vysvětlen v tématu [jednotky žádostí v Azure Cosmos DB](request-units.md) . Propustnost pro tabulku je rovnoměrně rozložená mezi příslušné fyzické oddíly.

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>Jaká je výchozí RU/s tabulka při vytváření prostřednictvím CQL? What If je potřebuji změnit?

Azure Cosmos DB používá jednotky žádostí za sekundu (RU/s) jako měnu pro zajištění propustnosti. Tabulky vytvořené prostřednictvím CQL mají 400 RU. RU můžete změnit z portálu.

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

### <a name="what-happens-when-throughput-is-used-up"></a>Co se stane, když se propustnost využije?

Azure Cosmos DB poskytuje záruky pro výkon a latenci a horní meze provozu. Tato záruka je možná, když modul může vyhovět zásadám správného řízení operací klienta. To je možné v závislosti na nastavení propustnosti, které zajišťuje zaručenou propustnost a latenci, protože platforma rezervuje tuto kapacitu a zaručuje úspěch operace.
Po převzetí této kapacity získáte chybovou zprávu, která indikuje, že se vaše kapacita použila.
0x1001 přetížení: požadavek nejde zpracovat, protože "frekvence požadavků je velká". V tomto situaci je nutné zjistit, jaké operace a jejich svazek způsobují tyto problémy. Můžete získat představu o využité kapacitě v rámci zřízené kapacity s metrikami na portálu. Pak je potřeba zajistit, aby byla kapacita spotřebována skoro stejně ve všech podkladových oddílech. Pokud se zobrazí většina propustnosti, která je spotřebována jedním oddílem, máte k dispozici zešikmení úlohy.

K dispozici jsou metriky, které ukazují, jak se propustnost používá za hodiny, dny a za sedm dní, napříč oddíly nebo v agregaci. Další informace najdete v tématu [monitorování a ladění pomocí metrik v Azure Cosmos DB](use-metrics.md).

Diagnostické protokoly jsou vysvětleny v článku [Azure Cosmos DB diagnostické protokolování](logging.md) .

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>Mapuje primární klíč na klíč oddílu Azure Cosmos DB konceptu?

Ano, klíč oddílu se použije k umístění entity do správného umístění. V Azure Cosmos DB se používá k nalezení správného logického oddílu, který je uložený na fyzickém oddílu. Koncept dělení se dobře vysvětluje v [oddílu a měřítku v Azure Cosmos DB](partition-data.md) článku. Tady je důležité vzít v úvahu, že logický oddíl by ještě neměl dosahovat limitu 10 GB.

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>Co se stane, když získám plnou kvótu, která značí, že je oddíl plný?

Azure Cosmos DB je systém založený na smlouvě SLA, který poskytuje neomezenou škálu, se zárukami na latenci, propustnost, dostupnost a konzistenci. Toto neomezené úložiště je založené na horizontálním škálování dat pomocí dělení jako klíčové koncepty. Koncept dělení se dobře vysvětluje v [oddílu a měřítku v Azure Cosmos DB](partition-data.md) článku.

Omezení velikosti 10 GB u počtu entit nebo položek na logický oddíl, který byste měli dodržovat. Aby se zajistilo, že se vaše aplikace dobře škáluje, doporučujeme, abyste nevytvořili aktivní oddíl tím, *že budete ukládat* všechny informace v jednom oddílu a dotazovat se na něj. Tato chyba může přijít jenom v případě, že jsou vaše data nakloněná: to znamená, že máte spoustu dat pro jeden klíč oddílu (víc než 10 @ no__t-0GB). Distribuci dat můžete najít pomocí portálu úložiště. Chcete-li tuto chybu opravit, je nutné znovu vytvořit tabulku a vybrat podrobný primární (klíč oddílu), který umožňuje lepší distribuci dat.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>Je možné použít rozhraní API Cassandra jako úložiště hodnot klíčů s miliony nebo miliardami jednotlivých klíčů oddílů?

Azure Cosmos DB může ukládat neomezená data tak, že se škáluje úložiště. To je nezávislé na propustnosti. Ano, můžete vždy použít rozhraní API Cassandra k ukládání a načítání hodnot a klíčů zadáním pravého primárního/oddílu klíče. Tyto jednotlivé klíče získají svůj vlastní logický oddíl a základem fyzický oddíl bez problémů.

### <a name="is-it-possible-to-create-more-than-one-table-with-apache-cassandra-api-of-azure-cosmos-db"></a>Je možné vytvořit více než jednu tabulku s rozhraní API pro Apache Cassandra Azure Cosmos DB?

Ano, je možné vytvořit více než jednu tabulku s rozhraní API pro Apache Cassandra. Každá z těchto tabulek je považována za jednotku pro propustnost a úložiště.

### <a name="is-it-possible-to-create-more-than-one-table-in-succession"></a>Je možné vytvořit více než jednu tabulku v případě úspěchu?

Azure Cosmos DB je systém řízený prostředky pro aktivity dat i řízení. Kontejnery, jako jsou kolekce, jsou běhové entity, které jsou zřízené pro danou kapacitu propustnosti. Vytváření těchto kontejnerů v rychlém úspěchu není očekávaná aktivita a omezení. Pokud máte testy pro okamžité vyřazení nebo vytváření tabulek, zkuste je umístit na místo.

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>Jaký je maximální počet tabulek, které lze vytvořit?

Neexistuje žádný fyzický limit počtu tabulek, odeslání e-mailu na [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) , pokud máte velký počet tabulek (kde celková stálá velikost překročí 10 TB dat), která se musí vytvořit z normálního desítkách nebo stovky.

### <a name="what-is-the-maximum--of-keyspace-that-we-can-create"></a>Jaký je maximální počet míst, které můžeme vytvořit?

V případě, že se jedná o kontejnery metadat, neexistuje žádný fyzický limit počtu klíčů a pokud z nějakého důvodu máte větší počet míst, pošlete e-mail na [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>Je možné po spuštění z normální tabulky přenést spoustu dat?

Kapacita úložiště se automaticky spravuje a zvyšuje se při nabízení více dat. Takže můžete bez obav naimportovat tolik dat, kolik potřebujete, aniž byste museli spravovat a zřizovat uzly a další.

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>Je možné dodat nastavení souboru YAML a nakonfigurovat rozhraní Apache Casssandra API Azure Cosmos DB chování?

Rozhraní API pro Apache Cassandra Azure Cosmos DB je služba platformy. Poskytuje kompatibilitu na úrovni protokolu pro provádění operací. Skrývá složitost správy, monitorování a konfigurace. Jako vývojář nebo uživatel se nemusíte starat o dostupnost, neplatnou hodnotu, mezipaměť klíčů, mezipaměť řádků, filtr Bloom a množství dalších nastavení. Azure Cosmos DB se rozhraní API pro Apache Cassandra zaměřuje na poskytování výkonu čtení a zápisu, které vyžadujete bez režie konfigurace a správy.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>Bude rozhraní API pro Apache Cassandra pro Azure Cosmos DB podpora přidání uzlů/stav clusteru/stav uzlu?

Rozhraní API pro Apache Cassandra je služba platformy, která zajišťuje plánování kapacity a reaguje na požadavky na pružnost v případě propustnosti & úložiště a Breeze. Díky Azure Cosmos DB budete mít zajištěnou propustnost. Pak můžete v průběhu dne kdykoli škálovat a snížit množství, aniž byste se museli starat o přidávání a odstraňování uzlů nebo jejich správu. To znamená, že nepotřebujete, abyste používali uzel nástroje pro správu clusteru.

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>Co se stane s ohledem na různá nastavení konfigurace pro vytváření klíčů, jako je jednoduchá/síť?

Azure Cosmos DB poskytuje globální distribuci z okna pro účely dostupnosti a nízké latence. Nemusíte nastavovat repliky ani jiné věci. Všechna zápisy jsou vždy trvale kvorum potvrzené v jakékoli oblasti, do které zapisujete a zároveň zadáte záruky výkonu.

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gc_grace-compression-memtable_flush_period-and-more"></a>Co se stane s ohledem na různá nastavení metadat tabulky, jako je filtr Bloom, ukládání do mezipaměti, čtení změn oprav, gc_grace, komprese memtable_flush_period a další?

Azure Cosmos DB poskytuje výkon pro čtení, zápisy a propustnost, aniž by bylo potřeba dotýkat kteréhokoli nastavení konfigurace a náhodné manipulace s nimi.

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Je pro tabulky Cassandra podporovaná hodnota TTL (Time to Live)?

Ano, hodnota TTL je podporována.

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>Je možné monitorovat stav uzlu, stav repliky, GC a parametry operačního systému dříve pomocí různých nástrojů? Co je potřeba monitorovat hned?

Azure Cosmos DB je služba platformy, která vám pomůže zvýšit produktivitu a nedělejte si starosti se správou a monitorováním infrastruktury. Stačí jenom zajistit propustnost, která je k dispozici na metrikách portálu, abyste zjistili, jestli se vám omezila a zvýšila nebo snížila tato propustnost.
Monitorujte [SLA](monitor-accounts.md).
Používejte [metriky](use-metrics.md) , které používají [diagnostické protokoly](logging.md).

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Které klientské sady SDK můžou pracovat s rozhraní API pro Apache Cassandra Azure Cosmos DB?

Ovladače klienta sady Apache Cassandra SDK, které používají CQLv3, byly použity pro klientské programy. Pokud máte jiné ovladače, které používáte, nebo pokud máte potíže, odešlete e-mail na [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="is-composite-partition-key-supported"></a>Je podporován klíč složeného oddílu?

Ano, pomocí regulární syntaxe můžete vytvořit složený klíč oddílu.

### <a name="can-i-use-sstableloader-for-data-loading"></a>Můžu použít sstableloader k načítání dat?

Ne, sstableloader se nepodporuje.

### <a name="can-an-on-premises-apache-cassandra-cluster-be-paired-with-azure-cosmos-dbs-cassandra-api"></a>Je možné místní cluster Apache Cassandra spárovat s rozhraní API Cassandraem Azure Cosmos DB?

V současnosti Azure Cosmos DB má optimalizované prostředí pro cloudové prostředí bez režie provozu. Pokud požadujete párování, pošlete e-mail na [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) s popisem vašeho scénáře. Pracujeme na tom, abychom pomohli spárovat místní nebo jiný cloudový cluster Cassandra s rozhraní API Cassandraem Cosomos DB.

### <a name="does-cassandra-api-provide-full-backups"></a>Poskytuje rozhraní API Cassandra úplné zálohy?

Azure Cosmos DB poskytuje dvě bezplatné zálohy, které jsou v současnosti v celém rozhraní API pořízeny v intervalu čtyř hodin. To zajistí, že nebudete muset nastavovat plán zálohování a další věci.
Pokud chcete upravit dobu uchovávání a četnosti, pošlete e-mail na [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) nebo zvyšte případ podpory. Informace o možnostech zálohování najdete v článku [automatické online zálohování a obnovení pomocí Azure Cosmos DB](online-backup-and-restore.md) .

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Jak účet rozhraní API Cassandra zpracovává převzetí služeb při selhání, pokud dojde k výpadku oblasti?

Azure Cosmos DB rozhraní API Cassandra vypůjčovatela z globálně distribuované platformy Azure Cosmos DB. Aby bylo zajištěno, že vaše aplikace může tolerovat výpadky datového centra, povolte alespoň jednu oblast pro účet na portálu Azure Cosmos DB, [vývoj s využitím Azure Cosmos DBch účtů ve více oblastech](high-availability.md). Prioritu oblasti můžete nastavit pomocí portálu, který [se vyvíjí s Azure Cosmos DBmi účty ve více oblastech](high-availability.md).

Pro účet můžete přidat tolik oblastí, kolik chcete, a určit, kde při převzetí služeb při selhání bude možné převzít služby při selhání. Chcete-li použít databázi, je nutné zadat také aplikaci. Když to uděláte, vaši zákazníci nebudou mít žádný výpadek.

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Má rozhraní API pro Apache Cassandra index všechny atributy entity ve výchozím nastavení?

Rozhraní API Cassandra plánuje podporovat sekundární indexování, aby usnadnila vytváření selektivního indexu u určitých atributů. 


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Můžu novou rozhraní API Cassandra sadu SDK použít lokálně s emulátorem?

Ano, to je podporováno.

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-change-feed-and-other-functionality-will-these-capabilities-be-added-to-the-cassandra-api"></a>Azure Cosmos DB jako platforma se jeví mít spoustu možností, jako je například změna kanálu a další funkce. Budou tyto možnosti přidány do rozhraní API Cassandra?

Rozhraní API pro Apache Cassandra poskytuje stejnou funkci CQL jako Apache Cassandra. Plánujeme vyhlížet na proveditelnost podpory různých funkcí v budoucnu.

### <a name="feature-x-of-regular-cassandra-api-isnt-working-as-today-where-can-the-feedback-be-provided"></a>Funkce x běžných rozhraní API Cassandra nefunguje jako dnes, kde může být poskytnuta zpětná vazba?

Poskytněte zpětnou vazbu pomocí [hlasu pro uživatele](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md
