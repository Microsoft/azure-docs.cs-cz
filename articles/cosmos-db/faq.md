---
title: Nejčastější dotazy týkající se různých api v Azure Cosmos DB
description: Získejte odpovědi na nejčastější dotazy týkající se Azure Cosmos DB, globálně distribuované databázové služby s více modely. Seznamte se s kapacitou, úrovní výkonu a škálováním.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 25b669493e1ee32df4a8c6fc33bdb1b21b08c70f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683330"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Nejčastější dotazy k různým rozhraním API ve službě Azure Cosmos DB

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Jaké jsou typické případy použití pro Azure Cosmos DB?

Azure Cosmos DB je dobrou volbou pro nové webové, mobilní, herní a IoT aplikace, kde je důležité automatické škálování, předvídatelný výkon, rychlé pořadí milisekundových dob odezvy a možnost dotazování na data bez schématu. Azure Cosmos DB se hodí k rychlému vývoji a podpoře průběžné iterace aplikačních datových modelů. Aplikace, které spravují obsah a data generovaná uživateli, jsou [běžné případy použití pro Azure Cosmos DB](use-cases.md).

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Jak Azure Cosmos DB nabízí předvídatelný výkon?

[Jednotka požadavku](request-units.md) (RU) je míra propustnostvosti v Azure Cosmos DB. Propustnost 1RU odpovídá propustnost GET dokumentu 1 KB. Každá operace v Azure Cosmos DB, včetně čtení, zápisy, SQL dotazy a uložené spuštění procedur, má deterministické RU hodnotu, která je založena na propustnosti potřebné k dokončení operace. Místo přemýšlení o procesoru, vi a paměti a jak každý z nich ovlivňují propustnost aplikace, můžete myslet z hlediska jednoho ru opatření.

