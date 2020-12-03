---
title: Nejčastější dotazy k různým rozhraním API v Azure Cosmos DB
description: Získejte odpovědi na nejčastější dotazy týkající se Azure Cosmos DB globálně distribuované databázové služby pro více modelů. Přečtěte si o kapacitě, úrovních výkonu a škálování.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 9e0c5ae119b734f91e8cfa1e7afb11a96e13035c
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96549068"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Nejčastější dotazy k různým rozhraním API ve službě Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Jaké jsou typické případy použití pro Azure Cosmos DB?

Azure Cosmos DB je vhodná volba pro nové webové, mobilní a herní aplikace a aplikace IoT, kde je důležité automatické škálování, předvídatelný výkon, rychlé řazení dob odezvy milisekund a možnost dotazování na data bez schémat. Azure Cosmos DB sám o sobě poskytovat rychlý vývoj a podporu průběžné iterace datových modelů aplikací. Aplikace, které spravují uživatelem generovaný obsah a data, jsou [běžné případy použití pro Azure Cosmos DB](use-cases.md).

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Jak Azure Cosmos DB nabízí předvídatelný výkon?

[Jednotka žádosti](request-units.md) (ru) je mírou propustnosti v Azure Cosmos DB. Propustnost 1RU odpovídá propustnosti získání dokumentu o 1 KB. Každá operace v Azure Cosmos DB, včetně čtení, zápisu, dotazů SQL a spuštění uložených procedur, má deterministické RU, který je založen na propustnosti potřebné k dokončení operace. Místo toho, abyste se přemysleli o procesoru, vstupně-výstupních operacích a paměti a o tom, jak mají vliv na propustnost vaší aplikace, si můžete představit v souvislosti s jedním mírou RU.