Každý kontejner Azure Cosmos můžete nakonfigurovat s zřízenou propustností z hlediska ru propustnost za sekundu. Pro aplikace libovolného rozsahu můžete porovnat jednotlivé požadavky na měření jejich hodnot Ru a zřídit kontejner pro zpracování celkového počtu jednotek požadavků ve všech požadavcích. Můžete také vertikálně navýšit nebo vertikálně navýšit propustnost vašeho kontejneru podle vývoje potřeb vaší aplikace. Další informace o jednotkách požadavků a pomoc s určením potřeb kontejneru získáte [v kalkulátoru propustnosti](https://www.documentdb.com/capacityplanner).

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Jak Azure Cosmos DB podporuje různé datové modely, jako je klíč/hodnota, sloupcové, dokument a graf?

Klíč/hodnota (tabulka), sloupcové, dokument a graf datové modely jsou nativně podporovány z důvodu ars (atomy, záznamy a sekvence) návrh, který Je postaven na Azure Cosmos DB. Atomy, záznamy a sekvence lze snadno mapovat a promítat do různých datových modelů. Rozhraní API pro podmnožinu modelů jsou k dispozici právě teď (SQL, MongoDB, Table a Gremlin) a další specifické pro další datové modely budou k dispozici v budoucnu.

Azure Cosmos DB má modul indexování agnostik schématu schopné automaticky indexovat všechna data, která ingestuje bez nutnosti jakékoli schéma nebo sekundární indexy od vývojáře. Modul spoléhá na sadu rozložení logického indexu (invertované, sloupcové, strom), které odpojí rozložení úložiště od podsystémů zpracování indexu a dotazů. Cosmos DB má také schopnost podporovat sadu drátových protokolů a rozhraní API rozšiřitelným způsobem a efektivně je překládat do základního datového modelu (1) a rozložení logického indexu (2), takže je jedinečně schopen podporovat více než jeden datový model nativně.

### <a name="can-i-use-multiple-apis-to-access-my-data"></a>Můžu k přístupu ke svým datům použít více api?

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu s více modely. Kde vícemodelové prostředky Azure Cosmos DB podporuje více api a více datových modelů, různá api používají různé formáty dat pro úložiště a drátový protokol. Například SQL používá JSON, MongoDB používá BSON, Tabulka používá EDM, Cassandra používá CQL, Gremlin používá formát JSON. V důsledku toho doporučujeme používat stejné rozhraní API pro veškerý přístup k datům v daném účtu.

Každé rozhraní API pracuje nezávisle, s výjimkou Gremlin a SQL API, které jsou interoperabilní.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Je kompatibilní s Azure Cosmos DB HIPAA?

Ano, Azure Cosmos DB je kompatibilní s HIPAA. HIPAA zavádí požadavky na použití, zveřejnění a ochranu jednotlivě identifikovatelných zdravotních informací. Další informace naleznete v [centru zabezpečení společnosti Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Jaké jsou limity úložiště Azure Cosmos DB?

Neexistuje žádné omezení na celkové množství dat, které kontejner můžete uložit v Azure Cosmos DB.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Jaké jsou limity propustnosti Azure Cosmos DB?

Neexistuje žádné omezení na celkové množství propustnost, které kontejner může podporovat v Azure Cosmos DB. Klíčovou myšlenkou je distribuovat vaše úlohy zhruba rovnoměrně mezi dostatečně velký počet klíčů oddílů.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>Jsou režimy přímého připojení a připojení brány šifrovány?

Ano, oba režimy jsou vždy plně šifrovány.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Kolik stojí Azure Cosmos DB?

Podrobnosti najdete na stránce [s podrobnostmi o cenách Azure Cosmos DB.](https://azure.microsoft.com/pricing/details/cosmos-db/) Poplatky za využití Azure Cosmos DB jsou určeny počtem zřízených kontejnerů, počtem hodin, po které byly kontejnery online, a zřízenou propustností pro každý kontejner.

### <a name="is-a-free-account-available"></a>Je k dispozici bezplatný účet?

Ano, můžete se zaregistrovat na časově omezený účet zdarma, bez závazku. Pokud se chcete zaregistrovat, navštivte [zdarma try Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) nebo si přečtěte další informace v [nejčastějších dotazech k try Azure Cosmos DB](#try-cosmos-db).

Pokud s Azure tečeme, můžete si zaregistrovat [bezplatný účet Azure](https://azure.microsoft.com/free/), který vám poskytne 30 dní a kredit na vyzkoušení všech služeb Azure. Pokud máte předplatné Visual Studia, máte taky nárok na [bezplatné kredity Azure,](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) které můžete použít na libovolné službě Azure.

[Emulátor Azure Cosmos DB](local-emulator.md) můžete taky použít k vývoji a testování vaší aplikace místně zdarma, bez vytvoření předplatného Azure. Jakmile budete spokojeni s fungováním aplikace v emulátoru, můžete přejít na účet služby Azure Cosmos DB v cloudu.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Jak můžu získat další pomoc s Azure Cosmos DB?

Chcete-li položit technickou otázku, můžete odeslat na jedno z těchto dvou fór otázek a odpovědí:

* [Fórum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Přetečení zásobníku](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Přetečení zásobníku je nejlepší pro programování otázek. Ujistěte se, že vaše otázka je [on-topic](https://stackoverflow.com/help/on-topic) a [poskytnout co nejvíce podrobností, jak je to možné, takže otázka jasná a odpovědětelná](https://stackoverflow.com/help/how-to-ask).

Chcete-li požádat o nové funkce, vytvořte nový požadavek na [hlas uživatele](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Pokud řešíte problém s účtem, podejte prostřednictvím webu Azure Portal [žádost o podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="try-azure-cosmos-db-subscriptions"></a><a id="try-cosmos-db"></a>Vyzkoušejte předplatná Azure Cosmos DB

Teď si můžete vychutnat časově omezené prostředí Azure Cosmos DB bez předplatného, zdarma a závazků. Pokud si chcete zaregistrovat předplatné Try Azure Cosmos DB, přejděte zdarma na [Vyzkoušejte Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) a použijte libovolný osobní účet Microsoft (MSA). Toto předplatné je oddělené od [bezplatné zkušební verze Azure](https://azure.microsoft.com/free/)a dá se používat spolu s bezplatnou zkušební verzí Azure nebo placeným předplatným Azure.

Vyzkoušejte předplatná Azure Cosmos DB, která se zobrazí na webu Azure Portal, další další předplatná přidružená k vašemu ID uživatele.

Následující podmínky platí pro vyzkoušení předplatného Azure Cosmos DB:

* Přístup k účtu lze udělit osobním účtům Microsoft (MSA). Vyhněte se použití účtů služby Active Directory (AAD) nebo účtů patřících k podnikovým klientům AAD, mohou mít zavedena omezení, která by mohla blokovat udělování přístupu.
* Jeden [propustný kontejner zřízený](./set-throughput.md#set-throughput-on-a-container) na předplatné pro účty SQL, Gremlin API a table.
* Až tři [kolekce zřízená propustnost na](./set-throughput.md#set-throughput-on-a-container) předplatné pro účty MongoDB.
* Jedna [propustnost zřízená databáze](./set-throughput.md#set-throughput-on-a-database) na předplatné. Databáze zřízené propustnostmi mohou obsahovat libovolný počet kontejnerů uvnitř.
* 10 GB úložné kapacity.
* Globální replikace je dostupná v [následujících oblastech Azure:](https://azure.microsoft.com/regions/)Střední USA, Severní Evropa a Jihovýchodní Asie.
* Maximální propustnost 5 K RU/s při zřizování na úrovni kontejneru.
* Maximální propustnost 20 K RU/s při zřízení na úrovni databáze.
* Platnost předplatného vyprší po 30 dnech a lze je prodloužit na maximálně 31 dní.
* Lístky podpory Azure nelze vytvořit pro účty Try Azure Cosmos DB; podpora je však k dispozici pro předplatitele s existující plány podpory.

## <a name="set-up-azure-cosmos-db"></a>Nastavení Azure Cosmos DB

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Jak se přihlásím do Služby Azure Cosmos DB?

Azure Cosmos DB je k dispozici na webu Azure Portal. Nejprve si zaregistrujte předplatné Azure. Po registraci můžete do předplatného Azure přidat účet Azure Cosmos DB.

### <a name="what-is-a-master-key"></a>Co je hlavní klíč?

Hlavní klíč je token zabezpečení pro přístup ke všem prostředkům účtu. Jednotlivci s klíčem mají přístup ke všem prostředkům v databázovém účtu a mají přístup ke všem prostředkům. Při distribuci hlavních klíčů buďte opatrní. Primární hlavní klíč a sekundární hlavní klíč jsou k dispozici v okně **Klíče** [na webu Azure Portal][azure-portal]. Další informace o klíčích najdete v tématu [Zobrazení, kopírování a obnovení přístupových klíčů](manage-with-cli.md#list-account-keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Jaké jsou oblasti, které PreferredLocations lze nastavit?

PreferredLocations hodnotu můžete nastavit na některou z oblastí Azure, ve kterém cosmos DB je k dispozici. Seznam dostupných oblastí najdete v [tématu Oblasti Azure](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Je něco, co bych měl vědět při distribuci dat po celém světě prostřednictvím datových center Azure?

Azure Cosmos DB se kdispozici ve všech oblastech Azure, jak je určeno na stránce [oblasti Azure.](https://azure.microsoft.com/regions/) Protože se jedná o základní službu, každé nové datové centrum má přítomnost Azure Cosmos DB.

Když nastavíte oblast, nezapomeňte, že Azure Cosmos DB respektuje suverénní a vládní cloudy. To znamená, že pokud vytvoříte účet v [suverénní oblasti](https://azure.microsoft.com/global-infrastructure/), nelze replikovat z této [suverénní oblasti](https://azure.microsoft.com/global-infrastructure/). Podobně nelze povolit replikaci do jiných suverénních umístění z externího účtu.

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>Je možné přepnout z propustnosti na úrovni kontejneru na zřizování propustnosti na úrovni databáze? Nebo naopak

Zřizování propustností na úrovni kontejnerů a databází jsou samostatné nabídky a přepínání mezi některou z nich vyžaduje migraci dat ze zdroje do cíle. To znamená, že musíte vytvořit novou databázi nebo nový kontejner a pak migrovat data pomocí [knihovny hromadného prováděcího modulu](bulk-executor-overview.md) nebo [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>Podporuje Azure CosmosDB analýzu časových řad?

Ano, Azure CosmosDB podporuje analýzu časových řad, tady je ukázka pro [vzorek časových řad](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns). Tato ukázka ukazuje, jak pomocí informačního kanálu o změnách vytvářet agregovaná zobrazení v průběhu dat časových řad. Tento přístup můžete rozšířit pomocí streamování jiskry nebo jiného datového procesoru datového proudu.

## <a name="what-are-the-azure-cosmos-db-service-quotas-and-throughput-limits"></a>Jaké jsou kvóty služeb Azure Cosmos DB a omezení propustnost

Další informace najdete v [tématu kvóty služeb](concepts-limits.md) Azure Cosmos DB a [v rámci limitů na kontejner a databázové](set-throughput.md#comparison-of-models) články.

## <a name="sql-api"></a>SQL API

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Jak se začnu vyvíjet v rozhraní SQL API?

Nejprve se musíte zaregistrovat k předplatnému Azure. Jakmile se zaregistrujete k předplatnému Azure, můžete do předplatného Azure přidat kontejner ROZHRANÍ SQL API. Pokyny k přidání účtu Azure Cosmos DB najdete [v tématu Vytvoření databázového účtu Azure Cosmos](create-sql-api-dotnet.md#create-account).

Pro .NET, Python, Node.js, JavaScript a Javu jsou k dispozici sady [SDK](sql-api-sdk-dotnet.md). Vývojáři můžou také použít [http api RESTful](/rest/api/cosmos-db/) k interakci s prostředky Azure Cosmos DB z různých platforem a jazyků.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Mohu získat přístup k připraveným vzorkům, abych získal náskok?

Ukázky pro SQL API [.NET](sql-api-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](sql-api-nodejs-samples.md)a [Python](sql-api-python-samples.md) SDK jsou k dispozici na GitHubu.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>Podporuje databáze rozhraní SQL API data bez schématu?

Ano, rozhraní SQL API umožňuje aplikacím ukládat libovolné dokumenty JSON bez definic schématu nebo rad. Data jsou okamžitě k dispozici pro dotaz prostřednictvím rozhraní dotazu Azure Cosmos DB SQL.

### <a name="does-the-sql-api-support-acid-transactions"></a>Podporuje rozhraní SQL API transakce ACID?

Ano, rozhraní SQL API podporuje transakce mezi dokumenty vyjádřené jako procedury a aktivační události uložené v jazyce JavaScript. Transakce jsou vymezeny na jeden oddíl v rámci každého kontejneru a provedeny s acid sémantiku jako "vše nebo nic", izolované od jiných souběžně vykonávající kód a požadavky uživatelů. Pokud jsou výjimky vyvolány prostřednictvím spuštění kódu aplikace JavaScript na straně serveru, je celá transakce vrácena zpět. 

### <a name="what-is-a-container"></a>Co je kontejner?

Kontejner je skupina dokumentů a jejich přidružené aplikační logiky JavaScriptu. Kontejner je fakturovatelná entita, kde [jsou náklady](performance-levels.md) určeny propustností a použitým úložištěm. Kontejnery mohou span jeden nebo více oddílů nebo serverů a škálovat pro zpracování prakticky neomezené svazky úložiště nebo propustnost.

* Pro rozhraní SQL API kontejner mapuje kontejneru.
* Pro rozhraní API Cosmos DB pro účty MongoDB kontejner mapuje na kolekci.
* Pro účty Cassandra a Table API kontejner mapuje na tabulka.
* Pro účty Gremlin API kontejner mapuje na graf.

Kontejnery jsou také fakturační entity pro Azure Cosmos DB. Každý kontejner se účtuje každou hodinu na základě zřízené propustnosti a využitého úložného prostoru. Další informace najdete v tématu [Azure Cosmos DB Pricing](https://azure.microsoft.com/pricing/details/cosmos-db/).

### <a name="how-do-i-create-a-database"></a>Jak vytvořím databázi?

Databáze můžete vytvářet pomocí [portálu Azure](https://portal.azure.com), jak je popsáno v [části Přidat kontejner](create-sql-api-java.md#add-a-container), jednu z sad Azure [Cosmos DB SDK](sql-api-sdk-dotnet.md)nebo [REST API](/rest/api/cosmos-db/).

### <a name="how-do-i-set-up-users-and-permissions"></a>Jak nastavím uživatele a oprávnění?

Můžete vytvořit uživatele a oprávnění pomocí jednoho z [kosmos DB API Sady SDK](sql-api-sdk-dotnet.md) nebo [rozhraní REST API](/rest/api/cosmos-db/).

### <a name="does-the-sql-api-support-sql"></a>Podporuje rozhraní SQL rozhraní SQL?

Dotazovací jazyk SQL podporovaný účty rozhraní SQL API je rozšířená podmnožina funkce dotazu, která je podporována sql serverem. Dotazovací jazyk Sql Azure Cosmos DB poskytuje bohaté hierarchické a relační operátory a rozšiřitelnost prostřednictvím funkcí založených na uživateli (UOF) založených na JavaScriptu. Gramatika JSON umožňuje modelování dokumentů JSON jako stromy s označenými uzly, které se používají pomocí technik automatického indexování Azure Cosmos DB a dialektu dotazu SQL služby Azure Cosmos DB. Informace o použití gramatiky SQL naleznete v článku [dotazu SQL.][query]

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>Podporuje rozhraní SQL API funkce agregace SQL?

Rozhraní SQL API podporuje agregaci s nízkou latencí `MIN` `MAX`v `AVG`libovolném měřítku prostřednictvím agregačních funkcí `SUM` `COUNT`, , a prostřednictvím gramatiky SQL. Další informace naleznete v tématu [Agregační funkce](sql-query-aggregates.md).

### <a name="how-does-the-sql-api-provide-concurrency"></a>Jak rozhraní SQL API poskytuje souběžnost?

Rozhraní SQL API podporuje optimistické řízení souběžnosti (OCC) prostřednictvím značek entit HTTP nebo etags. Každý prostředek rozhraní SQL API má etag a ETag je nastavena na serveru při každé aktualizaci dokumentu. Hlavička ETag a aktuální hodnota jsou zahrnuty ve všech zprávách odpovědi. ETags lze použít s If-Match záhlaví umožnit serveru rozhodnout, zda prostředek by měl být aktualizován. Hodnota If-Match je hodnota ETag, která má být kontrolována. Pokud hodnota ETag odpovídá hodnotě etag serveru, prostředek se aktualizuje. Pokud eTag již není aktuální, server odmítne operaci s kódem odpovědi "HTTP 412 Precondition failure". Klient pak znovu načte prostředek k získání aktuální hodnoty ETag pro prostředek. Kromě toho ETags lze použít s If-None-Match záhlaví k určení, zda je potřeba znovu načíst prostředek.

Chcete-li použít optimistickou souběžnost v rozhraní .NET, použijte třídu [AccessCondition.](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) Ukázka rozhraní .NET najdete [v tématu Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) v ukázce DocumentManagement na GitHubu.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Jak lze provádět transakce v rozhraní SQL API?

Rozhraní SQL API podporuje transakce integrované jazykem prostřednictvím procedur a aktivačních událostí uložených javascriptem. Všechny databázové operace uvnitř skriptů jsou prováděny pod snímek izolace. Pokud je kontejner s jedním oddílem, provádění je vymezena na kontejner. Pokud je kontejner rozdělen na oddíly, provádění je vymezeno na dokumenty se stejnou hodnotou klíče oddílu v rámci kontejneru. Na začátku transakce se pořídí snímek verzí dokumentů (ETagy) a k potvrzení dojde pouze v případě, že skript uspěje. Pokud JavaScript vyvolá chybu, transakce se vrátí zpět. Další informace najdete [v tématu Programování JavaScriptu na straně serveru pro Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Jak lze hromadně vkládat dokumenty do cosmos DB?

Dokumenty můžete do Služby Azure Cosmos DB hromadně vkládat jedním z následujících způsobů:

* Nástroj hromadného vykonavatele, jak je popsáno v [části Použití knihovny .NET hromadného vykonavatele](bulk-executor-dot-net.md) a [Použití knihovny Java hromadného vykonavatele](bulk-executor-java.md)
* Nástroj pro migraci dat, jak je popsáno v [nástroji pro migraci databáze pro Azure Cosmos DB](import-data.md).
* Uložené procedury, jak je popsáno v [programování JavaScriptu na straně serveru pro Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="does-the-sql-api-support-resource-link-caching"></a>Podporuje rozhraní SQL API ukládání do mezipaměti propojení prostředků?

Ano, protože Azure Cosmos DB je služba RESTful, odkazy prostředků jsou neměnné a lze uložit do mezipaměti. Klienti rozhraní SQL API mohou zadat hlavičku "If-None-Match" pro čtení proti dokumentu nebo kontejneru podobnému prostředkům a poté aktualizovat místní kopie po změně verze serveru.

### <a name="is-a-local-instance-of-sql-api-available"></a>Je k dispozici místní instance rozhraní SQL API?

Ano. [Emulátor Azure Cosmos DB](local-emulator.md) poskytuje emulaci služby Cosmos DB s vysokou věrností. Podporuje funkce, které jsou shodné s Azure Cosmos DB, včetně podpory pro vytváření a dotazování dokumentů JSON, zřizování a škálování kolekcí a provádění uložených procedur a aktivačních událostí. Můžete vyvíjet a testovat aplikace pomocí Emulátoru Azure Cosmos DB a nasadit je do Azure v globálním měřítku provedením jediné změny konfigurace koncového bodu připojení pro Azure Cosmos DB.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>Proč jsou dlouhé hodnoty s plovoucí desetinnou desetinnou desetinnou desetinnou hodnotou v dokumentu zaokrouhlené při zobrazení z průzkumníka dat na portálu.

Toto je omezení JavaScriptu. JavaScript používá double-přesnost s plovoucí desetinnou desetinnou položkou formát čísla, jak je uvedeno v IEEE 754 a může bezpečně držet čísla mezi -(2<sup>53</sup> - 1) a 2<sup>53</sup>-1 (tj. 9007199254740991) pouze.

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Kde jsou v hierarchii objektů povolena oprávnění?

Vytváření oprávnění pomocí ResourceTokens je povoleno na úrovni kontejneru a jeho potomků (například dokumenty, přílohy). To znamená, že pokus o vytvoření oprávnění na úrovni databáze nebo účtu není aktuálně povolen.

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>Rozhraní API služby Azure Cosmos DB pro MongoDB

### <a name="what-is-the-azure-cosmos-dbs-api-for-mongodb"></a>Jaké je rozhraní API Azure Cosmos DB pro MongoDB?

Rozhraní API Azure Cosmos DB pro MongoDB je vrstva kompatibility drátových protokolů, která umožňuje aplikacím snadno a transparentně komunikovat s nativním databázovým strojem Azure Cosmos pomocí existujících sad SDK a ovladačů podporovaných komunitou pro MongoDB. Vývojáři teď můžou používat existující řetězce nástrojů mongoDB a dovednosti k vytváření aplikací, které využívají Azure Cosmos DB. Vývojáři těží z jedinečných funkcí Azure Cosmos DB, které zahrnují globální distribuci s replikací více hlavních serverů, automatickým indexováním, údržbou zálohování, finančně podporovanými smlouvami o úrovni služeb (SLA) atd.

### <a name="how-do-i-connect-to-my-database"></a>Jak se připojím ke své databázi?

Nejrychlejší způsob, jak se připojit k databázi Cosmos pomocí rozhraní API Azure Cosmos DB pro MongoDB, je přejít na [portál Azure](https://portal.azure.com). Přejděte na svůj účet a v levé navigační nabídce klikněte na **Rychlý start**. Rychlý start je nejlepší způsob, jak získat fragmenty kódu pro připojení k databázi.

Azure Cosmos DB vynucuje přísné požadavky na zabezpečení a standardy. Účty Azure Cosmos DB vyžadují ověřování a zabezpečenou komunikaci prostřednictvím tls, takže nezapomeňte použít TLSv1.2.

Další informace najdete [v tématu Připojení k databázi Cosmos pomocí rozhraní API Azure Cosmos DB pro MongoDB](connect-mongodb-account.md).

### <a name="are-there-additional-error-codes-that-i-need-to-deal-with-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Existují další kódy chyb, které musím řešit při používání rozhraní API Azure Cosmos DB pro MongoDB?

Spolu se společnými kódy chyb MongoDB má rozhraní API Azure Cosmos DB pro MongoDB své vlastní specifické kódy chyb:

| Chyba               | kód  | Popis  | Řešení  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Celkový počet spotřebovaných jednotek požadavku je větší než zřízená sazba jednotky požadavku pro kontejner a byla omezena. | Zvažte škálování propustnost přiřazené ke kontejneru nebo sadu kontejnerů z portálu Azure nebo opakování znovu. |
| ExceededMemoryLimit | 16501 | Jako služba s více tenanty operace přešla přes přidělení paměti klienta. | Zmenšete rozsah operace prostřednictvím přísnějších kritérií dotazu nebo se obraťte na podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br><em> &nbsp; &nbsp;Příklad: &nbsp; &nbsp;db.getCollection('users').aggregate([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {jméno: "Andy"}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {věk: -1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])</em>) |

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>Je ovladač Simba pro MongoDB podporovaný pro použití s rozhraním API Azure Cosmos DB pro MongoDB?

Ano, ovladač Mongo ODBC společnosti Simba můžete použít s rozhraním API Azure Cosmos DB pro MongoDB.

## <a name="table-api"></a><a id="table"></a>Rozhraní Table API

### <a name="how-can-i-use-the-table-api-offering"></a>Jak mohu použít nabídku rozhraní TABLE API?

Rozhraní Azure Cosmos DB Table API je dostupné na [webu Azure Portal][azure-portal]. Nejprve se musíte zaregistrovat k předplatnému Azure. Po registraci můžete do předplatného Azure přidat účet rozhraní API Azure Cosmos DB Table API a pak do svého účtu přidat tabulky.

Podporované jazyky a přidružené rychlé spuštění najdete v [rozhraní The Introduction to Azure Cosmos DB Table API](table-introduction.md).

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Potřebuji novou sdk pro použití rozhraní Table API?

Ne, existující sady SDK úložiště by měly stále fungovat. Doporučuje se však, aby se vždy dostalo nejnovějšísady SDK pro nejlepší podporu a v mnoha případech vynikající výkon. Podívejte se na seznam dostupných jazyků v [úvodu k rozhraní API tabulky Azure Cosmos DB](table-introduction.md).

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>V čem není rozhraní API pro tabulky shodné s chováním s Azure Table Storage?

Existují určité rozdíly v chování, které by uživatelé z úložiště Azure Table Storage, kteří chtějí vytvářet tabulky pomocí rozhraní Azure Cosmos DB Table API, měli znát:

* Azure Cosmos DB Table API používá model vyhrazené kapacity k zajištění garantovaného výkonu, ale to znamená, že se platí za kapacitu, jakmile je tabulka vytvořena, i když se kapacita nepoužívá. S úložištěm Azure Table se platí jenom za využitou kapacitu. To pomáhá vysvětlit, proč table api může nabídnout 10 ms čtení a 15 ms psát SLA na 99 percentilu, zatímco Azure Table storage nabízí 10 sekund SLA. Ale v důsledku toho s tabulkami table API, i prázdné tabulky bez jakýchkoli požadavků, stojí peníze, aby bylo zajištěno, že kapacita je k dispozici pro zpracování všech požadavků na ně na SLA nabízí Azure Cosmos DB.
* Výsledky dotazu vrácené rozhraním TABLE API nejsou seřazeny v pořadí klíčů mezi klíči oddílu a řádky, jako jsou v úložišti Azure Table.
* Klíče řádků mohou mít pouze 255 bajtů
* Dávky mohou mít pouze 2 MBs
* CORS není momentálně podporován.
* Názvy tabulek v úložišti tabulek Azure nerozlišují malá a velká písmena, ale jsou v rozhraní API tabulky Db Azure Cosmos
* Některé interní formáty Azure Cosmos DB pro kódování informací, jako jsou binární pole, nejsou aktuálně tak efektivní, jak by se mohlo líbit. Proto to může způsobit neočekávaná omezení velikosti dat. Například v současné době nelze použít celou jednu Meg entity tabulky k ukládání binárních dat, protože kódování zvyšuje velikost dat.
* Název vlastnosti entity ID není aktuálně podporován.
* TableQuery TakeCount není omezena na 1000

Pokud jde o rozhraní REST API existuje řada koncových bodů/možností dotazu, které nejsou podporovány rozhraním AZURE Cosmos DB Table API:

| Metoda odpočinku (metody) | Možnost koncového bodu nebo dotazu | Adresy URL dokumentů | Vysvětlení |
| ------------| ------------- | ---------- | ----------- |
| ZÍSKAT, DÁT | /?restype=service@comp=vlastnosti| [Nastavit vlastnosti služby Table Service](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) a [získat vlastnosti služby table service](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) | Tento koncový bod se používá k nastavení pravidel CORS, konfigurace analýzy úložiště a nastavení protokolování. CORS momentálně není podporovaný a analýzy a protokolování se v Azure Cosmos DB zpracovávají jinak než tabulky úložišť Azure. |
| Možnosti | /\<> názvu zdroje tabulky | [Předletový požadavek na tabulku CORS](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | Toto je součástí CORS, které Azure Cosmos DB aktuálně nepodporuje. |
| GET | /?restype=service@comp=statistiky | [Získat statistiky stolních služeb](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | Obsahuje informace o tom, jak rychle se data replikují mezi primárními a sekundárními údaji. To není potřeba v Cosmos DB jako replikace je součástí zápisů. |
| ZÍSKAT, DÁT | /mytable?comp=acl | [Získat acl acl tabulky](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) a [nastavit tabulku](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) | Tím získáte a nastavíte uložené zásady přístupu používané ke správě sdílených přístupových podpisů (SAS). Přestože je podporováno SAS, jsou nastaveny a spravovány odlišně. |

Kromě toho rozhraní API tabulky Azure Cosmos DB podporuje jenom formát JSON, nikoli ATOM.

Zatímco Azure Cosmos DB podporuje sdílené přístupové podpisy (SAS), existují určité zásady, které nepodporuje, konkrétně ty, které souvisejí s operacemi správy, jako je právo vytvářet nové tabulky.

Zejména pro sdk .NET existují některé třídy a metody, které Azure Cosmos DB aktuálně nepodporuje.

| Třída | Nepodporovaná metoda |
|-------|-------- |
| CloudTableClient | \*Vlastnosti služby* |
|                  | \*Statistiky služeb* |
| Cloudtable | Nastavit oprávnění* |
|            | Získat oprávnění* |
| TableServiceContext | * (tato třída je zastaralá) |
| TableServiceEntity | " " |
| Rozšíření tableserviceextensions | " " |
| TableServiceQuery | " " |

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Jak mohu poskytnout zpětnou vazbu o SDK nebo chyby?

Svůj názor můžete sdílet libovolným z následujících způsobů:

* [Hlas uživatele](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [Fórum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Přetečení zásobníku](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Přetečení zásobníku je nejlepší pro programování otázek. Ujistěte se, že vaše otázka je [on-topic](https://stackoverflow.com/help/on-topic) a [poskytnout co nejvíce podrobností, jak je to možné, takže otázka jasná a odpovědětelná](https://stackoverflow.com/help/how-to-ask).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Jaký je připojovací řetězec, který potřebuji použít pro připojení k rozhraní TABLE API?

Připojovací řetězec je:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

Připojovací řetězec můžete získat ze stránky připojovací řetězec na webu Azure Portal.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Jak přepíšem nastavení konfigurace možností požadavku v sedstě .NET SDK pro rozhraní TABLE API?

Některá nastavení jsou zpracována metodou CreateCloudTableClient a další prostřednictvím application.config v části appSettings v klientské aplikaci. Informace o nastavení konfigurace najdete v tématu [možnosti Azure Cosmos DB](tutorial-develop-table-dotnet.md).

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Existují nějaké změny pro zákazníky, kteří používají existující sady SDK úložiště tabulky Azure?

Žádné. Neexistují žádné změny pro stávající nebo nové zákazníky, kteří používají existující sady SDK úložiště tabulky Azure.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Jak se zobrazí data tabulky, která jsou uložená v Azure Cosmos DB pro použití s rozhraním Table API?

K procházení dat můžete použít portál Azure. Můžete také použít kód rozhraní API tabulky nebo nástroje uvedené v další odpovědi.

### <a name="which-tools-work-with-the-table-api"></a>Které nástroje fungují s rozhraním Table API?

Můžete použít [Průzkumníka úložiště Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

Nástroje s flexibilitou pro převzetí připojovacího řetězce ve formátu, který byl dříve zadaný, mohou podporovat nové rozhraní TABLE API. Seznam nástrojů tabulky je k dispozici na stránce [Nástroje klienta úložiště Azure.](../storage/common/storage-explorers.md)

### <a name="is-the-concurrency-on-operations-controlled"></a>Je souběžnost operací řízena?

Ano, optimistická souběžnost je poskytována pomocí mechanismu ETag.

### <a name="is-the-odata-query-model-supported-for-entities"></a>Je model dotazu OData podporován pro entity?

Ano, rozhraní TABLE API podporuje dotaz OData a linq dotaz.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Můžu se ve stejné aplikaci připojit k Azure Table Storage a rozhraníapi tabulky Azure Cosmos DB vedle sebe?

Ano, můžete se připojit vytvořením dvou samostatných instancí cloudtableclient, z nichž každá ukazuje na vlastní identifikátor URI prostřednictvím připojovacího řetězce.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Jak můžu migrovat existující aplikaci úložiště Azure Table do této nabídky?

[AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) a [Nástroj pro migraci dat Azure Cosmos DB](import-data.md) jsou podporovány.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Jak se rozšíření velikosti úložiště provádí pro tuto službu, pokud například začnu s *n* GB dat a moje data se časem zvýší na 1 TB?

Azure Cosmos DB je navržený tak, aby poskytoval neomezené úložiště pomocí horizontálníškálování. Služba může monitorovat a efektivně zvyšovat vaše úložiště.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Jak můžu sledovat nabídku rozhraní TABLE API?

Podokno **Metriky** rozhraní TABLE API můžete použít ke sledování požadavků a využití úložiště.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Jak vypočítám propustnost, kterou potřebuji?

Odhad kapacity můžete použít k výpočtu TableThroughput, který je požadován pro operace. Další informace naleznete v [tématu Odhad jednotek požadavků a úložiště dat](https://www.documentdb.com/capacityplanner). Obecně můžete zobrazit entitu jako JSON a zadat čísla pro vaše operace.

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Lze pomocí emulátoru používat sdk rozhraní Table API místně?

V tuto chvíli to není možné.

### <a name="can-my-existing-application-work-with-the-table-api"></a>Může moje stávající aplikace pracovat s rozhraním Table API?

Ano, je podporováno stejné rozhraní API.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>Musím migrovat své stávající aplikace úložiště Azure Table do sady SDK, pokud nechci používat funkce rozhraní TABLE API?

Ne, můžete vytvořit a používat existující prostředky úložiště Azure Table bez přerušení jakéhokoli druhu. Pokud však nepoužíváte rozhraní TABLE API, nemůžete využívat výhod automatického indexu, další možnostkonzistence nebo globální distribuce.

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Jak přidám replikaci dat v rozhraní Table API ve více než jedné oblasti Azure?

Pomocí [globálního nastavení replikace](tutorial-global-distribution-sql-api.md#portal) portálu Azure Cosmos DB můžete přidat oblasti, které jsou vhodné pro vaši aplikaci. Chcete-li vyvinout globálně distribuované aplikace, měli byste také přidat aplikaci s PreferredLocation informace nastavena do místní oblasti pro poskytování nízké latence čtení.

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Jak změním primární oblast zápisu pro účet v rozhraní TABLE API?

Podokno portálu globální replikace Azure Cosmos DB můžete použít k přidání oblasti a převzetí služeb při selhání do požadované oblasti. Pokyny naleznete v [tématu Vývoj s účty Azure Cosmos DB s více oblastmi](high-availability.md).

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Jak nakonfiguruji preferované oblasti čtení pro nízkou latenci při distribuci dat?

Chcete-li pomoci číst z místního umístění, použijte klíč PreferredLocation v souboru app.config. Pro existující aplikace rozhraní TABLE API vyvolá chybu, pokud locationmode je nastavena. Odeberte tento kód, protože rozhraní TABLE API tyto informace vyzvedne ze souboru app.config. 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Jak mám přemýšlet o úrovních konzistence v rozhraní Table API?

Azure Cosmos DB poskytuje dobře odůvodněné kompromisy mezi konzistencí, dostupností a latencí. Azure Cosmos DB nabízí vývojářům rozhraní TABLE API pět úrovní konzistence, takže si můžete vybrat správný model konzistence na úrovni tabulky a provádět jednotlivé požadavky při dotazování na data. Když se klient připojí, může určit úroveň konzistence. Úroveň můžete změnit pomocí argumentu consistencyLevel klienta CreateCloudTableClient.

Tabulka ROZHRANÍ API poskytuje čtení s nízkou latencí s "Číst vlastní zápisy," s bounded-zastaralá konzistence jako výchozí. Další informace naleznete v [tématu Úrovně konzistence](consistency-levels.md).

Ve výchozím nastavení azure table storage poskytuje silnou konzistenci v rámci oblasti a konzistenci Eventuální v sekundárních umístěních.

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Nabízí rozhraní API tabulky Db Azure Cosmos více úrovní konzistence než úložiště table Azure?

Ano, informace o tom, jak těžit z distribuované povahy Azure Cosmos DB, najdete v [tématu úrovně konzistence](consistency-levels.md). Vzhledem k tomu, že jsou k dispozici záruky pro úrovně konzistence, můžete je použít s jistotou.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Pokud je povolena globální distribuce, jak dlouho trvá replikace dat?

Azure Cosmos DB potvrdí data trvale v místní oblasti a odešle data do jiných oblastí okamžitě během několika milisekund. Tato replikace je závislá pouze na době odezvy (RTT) datového centra. Další informace o možnostech globální distribuce služby Azure Cosmos DB najdete v tématu [Azure Cosmos DB: Globálně distribuovaná databázová služba v Azure](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>Lze změnit úroveň konzistence požadavku na čtení?

S Azure Cosmos DB můžete nastavit úroveň konzistence na úrovni kontejneru (v tabulce). Pomocí sady .NET SDK můžete změnit úroveň zadáním hodnoty klávesy TableConsistencyLevel v souboru app.config. Možné hodnoty jsou: Silná, ohraničená neaktuálnost, Relace, Konzistentní předpona a Eventual. Další informace najdete [v tématu Laditelné úrovně konzistence dat v Azure Cosmos DB](consistency-levels.md). Klíčovou myšlenkou je, že nelze nastavit úroveň konzistence požadavku na více než nastavení pro tabulku. Například nelze nastavit úroveň konzistence pro tabulku na eventual a úroveň konzistence požadavku na silné.

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Jak rozhraní API tabulky zpracovává převzetí služeb při selhání, pokud dojde k selhání oblasti?

Rozhraní Table API využívá globálně distribuovanou platformu Azure Cosmos DB. Chcete-li zajistit, aby vaše aplikace mohla tolerovat prostoje datového centra, povolte alespoň jednu další oblast pro účet na portálu Azure Cosmos DB [Vývoj s účty Azure Cosmos DB s více oblastmi](high-availability.md). Prioritu oblasti můžete nastavit pomocí portálu [Vývoj s účty Azure Cosmos DB s více oblastmi](high-availability.md).

Můžete přidat libovolný počet oblastí pro účet a řídit, kde může převzít služby při selhání, a to poskytnutím priority převzetí služeb při selhání. Chcete-li použít databázi, musíte také zadat aplikaci. Pokud tak učiníte, vaši zákazníci nebudou mít prostoje. [Nejnovější sada SDK klienta .NET](table-sdk-dotnet.md) je automatické navádění, ale ostatní sady SDK nejsou. To znamená, že může zjistit oblast, která je dolů a automaticky převzetí služeb při selhání do nové oblasti.

### <a name="is-the-table-api-enabled-for-backups"></a>Je rozhraní TABLE API povoleno pro zálohování?

Ano, rozhraní Table API využívá platformu Azure Cosmos DB pro zálohování. Zálohy se provádějí automaticky. Další informace najdete [v tématu Online zálohování a obnovení pomocí Azure Cosmos DB](online-backup-and-restore.md).

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>Indexuje rozhraní TABLE API ve výchozím nastavení všechny atributy entity?

Ano, všechny atributy entity jsou ve výchozím nastavení indexovány. Další informace najdete v [tématu Azure Cosmos DB: Zásady indexování](index-policy.md).

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>Znamená to, že není třeba vytvářet více než jeden index pro uspokojení dotazů?

Ano, rozhraní Azure Cosmos DB Table API poskytuje automatické indexování všech atributů bez definice schématu. Tato automatizace uvolní vývojáři zaměřit se na aplikaci, spíše než na vytváření a správu indexu. Další informace najdete v [tématu Azure Cosmos DB: Zásady indexování](index-policy.md).

### <a name="can-i-change-the-indexing-policy"></a>Mohu změnit zásady indexování?

Ano, zásady indexování můžete změnit poskytnutím definice indexu. Musíte správně zakódovat a uniknout nastavení.

U sad non-.NET sad SDK lze zásady indexování nastavit pouze na portálu v **Průzkumníku dat**, přejděte na konkrétní tabulku, kterou chcete změnit, a potom přejděte na **& nastavení měřítka**– >zásady indexování, proveďte požadovanou změnu a **potom uložte**.

Ze sady .NET SDK jej lze odeslat v souboru app.config:

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

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Zdá se, že Azure Cosmos DB jako platforma má mnoho funkcí, jako je řazení, agregace, hierarchie a další funkce. Budete přidávat tyto funkce do rozhraní TABLE API?

Rozhraní TABLE API poskytuje stejné funkce dotazu jako úložiště tabulky Azure. Azure Cosmos DB podporuje také řazení, agregace, geoprostorové dotazy, hierarchie a širokou škálu integrovaných funkcí. Další informace naleznete v tématu [dotazy SQL](how-to-sql-query.md).

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Kdy mám změnit TableThroughput pro rozhraní TABLE API?

Měli byste změnit TableThroughput, pokud platí jedna z následujících podmínek:

* Provádíte extrahování, transformace a načtení (ETL) dat, nebo chcete nahrát velké množství dat v krátkém čase.
* Potřebujete větší propustnost z kontejneru nebo ze sady kontejnerů na back-endu. Například uvidíte, že použitá propustnost je větší než zřízená propustnost a dochází k omezení. Další informace najdete v tématu [Set propustnost pro kontejnery Azure Cosmos](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Je možné vertikálně navýšit kapacitu nebo zmenšit propustnost tabulky rozhraní TABLE API?

Ano, podokno škálování portálu Azure Cosmos DB můžete použít ke škálování propustnost. Další informace naleznete v tématu [Set propustnost](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Je výchozí TableThroughput nastavena pro nově zřízené tabulky?

Ano, pokud nepřepíšete TableThroughput prostřednictvím app.config a nepoužíváte předem vytvořený kontejner v Azure Cosmos DB, služba vytvoří tabulku s propustností 400.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Došlo ke změně cen pro stávající zákazníky služby Azure Table storage?

Žádné. Pro stávající zákazníky úložiště Azure Table nedojde k žádné změně ceny.

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Jak se vypočítává cena pro rozhraní TABLE API?

Cena závisí na přidělené TableThroughput.

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Jak zpracovat omezení rychlosti v tabulkách v nabídce rozhraní Table API?

Pokud je míra požadavku větší než kapacita zřízené propustnosti pro základní kontejner nebo sadu kontejnerů, zobrazí se chyba a sada SDK opakuje volání použitím zásady opakování.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Proč musím vybrat propustnost kromě PartitionKey a RowKey, abych mohl využít nabídku rozhraní Table API azure cosmos db?

Azure Cosmos DB nastaví výchozí propustnost pro váš kontejner, pokud nezadáte v souboru app.config nebo prostřednictvím portálu.

Azure Cosmos DB poskytuje záruky výkonu a latence s horní hranice na provoz. Tato záruka je možné, když modul můžete vynutit zásadsprávné zásady na operace klienta. Nastavení TableThroughput zajišťuje, že získáte garantovanou propustnost a latenci, protože platforma rezervuje tuto kapacitu a zaručuje provozní úspěch.

Pomocí specifikace propustnosti ji můžete elasticky změnit, abyste mohli využívat sezónnost vaší aplikace, splňovat potřeby propustnosti a šetřit náklady.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>Azure Table storage je pro mě levné, protože platím jenom za ukládání dat a zřídka se dotazuji. Zdá se, že nabídka rozhraní API Table API Azure Cosmos mě účtuje, i když jsem neprovedl jedinou transakci ani nic neuložil. Můžete mi to vysvětlit?

Azure Cosmos DB je navržený jako globálně distribuovaný systém založený na sla se zárukami dostupnosti, latence a propustnosti. Když si rezervujete propustnost v Azure Cosmos DB, je zaručena, na rozdíl od propustnosti jiných systémů. Azure Cosmos DB poskytuje další funkce, které zákazníci požadovali, jako jsou sekundární indexy a globální distribuce.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Při ingestování dat do úložiště Azure Table se nikdy nezobrazí úplné oznámení o kvótě (označující, že oddíl je plný). S table API, já si tuto zprávu. Je tato nabídka omezení mě a nutí mě změnit svou stávající aplikaci?

Azure Cosmos DB je systém založený na sla, který poskytuje neomezené škálování se zárukami latence, propustnosti, dostupnosti a konzistence. Chcete-li zajistit zaručený výkon premium, ujistěte se, že velikost dat a index jsou spravovatelné a škálovatelné. Limit 10 GB na počet entit nebo položek na klíč oddílu je zajistit, že poskytujeme skvělé vyhledávání a výkon dotazu. Chcete-li zajistit, že vaše aplikace škáluje dobře, a to i pro Azure Storage, doporučujeme *nevytvářet* aktivní oddíl uložením všech informací v jednom oddílu a dotazování na něj.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Takže PartitionKey a RowKey jsou stále vyžadovány s rozhraním API tabulky?

Ano. Vzhledem k tomu, že plocha rozhraní TABLE API je podobná ploše sady Azure Table storage SDK, klíč oddílu poskytuje efektivní způsob distribuce dat. Klíč řádku je jedinečný v rámci tohoto oddílu. Klíč řádku musí být přítomen a nemůže být null jako ve standardní sadě SDK. Délka RowKey je 255 bajtů a délka PartitionKey je 1 KB.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Jaké jsou chybové zprávy pro rozhraní TABLE API?

Azure Table storage a Azure Cosmos DB Table API používají stejné sady SDK, takže většina chyb bude stejná.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Proč se mi v rozhraní TABLE API snažím vytvářet velké množství tabulek jeden po druhém?

Azure Cosmos DB je systém založený na sla, který poskytuje latenci, propustnost, dostupnost a konzistenci záruky. Vzhledem k tomu, že se jedná o zřízený systém, rezervuje prostředky k zajištění těchto požadavků. Rychlost vytváření tabulek je detekována a omezena. Doporučujeme, abyste se podívali na rychlost vytváření tabulek a snížili ji na méně než 5 za minutu. Nezapomeňte, že rozhraní Table API je zřízený systém. V okamžiku, kdy ji zřídíte, začnete za to platit.

## <a name="gremlin-api"></a>Rozhraní Gremlin API

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>Pro vývoj jazyka C#/.NET mám použít balíček Microsoft.Azure.Graphs nebo Gremlin.NET?

Rozhraní API Azure Cosmos DB Gremlin využívá ovladače s otevřeným zdrojovým kódem jako hlavní konektory pro službu. Takže doporučená možnost je použít [ovladače, které jsou podporovány Apache Tinkerpop](https://tinkerpop.apache.org/).

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Jak se ru/s účtují při spuštění dotazů v databázi grafů?

Všechny objekty grafu, vrcholy a hrany jsou zobrazeny jako dokumenty JSON v back-endu. Vzhledem k tomu, že jeden dotaz Gremlin můžete upravit jeden nebo více objektů grafu najednou, náklady s ním spojené přímo souvisí s objekty, hrany, které jsou zpracovány dotazem. Jedná se o stejný proces, který používá Azure Cosmos DB pro všechna ostatní api. Další informace najdete v tématu [o jednotkách žádostí v Azure Cosmos DB](request-units.md).

Poplatek ŽP je založen na pracovní datové sadě průchodu, nikoli na sadě výsledků. Například pokud dotaz má za cíl získat jeden vrchol jako výsledek, ale potřebuje procházet více než jeden jiný objekt na cestě, pak náklady budou založeny na všech objektů grafu, které bude trvat k výpočtu jeden výsledek vrchol.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Jaké je maximální škálování, které může mít databáze grafů v rozhraní API Azure Cosmos DB Gremlin?

Azure Cosmos DB využívá [horizontální dělení](partition-data.md) automaticky řešit zvýšení požadavků na úložiště a propustnost. Maximální propustnost a kapacita úložiště úlohy je určena počtem oddílů, které jsou přidruženy k danému kontejneru. Kontejner rozhraní API Gremlin má však konkrétní sadu pokynů k zajištění správné ho výkonu ve velkém měřítku. Další informace o dělení a doporučené postupy najdete v článku [dělení v azure cosmos db.](partition-data.md)

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Jak se mohu chránit před injekčními útoky pomocí ovladačů Gremlin?

Většina nativních ovladačů Apache Tinkerpop Gremlin umožňuje poskytnout slovník parametrů pro spuštění dotazu. Toto je příklad toho, jak to udělat v [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) a v [Gremlin-Javascript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js).

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Proč se zobrazuje chyba "Chyba kompilace gremlinského dotazu: Nelze najít žádnou metodu"?

Rozhraní Azure Cosmos DB Gremlin API implementuje podmnožinu funkcí definovaných v oblasti povrchu Gremlin. Podporované kroky a další informace naleznete v článku [podpory gremlin.](gremlin-support.md)

Nejlepším řešením je přepsat požadované kroky Gremlin s podporovanou funkcí, protože všechny základní kroky Gremlin jsou podporovány Službou Azure Cosmos DB.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Proč se zobrazuje chyba "WebSocketException: Server vrátil stavový kód 200"?

Tato chyba je pravděpodobně vyvolána při použití nesprávného koncového bodu. Koncový bod, který generuje tuto chybu má následující vzor:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

Toto je koncový bod dokumentů pro databázi grafů.  Správný koncový bod, který se má použít, je koncový bod Gremlin, který má následující formát:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Proč se zobrazuje chyba RequestRateIsTooLarge?

Tato chyba znamená, že přidělené jednotky požadavků za sekundu nestačí k zobrazení dotazu. Tato chyba se obvykle zobrazí při spuštění dotazu, který získá všechny vrcholy:

```
// Query example:
g.V()
```

Tento dotaz se pokusí načíst všechny vrcholy z grafu. Náklady na tento dotaz se tedy budou rovnat alespoň počtu vrcholů z hlediska ru. Nastavení ŽP/s by mělo být upraveno tak, aby tento dotaz řešilo.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Proč se moje spoje ovladačů Gremlin nakonec vypustí?

Připojení Gremlin je provedeno prostřednictvím připojení WebSocket. Přestože připojení WebSocket nemají konkrétní čas k životu, rozhraní API Azure Cosmos DB Gremlin ukončí nečinnosti připojení po 30 minutách nečinnosti.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Proč nemohu používat plynulá volání rozhraní API v nativních ovladačích Gremlin?

Fluent API volání ještě nejsou podporovány rozhraní API Azure Cosmos DB Gremlin. Fluent API volání vyžadují interní formátování funkce známé jako podpora bajtkódu, který aktuálně není podporován rozhraním API Azure Cosmos DB Gremlin. Ze stejného důvodu není v současné době podporován ani nejnovější ovladač Gremlin-JavaScript.

### <a name="how-can-i-evaluate-the-efficiency-of-my-gremlin-queries"></a>Jak mohu vyhodnotit efektivitu svých dotazů gremlin?

**SpuštěníProfil()** náhled krok lze poskytnout analýzu plánu spuštění dotazu. Tento krok je třeba přidat na konec každého dotazu Gremlin, jak je znázorněno v následujícím příkladu:

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

Výstup výše uvedeného profilu ukazuje, kolik času je vynaloženo na získání objektů vrcholu, objektů hran a velikosti pracovní datové sady. To souvisí s měřením standardních nákladů pro dotazy Azure Cosmos DB.

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md