U každého kontejneru Azure Cosmos můžete nakonfigurovat zřízenou propustnost v podobě ru propustnosti za sekundu. U aplikací v jakémkoli měřítku můžete určit srovnávací testy jednotlivých požadavků a zajistit, aby se v rámci všech požadavků vytvořil kontejner pro zpracování celkových jednotek požadavků. Propustnost kontejneru můžete také škálovat směrem nahoru nebo dolů podle potřeb vaší aplikace. Pokud chcete získat další informace o jednotkách žádostí a nápovědu k určení potřeb kontejneru, zkuste použít [kalkulačku propustnosti](https://www.documentdb.com/capacityplanner).

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Jak Azure Cosmos DB podporuje různé datové modely, jako je klíč/hodnota, sloupcový, dokument a graf?

Datové modely klíč/hodnota (tabulka), sloupcové, dokumentové a graf jsou nativně podporovány v důsledku návrhu ARS (atoms, Records a sequences), na který Azure Cosmos DB je postaven. Atomy, záznamy a sekvence je možné snadno namapovat a promítnout na různé datové modely. Rozhraní API pro podmnožinu modelů jsou nyní k dispozici (SQL, MongoDB, Table a Gremlin) a další specifické pro další datové modely budou v budoucnu k dispozici.

Azure Cosmos DB má modul indexování nezávislá schématu, který umožňuje automaticky indexovat všechna data, která ingestuje, bez nutnosti jakéhokoli schématu nebo sekundárních indexů od vývojáře. Tento modul spoléhá na sadu rozložení logických indexů (obrácený, sloupcový, strom), která odděluje rozložení úložiště od subsystémů zpracování dotazů a indexů. Cosmos DB má také možnost podporovat sadu přenosových protokolů a rozhraní API rozšiřitelným způsobem a efektivně je přeložit na základní datový model (1) a na rozložení logických indexů (2), což umožňuje jednoznačně podporovat více než jeden datový model nativně.

### <a name="can-i-use-multiple-apis-to-access-my-data"></a>Můžu pro přístup k datům použít více rozhraní API?

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Kde multi-model znamená Azure Cosmos DB podporuje více rozhraní API a více datových modelů, používají různá rozhraní API pro úložiště a síťový protokol různé formáty dat. Například SQL používá JSON, MongoDB používá BSON, tabulka používá EDM, Cassandra používá CQL, Gremlin používá formát JSON. V důsledku toho doporučujeme použít stejné rozhraní API pro veškerý přístup k datům v daném účtu.

Každé rozhraní API funguje nezávisle, s výjimkou Gremlin a SQL API, které jsou interoperabilní.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Je Azure Cosmos DB HIPAA kompatibilní?

Ano, Azure Cosmos DB je kompatibilní s HIPAA. HIPAA zavádí požadavky na použití, zveřejnění a ochranu jednotlivě identifikovatelných zdravotních informací. Další informace najdete na [webu Microsoft Trust Center](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

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

[Emulátor Azure Cosmos DB](local-emulator.md) můžete také použít k místnímu vývoji a testování vaší aplikace bez nutnosti vytvářet předplatné Azure. Jakmile budete spokojeni s fungováním aplikace v emulátoru, můžete přejít na účet služby Azure Cosmos DB v cloudu.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Jak můžu získat další pomoc s Azure Cosmos DB?

Pokud se chcete zeptat na technický dotaz, můžete publikovat na jedno z těchto dvou fór otázek a odpovědí:

* [Stránka s otázkou Microsoft Q&](/answers/topics/azure-cosmos-db.html)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow je nejvhodnější pro otázky týkající se programování. Ujistěte se, že je váš dotaz [v rámci vašeho tématu](https://stackoverflow.com/help/on-topic) [, a uveďte co nejvíce podrobností a vymažte otázku a odpovězte na](https://stackoverflow.com/help/how-to-ask)ně.

Pokud chcete požádat o nové funkce, vytvořte nový požadavek na [uživatelský hlas](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Pokud řešíte problém s účtem, podejte prostřednictvím webu Azure Portal [žádost o podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="try-azure-cosmos-db-subscriptions"></a><a id="try-cosmos-db"></a>Vyzkoušet Azure Cosmos DB předplatná

Nyní můžete využívat časově omezené prostředí Azure Cosmos DB bez předplatného, zdarma a závazky. Pokud se chcete zaregistrovat k předplatnému vyzkoušet Azure Cosmos DB, pokračujte [Azure Cosmos DB vyzkoušet si zdarma](https://azure.microsoft.com/try/cosmosdb/) a použijte libovolné osobní účet Microsoft (MSA). Toto předplatné je nezávislé na [bezplatné zkušební verzi Azure](https://azure.microsoft.com/free/)a dá se použít společně s bezplatnou zkušební verzí Azure nebo s placeným předplatným Azure.

Vyzkoušejte Azure Cosmos DB odběry se zobrazí v Azure Portal další předplatná přidružená k vašemu ID uživatele.

Následující podmínky se vztahují na zkoušení Azure Cosmos DB předplatných:

* Přístup k účtu se dá udělit pro osobní účty Microsoft (MSA). Nepoužívejte účty nebo účty Azure Active Directory (Azure AD) patřící k podnikovým klientům Azure AD, ale můžou mít omezení, která by mohla blokovat udělení přístupu.
* Jeden [kontejner zřízený propustností](./set-throughput.md#set-throughput-on-a-container) na předplatné pro SQL, Gremlin API a účty tabulek.
* Až tři [zajištěné propustnosti](./set-throughput.md#set-throughput-on-a-container) v rámci předplatného pro účty MongoDB.
* Jedna [zajištěná propustnost databáze](./set-throughput.md#set-throughput-on-a-database) na jedno předplatné. Databáze zřízené v propustnosti můžou obsahovat libovolný počet kontejnerů.
* kapacita úložiště o velikosti 10 GB.
* Globální replikace je dostupná v následujících [oblastech Azure](https://azure.microsoft.com/regions/): Střed USA, Severní Evropa a jihovýchodní Asie.
* Maximální propustnost 5 000 RU/s v případě zřízení na úrovni kontejneru.
* Maximální propustnost 20 000 RU/s, pokud je zajištěna na úrovni databáze.
* Platnost předplatných vyprší po 30 dnech a dají se prodloužit na maximum 31 dní. Po vypršení platnosti se obsažené informace odstraní.
* Pro účty try Azure Cosmos DB se nedají vytvořit lístky podpory Azure; Nicméně podpora je poskytována pro předplatitele se stávajícími plány podpory.

## <a name="set-up-azure-cosmos-db"></a>Nastavit Azure Cosmos DB

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Návody se zaregistrovat Azure Cosmos DB?

Azure Cosmos DB je k dispozici v Azure Portal. Nejdřív si zaregistrujte předplatné Azure. Po registraci můžete do svého předplatného Azure přidat účet Azure Cosmos DB.

### <a name="what-is-a-primary-key"></a>Co je primární klíč?

Primární klíč je token zabezpečení pro přístup ke všem prostředkům pro účet. Jednotlivci s klíčem mají přístup pro čtení a zápis ke všem prostředkům v databázovém účtu. Při distribuci primárních klíčů buďte opatrní. Primární primární klíč a sekundární primární klíč jsou k dispozici v okně **klíče** [Azure Portal][azure-portal]. Další informace o klíčích najdete v tématu [Zobrazení, kopírování a obnovení přístupových klíčů](manage-with-cli.md#list-account-keys).

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

## <a name="frequently-asked-questions-about-sql-api"></a><a id="sql-api-faq"></a>Nejčastější dotazy k rozhraní SQL API

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Návody začít vyvíjet s rozhraním SQL API?

Nejdřív si musíte zaregistrovat předplatné Azure. Po registraci předplatného Azure můžete k předplatnému Azure přidat kontejner rozhraní SQL API. Pokyny k přidání Azure Cosmos DB účtu najdete v tématu [Vytvoření účtu databáze Azure Cosmos](create-sql-api-dotnet.md#create-account).

Pro .NET, Python, Node.js, JavaScript a Javu jsou k dispozici sady [SDK](sql-api-sdk-dotnet.md). Vývojáři můžou také používat [rozhraní RESTFUL HTTP API](/rest/api/cosmos-db/) k interakci s Azure Cosmos DBmi prostředky z různých platforem a jazyků.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Můžu získat přístup k některým předem připraveným ukázkám, aby bylo možné začít?

Ukázky pro sady SDK rozhraní SQL API [.NET](sql-api-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](sql-api-nodejs-samples.md)a [Python](sql-api-python-samples.md) jsou k dispozici na GitHubu.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>Podporuje databáze rozhraní SQL API data bez schémat?

Ano, rozhraní SQL API umožňuje aplikacím ukládat libovolné dokumenty JSON bez definic schémat nebo tipů. Data jsou okamžitě k dispozici pro dotazování prostřednictvím rozhraní Azure Cosmos DB SQL Query.

### <a name="does-the-sql-api-support-acid-transactions"></a>Podporuje rozhraní SQL API transakce s KYSELINou?

Ano, rozhraní SQL API podporuje transakce mezi dokumenty vyjádřené jako procedury a triggery uložené v JavaScriptu. Transakce jsou vymezeny na jeden oddíl v rámci každého kontejneru a jsou spouštěny s sémantikou kyseliny jako "vše nebo nic" izolované od ostatních souběžně prováděných požadavků kódu a uživatelů. Pokud jsou výjimky vyvolány prostřednictvím spuštění kódu aplikace JavaScriptu na straně serveru, celá transakce je vrácena zpět. 

### <a name="what-is-a-container"></a>Co je kontejner?

Kontejner je skupina dokumentů a jejich přidružená logika aplikace JavaScriptu. Kontejner je fakturovatelná entita, kde [náklady](performance-levels.md) určují propustnost a využité úložiště. Kontejnery můžou zahrnovat jeden nebo víc oddílů nebo serverů a můžou se škálovat na zpracování prakticky neomezených objemů úložiště nebo propustnosti.

* Pro rozhraní SQL API se prostředek nazývá kontejner.
* Pro Cosmos DB rozhraní API pro účty MongoDB se kontejner mapuje na kolekci.
* Pro účty Cassandra a rozhraní API pro tabulky se kontejner mapuje na tabulku.
* V případě účtů rozhraní Gremlin API se kontejner mapuje do grafu.

Kontejnery jsou také fakturačními entitami pro Azure Cosmos DB. Každý kontejner se účtuje po hodinách na základě zřízené propustnosti a využitého prostoru úložiště. Další informace najdete v tématu [Azure Cosmos DB ceny](https://azure.microsoft.com/pricing/details/cosmos-db/).

### <a name="how-do-i-create-a-database"></a>Jak vytvořím databázi?

Databáze můžete vytvářet pomocí [Azure Portal](https://portal.azure.com), jak je popsáno v tématu [Přidání kontejneru](create-sql-api-java.md#add-a-container), jedna ze sady [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)nebo [rozhraní REST API](/rest/api/cosmos-db/).

### <a name="how-do-i-set-up-users-and-permissions"></a>Jak nastavím uživatele a oprávnění?

Uživatele a oprávnění můžete vytvořit pomocí jedné ze sady [sdk Cosmos DB API](sql-api-sdk-dotnet.md) nebo rozhraní [REST API](/rest/api/cosmos-db/).

### <a name="does-the-sql-api-support-sql"></a>Podporuje rozhraní SQL API SQL?

Dotazovací jazyk SQL podporovaný účty SQL API je rozšířená podmnožina funkcí dotazů, které podporuje SQL Server. Azure Cosmos DB dotazovací jazyk SQL poskytuje bohatý hierarchické a relační operátory a rozšiřitelnost prostřednictvím uživatelsky definovaných funkcí (UDF) založených na JavaScriptu. Gramatika JSON umožňuje modelování dokumentů JSON jako stromů s označenými uzly, které jsou používány postupy Azure Cosmos DB automatické indexování a dialektem dotazu SQL Azure Cosmos DB. Informace o použití gramatiky SQL najdete v článku [dotaz SQL][query] .

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>Podporuje rozhraní SQL API agregační funkce SQL?

Rozhraní SQL API podporuje agregaci s nízkou latencí v jakémkoli měřítku prostřednictvím agregačních funkcí `COUNT` , `MIN` ,, `MAX` `AVG` a `SUM` prostřednictvím gramatiky SQL. Další informace najdete v tématu [agregační funkce](sql-query-aggregate-functions.md).

### <a name="how-does-the-sql-api-provide-concurrency"></a>Jak rozhraní API SQL zajišťuje souběžnost?

Rozhraní SQL API podporuje optimistické řízení souběžnosti (OCC) prostřednictvím značek entit HTTP nebo značek ETag. Každý prostředek rozhraní API SQL má ETag a značka ETag je nastavená na serveru pokaždé, když se dokument aktualizuje. Hlavička ETag a aktuální hodnota jsou zahrnuty ve všech zprávách s odpovědí. Značky ETag lze použít s hlavičkou If-Match, aby bylo možné serveru rozhodnout, zda má být prostředek aktualizován. Hodnota If-Match je hodnota ETag, která se má zkontrolovat. Pokud hodnota ETag odpovídá hodnotě ETag serveru, prostředek se aktualizuje. Pokud značka ETag již není aktuální, server odmítne operaci s kódem odpovědi "selhání předběžné podmínky HTTP 412". Klient pak znovu načte prostředek, aby získal aktuální hodnotu ETag pro daný prostředek. Značky ETag lze navíc použít s hlavičkou If-None-Match k určení, zda je nutné znovu načíst prostředek.

Chcete-li použít optimistickou souběžnost v rozhraní .NET, použijte třídu [AccessCondition](/dotnet/api/microsoft.azure.documents.client.accesscondition) . Ukázku .NET najdete v tématu [program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) v ukázce DocumentManagement na GitHubu.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Návody provádět transakce v rozhraní SQL API?

Rozhraní SQL API podporuje transakce integrované s jazykem prostřednictvím procedur a triggerů uložených v JavaScriptu. Všechny databázové operace uvnitř skriptů jsou spouštěny při izolaci snímku. Pokud se jedná o kontejner s jedním oddílem, je spuštění vymezeno na kontejner. Pokud je kontejner rozdělený, je spuštění vymezeno na dokumenty se stejnou hodnotou klíče oddílu v rámci kontejneru. Na začátku transakce se pořídí snímek verzí dokumentů (ETagy) a k potvrzení dojde pouze v případě, že skript uspěje. Pokud JavaScript vyvolá chybu, transakce se vrátí zpět. Další informace najdete v tématu [programování JavaScriptu na straně serveru pro Azure Cosmos DB](stored-procedures-triggers-udfs.md).

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

[azure-portal]: https://portal.azure.com
[query]: ./sql-query-getting-started.md

## <a name="next-steps"></a>Další kroky

Další informace o nejčastějších dotazech v jiných rozhraních API najdete v těchto tématech:

* Nejčastější dotazy týkající se [rozhraní API Azure Cosmos DB pro MongoDB](mongodb-api-faq.md)
* Nejčastější dotazy týkající [se rozhraní Gremlin API v Azure Cosmos DB](gremlin-api-faq.md)
* Nejčastější dotazy týkající [se rozhraní API Cassandra v Azure Cosmos DB](cassandra-faq.md)
* Nejčastější dotazy týkající [se rozhraní API pro tabulky v Azure Cosmos DB](table-api-faq.md)