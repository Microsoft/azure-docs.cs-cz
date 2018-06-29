---
title: Nejčastější dotazy k Azure Cosmos DB | Microsoft Docs
description: Získejte odpovědi na nejčastější dotazy týkající se Azure Cosmos databáze, databáze globálně distribuované, více modelu služby. Další informace o kapacitě a úrovně výkonu a škálování.
keywords: Otázky k databázi, často kladené otázky, documentdb, azure, Microsoft azure
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: sngun
ms.openlocfilehash: 769d530d85199e3f38890589e3719ba35f7cf5d6
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097967"
---
# <a name="azure-cosmos-db-faq"></a>Nejčastější dotazy k Azure Cosmos DB
## <a name="azure-cosmos-db-fundamentals"></a>Základy Azure Cosmos DB
### <a name="what-is-azure-cosmos-db"></a>Co je Azure Cosmos DB?
Azure Cosmos DB je globálně replikované databáze, více modelu služba, která nabízí bohaté dotazování přes data bez schémat, pomáhá poskytovat Konfigurovatelný a spolehlivý výkon a umožňuje rychlý vývoj. Ho všechny dosáhnete prostřednictvím spravovaná platforma, která je podpořenou výkonem a dosahem Microsoft Azure. 

Azure Cosmos DB je správným řešením pro webové, mobilní a herní, a aplikace či aplikace IoT při předvídatelnou propustnost, vysokou dostupnost, nízkou latenci a bez schémat datového modelu jsou klíčové požadavky. Poskytuje flexibilitu schémat a bohaté indexování a zahrnuje podporu transakcí několika dokumentů s integrovaným JavaScriptem. 

Další databáze otázky, odpovědi a pokyny pro nasazení a používání této služby najdete v tématu [stránce dokumentace Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/).

### <a name="what-happened-to-the-documentdb-api"></a>Co se stalo rozhraní API DocumentDB?

Rozhraní API služby Azure Cosmos databáze DocumentDB nebo SQL (DocumentDB) rozhraní API se nyní označuje jako rozhraní API pro Azure Cosmos databáze SQL. Nemusíte změnit všechno, chcete-li pokračovat, spouští vaše aplikace vytvořené s DocumentDB rozhraní API. Funkce zůstává stejná.

Pokud jste měli účet DocumentDB API před, nyní máte účet rozhraní API pro SQL, nezmění se vaše fakturace. 

### <a name="what-happened-to-azure-documentdb-as-a-service"></a>Co se stalo s Azure DocumentDB jako službu?

Služba Azure DocumentDB je nyní součástí služby Azure Cosmos DB a se projevuje ve formě rozhraní SQL API. Aplikace sestavené s Azure DocumentDB se spustí bez uložení změn rozhraní API Azure Cosmos databáze SQL. Kromě toho Azure Cosmos DB podporuje rozhraní Graph API, rozhraní API tabulky, MongoDB rozhraní API a rozhraní API Cassandra (Preview).

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Jaké jsou typické použití případů pro Azure Cosmos DB?
Azure Cosmos DB je dobrou volbou pro nové webové, mobilní a herní, a je důležité, aplikace či aplikace IoT kde automatické škálování, předvídatelný výkon, rychlé pořadí doby odezvy milisekund a schopnost dotazovat přes data bez schémat. Azure Cosmos DB slouží k rychlému vývoji a podpoře nepřetržitých iterací modelů dat aplikace. Aplikace, které spravují uživatelem generovaný obsah a data jsou [běžné případy použití pro Azure Cosmos DB](use-cases.md). 

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Jak Azure Cosmos DB nabízí předvídatelný výkon?
A [jednotek žádosti](request-units.md) (RU) je mírou propustnosti v Azure Cosmos DB. Propustnost 1 RU odpovídá propustnosti operace GET u 1 KB dokumentu. Všechny operace v Azure DB Cosmos, včetně čtení, zápisu, dotazů SQL a spuštění uložených procedur, má deterministickou RU hodnotu, která je založena na propustnost potřebné k dokončení operace. Namísto přemýšlení o procesoru, vstupně-výstupní operace a paměti a jak každá ovlivňují propustnost aplikace, si můžete představit jako jednu míru RU.

Je možné rezervovat každý kontejner Azure Cosmos DB zajištěnou propustností z hlediska RUs propustnost za sekundu. Pro aplikace jakéhokoli rozsahu můžete srovnávací test jednotlivých požadavků, změřit jejich hodnoty RU a zřídit kontejner pro zpracování celkový počet jednotek žádosti ze všech požadavků. Můžete taky škálovat nebo snižovat propustnost vaší kontejneru potřebám vaší aplikace měnícím. Další informace o jednotkách žádosti a nápovědu pro určení vašeho kontejneru potřebuje, najdete v části [odhadnout požadavky propustnost](request-units.md#estimating-throughput-needs) a zkuste to [propustnost kalkulačky](https://www.documentdb.com/capacityplanner). Termín *kontejneru* zde označují odkazuje na kolekci rozhraní API pro SQL, rozhraní Graph API grafu, kolekce MongoDB rozhraní API a rozhraní API tabulky tabulku. 

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Jak Azure Cosmos DB podporuje různé datové modely, například klíč/hodnota, sloupcové, dokument a graf?

Klíč hodnota (tabulky), sloupcové, dokumentů a data grafu modely jsou všechny nativně podporované kvůli ARS (atomů, záznamy a pořadí) návrhu tohoto Azure DB Cosmos je založený na. Atomů, záznamy a pořadí může být snadno namapované a promítá do různých datových modelů. Rozhraní API pro podmnožinu modely jsou k dispozici pravé nyní (SQL, MongoDB, tabulky a rozhraní Graph API) a ostatní specifické pro další datové modely bude k dispozici v budoucnu.

Azure Cosmos DB má schéma lhostejné indexování modul schopná automaticky indexování všechna data, která ho ingestuje bez nutnosti žádné schéma nebo sekundární indexy od vývojáře. Modul spoléhá na sadu rozvržení logické indexu (obráceným, sloupcové, stromu), které oddělit rozložení úložiště z indexu a zpracování subsystémů dotazů. Cosmos DB má také možnost podporovat sadu přenosu protokolů a rozhraní API extensible způsobem a efektivně převede základní datový model (1) a rozložení logické indexu (2) díky tomu jednoznačně schopný zajistit podporu více datové modely nativně.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Je kompatibilní s Azure Cosmos DB HIPAA?
Ano, je kompatibilní se standardem HIPAA Azure Cosmos DB. HIPAA zavádí požadavky na použití, zveřejnění a ochranu jednotlivě identifikovatelných zdravotních informací. Další informace najdete na webu [Centrum zabezpečení Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Jaká jsou omezení úložiště databáze Cosmos Azure?
Neexistuje žádné omezení na celkovém množství dat, která kontejner můžete pojmout v Azure Cosmos DB.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Jaká jsou omezení propustnosti databáze Cosmos Azure?
Neexistuje žádné omezení na celkovém množství propustnosti, kterou kontejner může podporovat v Azure Cosmos DB. Klíče Rada je distribuovat vaši úlohu přibližně rovnoměrně mezi dostatečně velký počet klíčů oddílů.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted-"></a>Se šifrují přímo a brány režimy připojení? 
Ano jsou vždy plně zašifrované oba režimy. 

### <a name="how-much-does-azure-cosmos-db-cost"></a>Kolik Azure Cosmos DB stojí?
Podrobnosti najdete [podrobnosti o cenách Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) stránky. Poplatky za používání služby Azure Cosmos DB určuje počet zřízené kontejnerů, počet hodin kontejnery byly online, a zřízené propustnosti pro každý kontejner. Termín *kontejnery* zde označují rozhraní API pro SQL shromažďování, grafu rozhraní Graph API, rozhraní API MongoDB kolekce a Table API tabulky. 

### <a name="is-a-free-account-available"></a>Je k dispozici bezplatný účet?
Ano, můžete zaregistrovat pro účet časově omezené bez poplatků, bez závazků. Přejděte na [zdarma zkuste Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) nebo Další informace najdete v [zkuste Azure Cosmos DB – nejčastější dotazy](#try-cosmos-db).

Pokud jste Azure ještě nepoužívali, můžete si zaregistrovat [bezplatný účet Azure](https://azure.microsoft.com/free/), kterým získáte 30 dnů a a Dal na vyzkoušení všech služeb Azure. Pokud máte předplatné sady Visual Studio, jste také vhodné pro [volné kredity Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) na libovolnou službu Azure používat. 

Můžete také [emulátoru DB Cosmos Azure](local-emulator.md) pro vývoj a testování vaší aplikace místně pro uvolnění bez vytváření předplatného Azure. Jakmile budete spokojeni s fungováním aplikace v emulátoru, můžete přejít na účet služby Azure Cosmos DB v cloudu.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Jak můžete získat další pomoc s Azure Cosmos DB?

Položte technické dotazy, můžete odeslat na jednu z těchto dvou otázku a odpovědět fóra:
* [Fórum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Přetečení zásobníku](http://stackoverflow.com/questions/tagged/azure-cosmosdb). Přetečení zásobníku je nejvhodnější pro dotazy programování. Zkontrolujte, že váš dotaz je [na téma](https://stackoverflow.com/help/on-topic) a [zadejte tolik podrobnosti nejdříve provádění na otázku, zrušte zaškrtnutí a který jej](https://stackoverflow.com/help/how-to-ask). 

Pokud chcete požádat o nové funkce, vytvořit novou žádost o na [Uservoice](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Pokud řešíte problém s účtem, podejte prostřednictvím webu Azure Portal [žádost o podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

Další otázky, můžete odeslat týmu v [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com); ale to není alias se na technickou podporu. 

<a id="try-cosmos-db"></a>
## <a name="try-azure-cosmos-db-subscriptions"></a>Zkuste předplatných Azure Cosmos DB

Nyní můžete sledovat v Azure Cosmos DB rozhraní časově omezené bez předplatného zdarma a závazky. Chcete-li zaregistrovat předplatné a zkuste databázi Cosmos Azure, přejděte na [zdarma zkuste Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/). Toto předplatné je nezávislý [bezplatná zkušební verze Azure](https://azure.microsoft.com/free/)a dá se použít kromě bezplatnou zkušební verzi Azure nebo Azure placené předplatné. 

Předplatná Azure Cosmos DB zkuste zobrazovat na portálu Azure vedle dalších předplatná spojená s vaším ID uživatele. 

Tyto podmínky platí pro odběry zkuste Cosmos databázi Azure:

* Jeden kontejner jedno předplatné pro účty SQL, Gremlin (rozhraní Graph API) a tabulku.
* Až 3 kolekce jedno předplatné pro účty MongoDB.
* Kapacita úložiště 10 GB.
* Globální replikace je k dispozici v následujícím [oblastí Azure](https://azure.microsoft.com/regions/): střed USA, severní Evropy a Asie a Tichomoří – jihovýchod
* Maximální propustnost 5 tis. RU/s.
* Předplatných vyprší po 24 hodinách a je možné rozšířit na celkový 48 hodin.
* Pro účty zkuste Azure DB Cosmos; nelze vytvořit lístků podpory Azure Podpora je však k dispozici pro odběratele s existující plánům podpory. 

## <a name="set-up-azure-cosmos-db"></a>Nastavení Azure Cosmos DB
### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Jak přihlásím Azure Cosmos DB?
Azure Cosmos DB je k dispozici na portálu Azure. První zaregistrujte si předplatné Azure. Poté, co jste se přihlásili, můžete přidat rozhraní API pro SQL, rozhraní Graph API, rozhraní API tabulky, MongoDB API nebo rozhraní API Cassandra účtu k předplatnému Azure.

### <a name="what-is-a-master-key"></a>Co je hlavní klíč?
Hlavní klíč je token zabezpečení pro přístup ke všem prostředkům účtu. Uživatelé, kteří mají klíč čtení a zápisu přístup ke všem prostředkům v databázovém účtu. Při distribuci hlavního klíče buďte opatrní. Primární hlavní klíč nebo sekundární hlavní klíč jsou k dispozici na **klíče** okno [portál Azure][azure-portal]. Další informace o klíčích najdete v tématu [zobrazení, kopírování a opětovné vytváření přístupových klíčů](manage-account.md#keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Jaké jsou oblasti, které může být nastavena PreferredLocations? 
Hodnota PreferredLocations lze nastavit pro žádného z regionů Azure, ve kterých je k dispozici Cosmos DB. Seznam dostupných oblastí najdete v tématu [oblastí Azure](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Je všechno, co I měli vzít v potaz při distribuci dat po celém světě prostřednictvím datových centrech Azure? 
Azure Cosmos DB přes všechny oblasti Azure, jak je uvedeno v nachází [oblastí Azure](https://azure.microsoft.com/regions/) stránky. Vzhledem k tomu, že je základní služby, má každý nový datacenter přítomnosti Azure Cosmos DB. 

Když nastavíte oblast, mějte na paměti, že Azure Cosmos DB respektuje suverénní a government cloudy. To znamená pokud vytvoříte účet v [svrchovaných oblast](https://azure.microsoft.com/global-infrastructure/), nelze replikovat mimo který [svrchovaných oblast](https://azure.microsoft.com/global-infrastructure/). Podobně nelze povolit replikaci do jiných umístění svrchovaných z mimo účtu. 

## <a name="develop-against-the-sql-api"></a>Vývoj rozhraní SQL API

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Jak spustit vývoj proti rozhraní SQL API?
Nejprve musíte zaregistrovat předplatné Azure. Jakmile si zaregistrujete předplatné Azure, můžete přidat kontejner rozhraní SQL API k předplatnému Azure. Pokyny k přidání účtu Azure Cosmos DB najdete v tématu [vytvoření účtu Azure Cosmos DB databáze](create-sql-api-dotnet.md#create-account). 

[Sady SDK](sql-api-sdk-dotnet.md) pro rozhraní .NET, Python, Node.js, JavaScript a Javu jsou k dispozici. Vývojáři mohou použít také [RESTful HTTP API](/rest/api/cosmos-db/) pracovat s prostředky Azure Cosmos DB z různých platforem a jazyků.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Může přistupovat ke profesionální některé připravených vzorků?
Ukázky pro rozhraní SQL API [.NET](sql-api-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](sql-api-nodejs-samples.md), a [Python](sql-api-python-samples.md) sady SDK jsou k dispozici na Githubu.


### <a name="does-the-sql-api-database-support-schema-free-data"></a>Podporuje rozhraní API pro SQL databáze data bez schémat?
Ano, rozhraní SQL API umožňuje aplikacím ukládat libovolné dokumenty JSON bez schématu definic nebo parametrů. Data jsou okamžitě k dispozici pro dotaz přes rozhraní dotazů SQL databáze Azure Cosmos.  

### <a name="does-the-sql-api-support-acid-transactions"></a>Podporuje rozhraní SQL API transakce ACID?
Ano, rozhraní SQL API podporuje transakce mezi dokumenty vyjádřené jako JavaScript uložených procedur a aktivačních událostí. Transakce jsou omezená na jeden oddíl v každé kolekci a spouštěny sémantice ACID jako "všechny nebo nic," izolované od ostatních souběžně spuštěných požadavků kód a uživatele. Pokud jsou výjimky vyvolány prostřednictvím spuštění serverový kód Javascriptové aplikace, celá transakce bude vrácena zpět. Další informace o transakcích najdete v tématu [databáze programu transakce](programming.md#database-program-transactions).

### <a name="what-is-a-collection"></a>Co je kolekce?
Kolekce je skupina dokumentů a jejich přidružené logiky Javascriptové aplikace. Kolekce je fakturovatelná entita, kde [náklady](performance-levels.md) je dáno propustnost a použít úložiště. Kolekce může mít rozsah jeden nebo více oddílů nebo serverů a můžete škálovat zvládaly prakticky neomezené objemy úložišť a propustnosti.

Kolekce jsou také entitami fakturace pro Azure Cosmos DB. Každá kolekce se fakturuje každou hodinu, podle zřízené propustnosti a využitého prostoru úložiště. Další informace najdete v tématu [Azure Cosmos DB ceny](https://azure.microsoft.com/pricing/details/cosmos-db/). 

### <a name="how-do-i-create-a-database"></a>Jak vytvořím databázi?
Databáze můžete vytvořit pomocí [portál Azure](https://portal.azure.com), jak je popsáno v [přidat do kolekce](create-sql-api-dotnet.md#create-collection), jeden z [SDK služby Azure Cosmos DB](sql-api-sdk-dotnet.md), nebo [rozhraní REST API](/rest/api/cosmos-db/). 

### <a name="how-do-i-set-up-users-and-permissions"></a>Jak nastavím uživatele a oprávnění?
Uživatele a oprávnění můžete vytvořit pomocí jedné z [Cosmos DB rozhraní API sady SDK](sql-api-sdk-dotnet.md) nebo [rozhraní REST API](/rest/api/cosmos-db/).  

### <a name="does-the-sql-api-support-sql"></a>Podporuje rozhraní SQL API SQL?
Dotazovací jazyk SQL nepodporuje účty rozhraní API pro SQL je vylepšená podmnožina dotazovacích funkcí podporovaným systémem SQL Server. Dotazovací jazyk Azure Cosmos DB SQL nabízí bohaté hierarchické a relační operátory a rozšiřitelnost prostřednictvím bázi jazyka JavaScript, uživatelsky definované funkce (UDF). Gramatika JSON umožňuje modelování dokumentů JSON ve formě stromů ve s popiskem uzly, které jsou používány Azure Cosmos DB automatických technikách indexování a dialektu dotazování SQL v Azure Cosmos DB. Informace o používání gramatiky SQL najdete v tématu [dotazu SQL] [ query] článku.

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>Podporuje rozhraní SQL API funkce agregace SQL?
Rozhraní SQL API podporuje agregace s nízkou latencí v jakémkoli měřítku prostřednictvím agregační funkce `COUNT`, `MIN`, `MAX`, `AVG`, a `SUM` prostřednictvím gramatiku SQL. Další informace najdete v tématu [agregační funkce](sql-api-sql-query.md#Aggregates).

### <a name="how-does-the-sql-api-provide-concurrency"></a>Jak rozhraní SQL API zajišťuje souběžnost?
Rozhraní SQL API podporuje optimistické řízení souběžného (přístupu OCC) prostřednictvím značek entit HTTP, neboli Etagů. Každé rozhraní API pro SQL prostředků má ETag a značku ETag je nastaven na serveru pokaždé, když je aktualizován dokument. Všechny zprávy odpovědi jsou součástí hlavičku ETag a aktuální hodnotu. Značky etag binárním rozsáhlým použít s hlavičku If-Match umožňuje serveru se rozhodnout, zda mají být aktualizovány prostředku. Hodnota If-Match je hodnota ETag, která má být zkontrolován. Pokud hodnota ETag odpovídá serveru hodnota ETag, prostředek je aktualizována. Pokud už je ETag aktuální, server odmítne operaci s "HTTP 412 Precondition selhání" kód odpovědi. Klient pak znovu načte prostředek získat aktuální hodnota ETag pro prostředek. Kromě toho značky etag binárním rozsáhlým umožňuje s hlavičku If-None-Match určit, jestli je potřeba znovu načtěte prostředku.

Chcete-li použít optimistickou metodu souběžného v rozhraní .NET, použijte [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) třídy. Ukázku .NET naleznete v části [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) v DocumentManagement ukázce na Githubu.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Jak se provádí transakce v rozhraní SQL API?
Rozhraní SQL API podporuje transakce integrované do jazyka prostřednictvím JavaScript uložených procedur a aktivačních událostí. Všechny databázové operace ve skriptech se spouští v izolaci snímku. Pokud je kolekci s jedním oddílem, provádění je vymezen na kolekci. Pokud je kolekce rozdělena na oddíly, provádění je vymezen na dokumenty se stejnou hodnotou klíče oddílu v rámci kolekce. Na začátku transakce se pořídí snímek verzí dokumentů (ETagy) a k potvrzení dojde pouze v případě, že skript uspěje. Pokud JavaScript vyvolá chybu, transakce se vrátí zpět. Další informace najdete v tématu [programování v jazyce JavaScript na straně serveru pro databázi Azure Cosmos](programming.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Jak můžete I hromadného vložení dokumenty do Cosmos DB?
Vám může hromadného vložení dokumenty do Azure Cosmos DB v jednom z následujících způsobů:

* Nástroj vykonavatele hromadně, jak je popsáno v [knihovny .NET vykonavatele pomocí hromadné](bulk-executor-dot-net.md) a [pomocí hromadné vykonavatele Java knihovny](bulk-executor-java.md)
* Nástroj pro migraci dat, jak je popsáno v [nástroj pro migraci databáze pro databázi Azure Cosmos](import-data.md).
* Uložené procedury, jak je popsáno v [programování v jazyce JavaScript na straně serveru pro databázi Azure Cosmos](programming.md).

### <a name="i-have-setup-my-collection-to-use-lazy-indexing-i-see-that-my-queries-do-not-return-expected-results"></a>Instalační program mám mé kolekce používat Opožděné indexování, zobrazuje, že dotazy nevrátí očekávané výsledky. 
Jak je popsáno v části indexování, opožděné indexování může způsobit toto chování. Vždy byste měli používat, konzistentní indexování pro všechny aplikace. 


### <a name="does-the-sql-api-support-resource-link-caching"></a>Využívá rozhraní SQL API podporu prostředků odkaz ukládání do mezipaměti?
Ano, protože Azure Cosmos DB je služba RESTful, odkazy na zdroje jsou neměnné a mohou být uloženy v mezipaměti. Rozhraní SQL API klientů můžete zadat hlavičku "If-None-Match" pro čtení pro všechny prostředků jako dokumentu nebo kolekci a pak aktualizujte své místní kopie po změně verze serveru.

### <a name="is-a-local-instance-of-sql-api-available"></a>Je k dispozici místní instance systému SQL API?
Ano. [Emulátoru DB Cosmos Azure](local-emulator.md) poskytuje zachováním emulace služby Cosmos DB. Podporuje funkce, které jsou shodné s Azure Cosmos databáze, včetně podpory pro vytváření a dotazování dokumentů JSON, zřizování a škálování kolekce a provádění uložené procedury a triggery. Můžete vyvíjet a testovat aplikace pomocí emulátoru DB Cosmos Azure a jejich nasazení do Azure v globálním měřítku tím, že změníte koncového bodu připojení pro Azure Cosmos DB jednu konfiguraci.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>Proč se dlouho s plovoucí desetinnou čárkou v dokumentu zaokrouhlené při pohledu z Průzkumníku dat na portálu. 
Toto je omezení jazyka JavaScript. JavaScript používá jako zadaný v IEEE 754 čísla s plovoucí desetinnou čárkou formát s dvojitou přesností a bezpečně může představovat hodnoty mezi-(253 - 1) a 253-1 (tj, 9007199254740991) jenom.

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Kde jsou v hierarchii objekt povoleno oprávnění?

Vytváření oprávnění pomocí ResourceTokens je povoleno na úrovni kolekce a na jeho následníky (jako jsou dokumenty, přílohy). To znamená, že pokusu o vytvoření oprávnění v databázi nebo úrovni účtu není aktuálně povoleno.


## <a name="develop-against-the-api-for-mongodb"></a>Vývoj pro rozhraní API pro MongoDB
### <a name="what-is-the-azure-cosmos-db-api-for-mongodb"></a>Co je Azure API DB Cosmos pro MongoDB?
Rozhraní API Azure Cosmos DB pro MongoDB je vrstvu kompatibility, která umožňuje aplikacím snadno a transparentně komunikovat s nativní databázový stroj Azure Cosmos DB pomocí existující, podporované komunity Apache MongoDB rozhraní API a ovladačů. Vývojáři teď můžete použít existující řetězy MongoDB nástroje a dovednosti vytvářet aplikace, které využívá výhod Azure Cosmos DB. Vývojáři těžit z jedinečných funkcích Azure Cosmos DB, mezi které patří automatické indexování, zálohování údržby, smlouvy o úrovni finančně zálohovány služeb (SLA) a tak dále.

### <a name="how-do-i-connect-to-my-api-for-mongodb-database"></a>Připojení k mé rozhraní API pro databázi MongoDB
Nejrychlejší způsob, jak připojit k rozhraní API služby Azure Cosmos DB pro MongoDB má k head přes [portál Azure](https://portal.azure.com). Přejděte na svůj účet a potom v levém navigační nabídce klikněte na **rychlý Start**. Rychlý Start je nejlepší způsob, jak získat fragmenty kódu pro připojení k vaší databázi. 

Azure Cosmos DB vynucuje vysokými nároky na zabezpečení a standardy. Účty Azure Cosmos DB vyžadují ověřování a zabezpečenou komunikaci prostřednictvím protokolu SSL, takže je nutné použít TLSv1.2.

Další informace najdete v tématu [připojit k rozhraní API pro databázi MongoDB](connect-mongodb-account.md).

### <a name="are-there-additional-error-codes-for-an-api-for-mongodb-database"></a>Existují další kódy chyb pro rozhraní API pro databázi MongoDB?
Kromě běžné kódy chyb MongoDB rozhraní API MongoDB má svou vlastní specifické chybové kódy:


| Chyba               | Kód  | Popis  | Řešení  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Celkový počet jednotek žádosti použití překročilo četnost zřízené požadavek jednotky pro kolekci a byla omezena. | Vezměte v úvahu škálování propustnost přiřazené kontejner nebo sadu kontejnery z Azure portálu nebo Probíhá opakování znovu. |
| ExceededMemoryLimit | 16501 | Jako víceklientské služby překročil operaci přidělení paměti klienta. | Snížit rozsah operaci prostřednictvím konkrétnější kritéria dotazu nebo se obraťte na podporu [portál Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br>Příklad:  *&nbsp; &nbsp; &nbsp; &nbsp;db.getCollection('users').aggregate ([<br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;{$match: {název: "Andy"}}, <br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;{$sort: {stáří: -1} }<br>&nbsp;&nbsp;&nbsp;&nbsp;])*) |

## <a name="develop-with-the-table-api"></a>Vývoj s tabulkou rozhraní API

### <a name="how-can-i-use-the-table-api-offering"></a>Jak můžete použít nabídku tabulky API? 
Rozhraní API služby Azure DB Cosmos tabulka je k dispozici v [portál Azure][azure-portal]. Nejprve musíte zaregistrovat předplatné Azure. Poté, co jste se přihlásili, můžete k předplatnému Azure přidat účet rozhraní API služby Azure Cosmos DB tabulky a poté přidejte tabulky k vašemu účtu. 

Podporované jazyky a přidružené rychlé spuštění v naleznete [Úvod do rozhraní API služby Azure Cosmos DB tabulky](table-introduction.md).

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Je nutné používat rozhraní API tabulky novou sadu SDK? 
Ne, existující úložiště, které by měl sady SDK i nadále fungovat. Nedoporučujeme ale, že jeden vždy získá nejnovější sady SDK nejlepší podpory a v mnoha případech vynikající výkon. Zobrazit seznam dostupných jazyků v [Úvod do rozhraní API služby Azure Cosmos DB tabulky](table-introduction.md).

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Pokud není tabulka API identické s Azure Table storage chování?
Existují určité rozdíly chování, které uživatelé pocházejících z Azure Table storage, kteří chtějí vytvářet tabulky s rozhraním API pro Azure Cosmos DB tabulky měli vědět:

* Azure Cosmos DB tabulky API používá model rezervované kapacity za účelem zajištění zaručenou výkon, ale to znamená, že jeden platí za kapacitu při vytvoření tabulky, i v případě, že se nepoužívají kapacitu. S Azure Table storage jeden pouze platí za kapacitu, který se používá ve skutečnosti. Tento pomůže vysvětlit, proč tabulky API nabízejí že 10 ms číst a 15 ms Azure Table storage nabízí 10 druhý SLA zapisovat SLA na 99th percentil. Ale v důsledku toho s tabulkami tabulky rozhraní API, i prázdný tabulky bez všechny žádosti, náklady na peníze, aby se zajistilo, že je k dispozici pro zpracovat žádné požadavky na ně ve smlouvě SLA kapacitu nabízené sítěmi Azure Cosmos DB.
* Výsledky dotazu vrácený rozhraní API tabulky nejsou seřazeny ve klíče pořadí oddílu klíč či řádku, jako jsou ve službě Azure Table storage.
* Řádek klíčů může být pouze až 255 bajtů
* Dávky může obsahovat pouze až 2 MB
* CORS není aktuálně podporováno.
* Názvy tabulek ve službě Azure Table storage nejsou malá a velká písmena, ale jsou v rozhraní API služby Azure Cosmos DB tabulky
* Některé z Azure Cosmos DB interní formátů kódování informace, například binární pole nejsou aktuálně efektivní, jako jeden mohly líbit. Proto to může způsobit neočekávané omezení na velikost dat. Například jeden nelze používají úplné 1 MB tabulka entity k uložení binární data, protože kódování zvyšuje velikost data.
* Název vlastnosti entity Id aktuálně není podporována
* TableQuery TakeCount není omezen na 1000

Z hlediska rozhraní REST API existuje několik možností koncových bodů nebo dotazu, které nepodporuje rozhraní API služby Azure Cosmos DB tabulky:
| Metodu nebo metody REST | Možnost koncový bod/dotazu REST | Adresy URL dokumentu | Vysvětlení |
| ------------| ------------- | ---------- | ----------- |
| GET A PUT | /? restype =service@comp= vlastnosti| [Nastavit vlastnosti služby Table](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) a [získat vlastnosti služby Table](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) | Tento koncový bod se používá k nastavení pravidla CORS, konfigurace úložiště analýzy a nastavení protokolování. Protokolování a analýza jsou zpracovávány jinak v Azure DB Cosmos než úložiště tabulek Azure a CORS není aktuálně podporována. |
| MOŽNOSTI | / < název tabulky zdroje > | [Požadavek tabulky před letu CORS](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | Toto je část CORS, která Azure Cosmos DB v současné době nepodporuje. |
| GET | /? restype =service@comp= statistiky | [Získat statistiky služby Table](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | Poskytuje informace o tom, jak rychle replikuje data mezi primární a sekundární repliky. Toto není nutné Cosmos DB jako replikace je součástí zápisy. |
| GET A PUT | /mytable? comp = seznamu acl | [Získejte tabulku seznamu ACL](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) a [nastavit tabulky seznamu ACL](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) | To získá a nastaví zásady uložené přístupu používat ke správě podpisy sdíleného přístupu (SAS). I když SAS se podporuje, jsou nastavit a spravovat jinak. |

Kromě toho rozhraní API služby Azure Cosmos DB tabulka podporuje jenom formátu JSON, není ATOM.

Zatímco Azure Cosmos DB podporuje sdíleného přístupu podpisy (SAS) existují určité zásady, které nepodporuje, konkrétně události související se operace správy, jako je například práva k vytvoření nové tabulky.

Pro .NET SDK konkrétně, existují některé třídy a metody, které databázi Cosmos Azure v současné době nepodporuje.

| Třída | Nepodporované – metoda |
|-------|-------- |
| CloudTableClient | \*ServiceProperties * |
|                  | \*ServiceStats * |
| CloudTable | Měli * |
|            | GetPermissions * |
| TableServiceContext | * (Tato třída je ve skutečnosti zastaralý) |
| TableServiceEntity | " " |
| TableServiceExtensions | " " |
| TableServiceQuery | " " |

Pokud některá z těchto rozdílů problém pro svůj projekt obraťte [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com) a dejte nám vědět.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Jak poskytují zpětnou vazbu o SDK nebo chyby?
Váš názor můžete sdílet v některém z následujících způsobů:

* [Uservoice](https://feedback.azure.com/forums/599062-azure-cosmos-db-table-api)
* [Fórum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Přetečení zásobníku](http://stackoverflow.com/questions/tagged/azure-cosmosdb). Přetečení zásobníku je nejvhodnější pro dotazy programování. Zkontrolujte, že váš dotaz je [na téma](https://stackoverflow.com/help/on-topic) a [zadejte tolik podrobnosti nejdříve provádění na otázku, zrušte zaškrtnutí a který jej](https://stackoverflow.com/help/how-to-ask).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Co je připojovací řetězec, který je nutné použít pro připojení k rozhraní API tabulky?
Připojovací řetězec je:
```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```
Na stránce připojovací řetězec na portálu Azure můžete získat připojovací řetězec. 

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Jak přepsat nastavení konfigurace pro žádost o možnosti sady .NET SDK pro rozhraní API tabulky?
Informace o nastavení konfigurace najdete v tématu [možnosti Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Některá nastavení jsou zpracovávány o CreateCloudTableClient metoda a dalších pomocí souboru app.config v sekci appSettings klientské aplikace.

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Existují změny pro zákazníky, kteří používají je stávající úložiště Azure Table sady SDK?
Žádné. Neexistují žádné změny pro existující nebo nové zákazníky, kteří používají je stávající úložiště Azure Table sady SDK. 

### <a name="how-do-i-view-table-data-that-is-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Zobrazení tabulky dat, které jsou uložené v Azure Cosmos DB pro použití s rozhraním API pro tabulku? 
Na portálu Azure můžete procházet data. Můžete také použít kódu rozhraní API tabulky nebo uvedené v další odpovědí nástroje. 

### <a name="which-tools-work-with-the-table-api"></a>Které nástroje pracovat s rozhraním API pro tabulku? 
Můžete použít [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

Nástroje a flexibilně trvat připojovací řetězec ve formátu určeném dříve může podporovat nové rozhraní API tabulky. Seznam nástroje tabulky je uvedený na [Azure Storage Client Tools](../storage/common/storage-explorers.md) stránky. 

### <a name="is-the-concurrency-on-operations-controlled"></a>Je na operace řízené souběžnost?
Ano, optimistickou metodu souběžného je k dispozici prostřednictvím použití mechanismu značka ETag. 

### <a name="is-the-odata-query-model-supported-for-entities"></a>Se model dotazů OData podporuje pro entity? 
Ano, podporuje rozhraní API tabulka dotazu OData a dotazů LINQ. 

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Můžete připojit k Azure Table Storage a Azure Cosmos DB tabulky API vedle sebe ve stejné aplikaci? 
Ano, můžete připojit pomocí dvou samostatných instancí CloudTableClient, každý odkazující na vlastní identifikátor URI prostřednictvím připojovací řetězec.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Jak mohu migrovat existující aplikaci Azure Table storage s touto nabídkou?
[AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) a [nástroj pro migraci dat Azure Cosmos DB](import-data.md) jsou obě podporována.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Jak je rozšíření velikost úložiště, provádí pro tuto službu, je-li například spustit s *n* GB dat a data se v průběhu času rozšiřovat 1 TB? 
Azure Cosmos DB určená k poskytování neomezené úložiště prostřednictvím použití vodorovné škálování. Službu můžete sledovat a efektivně zvýšit úložiště. 

### <a name="how-do-i-monitor-the-table-api-offering"></a>Jak se monitorování nabídku tabulky API?
Můžete použít rozhraní API tabulky **metriky** podokně ke sledování požadavků a využití úložiště. 

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Jak vypočítat propustnost, které můžu vyžadovat?
Odhadu kapacity můžete použít k výpočtu TableThroughput, které je nutné pro operace. Další informace najdete v tématu [jednotky žádosti odhad a úložiště dat](https://www.documentdb.com/capacityplanner). Obecně platí může představovat vaší entity jako JSON a zadejte čísla pro operace. 

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Lze použít sadu SDK API tabulky místně pomocí emulátoru?
V tuto chvíli to není možné.

### <a name="can-my-existing-application-work-with-the-table-api"></a>Můžete Moje existující aplikace pracovat s rozhraním API pro tabulku? 
Ano, je podporováno stejné rozhraní API.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-do-not-want-to-use-the-table-api-features"></a>Je potřeba migrovat svoje existující aplikace Azure Table storage k sadě SDK, pokud není chcete používat funkce rozhraní API tabulky?
Ne, můžete vytvořit a používat existující prostředky Azure Table storage bez přerušení jakéhokoli druhu. Ale pokud použijete rozhraní API tabulky, můžete nemohou využívat automatické index, možnost Další konzistence nebo globální distribuční. 

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-multiple-regions-of-azure"></a>Jak přidat replikace dat v tabulce API nad několika oblastmi Azure?
Můžete použít portál Azure Cosmos DB [nastavení globální replikace](tutorial-global-distribution-sql-api.md#portal) přidávání oblastí, které jsou vhodné pro vaši aplikaci. K vývoji globálně distribuované aplikace, měli byste také přidat aplikaci s PreferredLocation informací nastavenou na místní oblast pro zajištění nízkou latenci pro čtení. 

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Změna zápisu primární oblast pro účet v tabulce rozhraní API
V podokně portálu Azure Cosmos DB globální replikace můžete přidat oblast a potom převzetí služeb při selhání požadované oblasti. Pokyny najdete v tématu [vývoj s více oblast Azure Cosmos DB účty](regional-failover.md). 

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Jak lze nakonfigurovat Moje upřednostňované čtení oblasti pro s nízkou latencí při I distribuci svá data? 
Pomoc při čtení z místního umístění, použijte PreferredLocation klíč v souboru app.config. Pro existující aplikace rozhraní API tabulky vrátí chybu, pokud je nastavená LocationMode. Tento kód odeberte, protože rozhraní API tabulky převezme tyto informace ze souboru app.config. Další informace najdete v tématu [možnosti Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Jak by měla myslíte o úrovně konzistence v tabulce rozhraní API? 
Azure Cosmos DB poskytuje dobře odůvodněnou kompromis mezi konzistencí, dostupností a latencí. Azure Cosmos DB nabízí pět úrovní konzistence pro vývojáře tabulky rozhraní API, abyste mohli vybrat model správné konzistence na úrovni tabulky a provádět požadavky na jednotlivé při dotazování data. Když se klient připojí, může však určovat úrovní konzistence. Můžete změnit úroveň prostřednictvím consistencyLevel argument CreateCloudTableClient. 

Rozhraní API tabulka poskytuje nízkou latencí čte s "načíst vlastní zápisy" konzistence typu Bounded prošlostí jako výchozí. Další informace najdete v tématu [úrovně konzistence](consistency-levels.md). 

Ve výchozím nastavení Azure Table storage poskytuje silnou konzistenci v rámci oblasti a Eventual konzistenci sekundárního umístění. 

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Nabízí rozhraní API služby Azure Cosmos DB tabulky další úrovně konzistence než Azure Table storage?
Ano, informace o tom, jak využívat distribuovaná povaha Azure Cosmos DB najdete v tématu [úrovně konzistence](consistency-levels.md). Protože záruky jsou k dispozici pro úrovně konzistence, můžete je používat s jistotou. Další informace najdete v tématu [možnosti Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Pokud je povoleno globální distribuční, jak dlouho trvá replikaci dat?
Azure Cosmos DB potvrdí data spolehlivě v místní oblast a nabízených oznámení data do jiných oblastí okamžitě v řádu milisekundách. Tato replikace je závislá pouze na času jejich návratu (požadavku) datového centra. Další informace o funkci globální distribuční databáze Cosmos Azure najdete v tématu [Cosmos databázi Azure: Služba globálně distribuované databáze v Azure](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>Můžete změnit úroveň konzistence požadavků na čtení?
S Azure Cosmos databáze můžete nastavit úroveň konzistence na úrovni kontejneru (pro tabulku). Pomocí sady .NET SDK, můžete změnit úroveň tím, že poskytuje hodnotu pro klíč TableConsistencyLevel v souboru app.config. Možné hodnoty jsou: silného typu s ohraničenou Prošlostí, relace, konzistentní předpony a Eventual. Další informace najdete v tématu [úrovně konzistence přizpůsobitelné dat v Azure Cosmos DB](consistency-levels.md). Klíče cílem je, že nelze nastavit konzistence žádost o úrovni na větší než nastavení pro tabulku. Například nelze nastavit úroveň konzistence v tabulce v Eventual a úroveň konzistence požadavek na silné. 

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Jak rozhraní API tabulky zpracovávat převzetí služeb při selhání Pokud oblast ocitne mimo provoz? 
Rozhraní API tabulka využívá globálně distribuované platformu Azure Cosmos DB. Aby se zajistilo, že vaše aplikace může tolerovat výpadků datacenter, povolte alespoň jeden další oblast pro účet na portálu Azure Cosmos DB [vývoj s více oblast Azure Cosmos DB účty](regional-failover.md). Můžete nastavit prioritu oblasti pomocí portálu [vývoj s více oblast Azure Cosmos DB účty](regional-failover.md). 

Můžete přidat jako v mnoha oblastech jako pro účet a řídit, kde ji můžete převzetí služeb při selhání tím, že poskytuje prioritu převzetí služeb při selhání. Samozřejmě používat databázi, budete muset zadat aplikaci existuje příliš. Pokud tak učiníte, nebudou vaši zákazníci se setkávají výpadku. [Nejnovější klient .NET SDK](table-sdk-dotnet.md) je automaticky homing dalších sadách SDK, nejsou však. To znamená může zjistit oblast, která je mimo provoz a automaticky převzetí služeb při selhání pro novou oblast.

### <a name="is-the-table-api-enabled-for-backups"></a>Je rozhraní API tabulky povolené pro zálohování?
Ano, rozhraní API tabulka využívá platformu Azure Cosmos databáze pro zálohování. Zálohy jsou vytvářeny automaticky. Další informace najdete v tématu [Online zálohování a obnovení s Azure Cosmos DB](online-backup-and-restore.md).

 
### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>Rozhraní API tabulky všechny atributy indexu entity ve výchozím nastavení?
Ano, jsou všechny atributy entity indexovaný ve výchozím nastavení. Další informace najdete v tématu [Cosmos databázi Azure: indexování zásady](indexing-policies.md). 

### <a name="does-this-mean-i-do-not-have-to-create-multiple-indexes-to-satisfy-the-queries"></a>Neodpovídá, znamená to není nutné vytvořit více indexů, aby pokryl dotazy? 
Ano, rozhraní API služby Azure Cosmos DB tabulka poskytuje automatické indexování všechny atributy bez jakékoli definice schématu. Tato automatizace uvolní vývojáři zaměřit se na aplikace a nikoli na vytvoření indexu a správu. Další informace najdete v tématu [Cosmos databázi Azure: indexování zásady](indexing-policies.md).

### <a name="can-i-change-the-indexing-policy"></a>Můžete změnit zásady indexování?
Ano, můžete změnit zásady indexování tím, že poskytuje definici indexu. Další informace najdete v tématu [možnosti Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Budete muset správně kódování a vyhnuli nastavení. 

pro sady jiný - .NET SDK zásady indexování lze nastavit pouze na portál **Průzkumníku dat**, přejděte do určité tabulky, které chcete změnit a potom přejděte na **škálování & nastavení**-> zásady indexování proveďte požadovanou změnu a potom **Uložit**.

Ze sady SDK rozhraní .NET můžete odeslat v souboru app.config:
```
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

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Azure DB Cosmos jako platformu zdá se, že máte spoustu možností, jako je například řazení, agregace, hierarchie a další funkce. Můžete přidávat tyto funkce rozhraní API tabulky? 
Rozhraní API tabulka poskytuje stejné funkce dotazu jako Azure Table storage. Azure Cosmos DB podporuje také řazení, agregace, geoprostorové dotazy, hierarchie a širokou škálu integrovaných funkcí. Poskytujeme další funkce v rozhraní API tabulky v aktualizaci budoucí služby. Další informace najdete v tématu [dotazy SQL](sql-api-sql-query.md).
 
### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Kdy je třeba změnit TableThroughput pro rozhraní API tabulky?
Měli byste změnit TableThroughput, pokud platí některá z následujících podmínek:
* Provádění extrakce, transformace a načítání (ETL) dat, nebo chcete nahrát velké množství dat v krátkém čase. 
* Budete potřebovat další propustnost z kontejneru nebo ze sady kontejnerů v back-end. Například můžete zjistit, že používané propustnost je větší než zřízené propustnosti a jste jsou získávání omezené. Další informace najdete v tématu [sadu propustnost pro Azure Cosmos DB kontejnery](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Je možné škálovat nebo snižovat propustnost tabulka tabulky API? 
Ano, můžete na portálu Azure Cosmos DB škálování podokně škálování propustnost. Další informace najdete v tématu [sadu propustnost](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Je výchozí pro nově zřízeného tabulky nastavit TableThroughput?
Ano, pokud TableThroughput prostřednictvím app.config nepotlačí a nepoužívejte kontejner předem vytvořené v Azure Cosmos DB, služba vytvoří tabulku s propustností 400.
 
### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Je k dispozici žádné změně ceny pro stávající zákazníky služby Azure Table storage?
Žádné. Neexistuje žádná změna v ceny pro stávající zákazníky služby úložiště Azure Table. 

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Výpočtu ceny pro rozhraní API tabulky 
Cena závisí na přidělené TableThroughput. 

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Jak pracovat, žádné omezení rychlosti na tabulky v tabulce API nabídky? 
Pokud rychlost požadavků překročí kapacitu zřízené propustnosti pro základní kontejner nebo sadu kontejnery, dojde k chybě a sady SDK opakování volání použitím zásady opakování.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Proč je muset zvolit propustnost kromě PartitionKey a RowKey využívat výhod nabídku API tabulky databáze Cosmos Azure?
Azure Cosmos DB nastaví výchozí propustnost pro váš kontejner, pokud nezadáte jednoho v souboru app.config nebo přes portál. 

Azure Cosmos DB poskytuje záruku výkonu a latencí a s horní meze operaci. Tato záruka je možné, pokud modul můžete vynutit zásady správného řízení na operace klienta. Nastavení TableThroughput zajišťuje získat zaručenou propustnosti a latence, protože platforma si vyhrazuje tato kapacita a zaručí provozní úspěch. 

Pomocí specifikace propustnost pružně můžete využívat sezónnosti vaší aplikace, vyhovovaly propustnost a ušetřili náklady.

### <a name="azure-table-storage-has-been-very-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-have-not-performed-a-single-transaction-or-stored-anything-can-you-please-explain"></a>Azure Table storage bylo pro mě nejlepší, velmi málo, protože I platit pouze pro ukládání dat a můžu zřídka dotazu. Nabídky Azure Cosmos DB tabulky API zdá se, že se poplatků mi, i když I nebyly provést jedné transakci nebo nic uložené. Můžete je vysvětlete?

Azure Cosmos DB je navržený jako globálně distribuované, na základě smlouvy SLA systému se záruky dostupnosti, latence a propustnosti. Při rezervaci propustnost v Azure Cosmos DB tak, aby zajistil, na rozdíl od jiných systémů propustnost. Azure Cosmos DB poskytuje další funkce, které zákazníci požadovali, jako je například sekundární indexy a globální distribuci.  

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Nikdy zobrazí úplný kvótu"oznámení (což znamená, že oddíl je úplná) při načítání dat do Azure Table storage. S rozhraním API pro tabulky zobrazí se tato zpráva. Tato nabídka je omezení mi a vynucení mi změnit mé existující aplikace?

Azure Cosmos DB je systém na základě smlouvy o úrovni služeb, který poskytuje neomezené škálování záruky latence, propustnost, dostupnosti a konzistence. K zajištění výkonu zaručenou premium, ujistěte se, že velikost dat a index jsou spravovat a škálovatelné. 10 GB limitu počet entit nebo počet položek na klíč oddílu je zajistit, že můžeme poskytnout vynikající výkon vyhledávání a dotazů. K zajištění, dobře škáluje aplikace i pro Azure Storage, doporučujeme, aby vám *není* vytvořit aktivní oddíl ukládání všechny informace v jednom oddílu a dotazování ho. 

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Proto PartitionKey a RowKey se stále vyžadují s rozhraním API pro tabulku? 
Ano. Protože plochy rozhraní API tabulka je podobná Azure Table storage SDK, klíč oddílu poskytuje účinný způsob, jak distribuovat data. Klíč řádku je jedinečný v rámci oddílu. Klíč řádku musí být přítomen a nemůže mít hodnotu null jako standardní sady SDK. Délka RowKey je 255 bajtů a délka PartitionKey je 1 KB. 

### <a name="what-are-the-error-messages-for-the-table-api"></a>Jaké jsou chybové zprávy pro rozhraní API tabulky?
Azure Table storage a rozhraní API služby Azure Cosmos DB tabulky pomocí stejné sad SDK, takže většinu chyb budou stejné.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Proč I získat omezeny při pokusu vytvořit mnoho tabulek jedna po druhé v rozhraní API tabulky?
Azure Cosmos DB je systém na základě smlouvy o úrovni služeb, který poskytuje latence, propustnost, dostupnost a konzistence záruky. Protože je zřízená systému, si vyhrazuje prostředky k zajištění těchto požadavků. Rychlé rychlost vytváření tabulek je zjištěna a omezení. Doporučujeme podívat rychlost vytváření tabulek a snížit na méně než 5 za minutu. Mějte na paměti, že rozhraní API tabulky je zřízená systém. V okamžiku, zřídíte začne platit pro ni. 

## <a name="develop-against-the-graph-api"></a>Vývoj proti Graph API
### <a name="how-can-i-apply-the-functionality-of-graph-api-to-azure-cosmos-db"></a>Tom, jak můžete použít funkci rozhraní Graph API k databázi Cosmos Azure?
Rozšíření knihovny můžete použít funkci rozhraní Graph API. V této knihovně se nazývá grafy Microsoft Azure a je k dispozici na NuGet. 

### <a name="it-looks-like-you-support-the-gremlin-graph-traversal-language-do-you-plan-to-add-more-forms-of-query"></a>Zdá se, podporu jazyka traversal Gremlin grafu. Máte v plánu přidat další formuláře dotazu?
Ano, plánujeme přidat v budoucnu další mechanismy pro dotaz. 

### <a name="how-can-i-use-the-new-graph-api-offering"></a>Jak můžete použít novou nabídku rozhraní Graph API? 
Chcete-li začít, proveďte [rozhraní Graph API](../cosmos-db/create-graph-dotnet.md) úvodní článek.

<a id="cassandra"></a> 
## <a name="develop-with-the-apache-cassandra-api-preview"></a>Vývoj s rozhraním API Cassandra Apache (preview)

### <a name="what-is-the-protocol-version-supported-in-the-private-preview-is-there-a-plan-to-support-other-protocols"></a>Co je podporováno v privátní preview verzi protokolu? Je k dispozici plán pro podporu dalších protokolů?
Apache Cassandra rozhraní API pro Azure Cosmos DB podporuje dnes CQL verze 4. Pokud máte zpětnou vazbu o podpoře jiné protokoly, dejte nám vědět prostřednictvím [názorů na webu uservoice](https://feedback.azure.com/forums/263030-azure-cosmos-db) nebo e-mailovou zprávu na [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com). 

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Proč je výběr požadavek propustnost pro tabulku?
Azure Cosmos DB nastaví výchozí propustnost pro váš kontejner podle kde vytvořit tabulku ze - portálu nebo CQL. Azure Cosmos DB poskytuje záruku výkonu a latencí a s horní meze operaci. Tato záruka je možné, pokud modul můžete vynutit zásady správného řízení na operace klienta. Nastavení propustnosti zajišťuje získat zaručenou propustnosti a latence, protože platforma si vyhrazuje tato kapacita a zaručí operace byla úspěšná. Propustnost těžit z sezónnosti vaší aplikace a ušetřili náklady, můžete změnit pružně.

Koncept propustnost je podrobně popsaný [jednotky žádosti v Azure Cosmos DB](request-units.md) článku. Propustnost pro tabulku je rovnoměrně rozdělené mezi základní fyzické oddíly.  

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>Co je výchozí RU/s tabulky při vytvoření prostřednictvím CQL? Co když je nutné ho změnit?
Azure Cosmos DB jako měny používá jednotek žádosti za sekundu (RU/s) pro zajištění propustnost. Tabulky, které jsou vytvořené pomocí CQL mít 400 RU. Z portálu, můžete změnit RU. 

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

### <a name="what-happens-when-throughput-is-exceeded"></a>Co se stane, pokud je překročena propustnost? 
Azure Cosmos DB poskytuje záruku výkonu a latencí a s horní meze operaci. Tato záruka je možné, pokud modul můžete vynutit zásady správného řízení na operace klienta. To je možné, podle nastavení propustnosti, které zajišťuje získat zaručenou propustnosti a latence, protože platforma si vyhrazuje tato kapacita a zaručí operace byla úspěšná. Když se překročí tuto kapacitu zobrazí přetížené chybová zpráva oznamující, že vaše kapacita byla překročena. 0x1001 přetížený: požadavek nelze zpracovat, protože "Rychlost požadavků je velká". V tomto okamžiku je nezbytně nutné zobrazit jaké operace a jejich svazku způsobí, že tento problém. Můžete získat představu o spotřebované kapacitě větší než zřízená kapacita s metriky na portálu. Pak je potřeba zajistit kapacity obsazením téměř rovnoměrně mezi všechny základní oddíly. Pokud se zobrazí, je většina propustnost spotřebovávají jeden oddíl, máte zkosení úlohy. 

Metriky, které jsou k dispozici, zobrazit, jak propustnosti se používá v hodin, dnů a za sedm dní, napříč oddíly nebo agregace. Další informace najdete v tématu [sledování a ladění pomocí metriky v Azure Cosmos DB](use-metrics.md).

Diagnostické protokoly jsou vysvětlené v [protokolování diagnostiky Azure Cosmos DB](logging.md) článku.

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>Nemá primární klíče mapy pro koncept klíče oddílu databáze Cosmos Azure?
Ano, klíč oddílu se používá k umístění entity správné místo. V Azure Cosmos DB je použít k vyhledání právo logický oddíl, který je uložený na fyzickém oddílu. Rozdělení koncept a podrobně [oddílu a škálování v Azure Cosmos DB](partition-data.md) článku. Základní proveďte rychle tady je, že logický oddíl dnes by neměl překročit omezení na 10 GB. 

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>Co se stane, když se zobrazí úplný kvótu"oznámení o tom, že oddíl je úplná?
Azure Cosmos DB je systém na základě smlouvy o úrovni služeb, který poskytuje neomezené škálování záruky latence, propustnost, dostupnosti a konzistence. Cassandra API umožňuje příliš neomezené úložiště dat. Tato neomezené úložiště je založena na vodorovné škálování dat pomocí dělení jako klíče koncept. Rozdělení koncept a podrobně [oddílu a škálování v Azure Cosmos DB](partition-data.md) článku.

10 GB limitu počtu entit nebo položek na logický oddíl měli byste dodržovat. Aby se dobře škáluje vaší aplikace, doporučujeme, aby vám *není* vytvořit aktivní oddíl ukládání všechny informace v jednom oddílu a dotazování ho. Tato chyba může obsahovat jenom přijde, pokud se vám data nesouměrně rozdělí –, můžete je mít velké množství dat pro jeden oddíl klíč – tj, více než 10 GB. Můžete najít distribuci dat pomocí portálu pro úložiště. Způsob, jak opravit tuto chybu je recrete tabulky a zvolte granulární primární (klíč oddílu), což umožňuje lepší distribuci dat.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>Je možné používat Cassandra rozhraní API jako úložiště hodnota klíče s miliony nebo až miliardy klíče jednotlivých oddílů?
Neomezená data můžete uložit Azure Cosmos DB podle škálování úložiště. Toto je nezávislé na propustnost. Ano můžete vždy použít rozhraní API Cassandra k ukládání a načítání hodnot klíče nebo zadáním klíče správné primární nebo oddílu. Tyto jednotlivé klíče získat vlastní logický oddíl a nacházejí na fyzickém oddílu bez problémů. 

### <a name="is-it-possible-to-create-multiple-tables-with-apache-cassandra-api-of-azure-cosmos-db"></a>Je možné vytvořit více tabulek s Apache Cassandra rozhraní API služby Azure Cosmos DB?
Ano, je možné Krétě více tabulek s Apache Cassandra API. Každá z těchto tabulek je považována za jednotku pro propustnost a úložiště. 

### <a name="is-it-possible-to-create-multiple-tables-in-succession"></a>Je možné vytvořit více tabulek po sobě?
Azure Cosmos DB je systém řídí prostředků pro data a řízení roviny aktivity. Kontejnery jako kolekce tabulek jsou runtime entit, které jsou zřízené pro daný kapacity propustnosti. Vytvoření těchto kontejnerů rychle po sobě není očekávaný aktivity a omezení. Pokud máte testy, které vyřaďte nebo vytváření tabulek okamžitě – zkuste místo nich.

### <a name="what-is-maximum-number-of-tables-which-can-be-created"></a>Co je maximální počet tabulek, které lze vytvořit?
Neexistuje žádné fyzické omezení počtu tabulek, pošlete e-mail na [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) Pokud máte velký počet tabulek (kde je celková velikost konstantní překračuje 10 TB dat), které je potřeba vytvořit z obvyklé 10s nebo 100s. 

### <a name="what-is-the-maximum--of-keyspace-which-we-can-create"></a>Jaký je maximální počet keyspace, které můžeme vytvořit? 
Neexistuje žádné fyzické omezení počtu keyspaces, jako jsou kontejnery metadata, pošlete e-mail na [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) Pokud máte velký počet keyspaces z nějakého důvodu. 

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>Je možné mají být předány velké množství dat po spuštění z normální tabulky? 
Kapacita úložiště je automaticky prováděna a zvyšuje jako push v další data. Proto můžete importovat s jistotou dat, jaký potřebujete bez správy a zřizování uzly atd. 

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>Je možné poskytnout yaml souboru nastavení konfigurace chování Apache Casssandra rozhraní API služby Azure Cosmos DB?
Apache Cassandra rozhraní API služby Azure Cosmos DB je služba platformy. Poskytuje protokol zájmu úroveň kompatibility pro provádění operací. Skryje rychle složitosti správu, monitorování a konfigurace. Jako vývojář nebo uživatel není potřeba starat o dostupnosti, značek odstraněných položek, klíče mezipaměti, řádek mezipaměti, filtr Standard a velkého množství další nastavení. Azure Cosmos DB Apache Cassandra API se zaměřuje na poskytnutí výkon čtení a zápisu budete potřebovat bez nutnosti konfigurace a správy.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>Bude podporovat Apache Cassandra rozhraní API pro Azure Cosmos DB uzlu Přidání/clusteru nebo uzlu stav stav příkazy?
Rozhraní API Cassandra Apache je služba platformy, takže je plánování kapacity, reaguje na požadavky pružnost pro propustnost a úložiště uloženy. S Azure Cosmos DB zřídit propustnost, které potřebujete. Pak je možné škálovat ho nahoru a dolů libovolný počet časy prostřednictvím den bez obav o přidání nebo odstranění uzlů nebo jejich správě. To znamená, že není potřeba příliš použijte uzel, nástroj pro správu clusteru. 

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>Co se stane s ohledem na různá nastavení konfigurace pro vytvoření keyspace jako jednoduchý nebo síť?
Azure Cosmos DB poskytuje globální distribuční předinstalované z důvodu s nízkou latencí a dostupnosti. Není nutné pro instalaci repliky atd. Všech zápisů jsou vždy spolehlivě kvora potvrzené v všechny oblasti, kde se zadávají při současném poskytování výkonu záruky.  

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gcgrace-compression-memtableflushperiod-etc"></a>Co se stane s ohledem na různá nastavení pro metadata tabulky jako filtr standard pro ukládání do mezipaměti, přečtěte si oprava změn, gc_grace, komprese memtable_flush_period atd?
Azure Cosmos DB poskytuje výkonu pro čtení/zápisu a propustnost bez nutnosti zásahu do žádné nastavení konfigurace a ať už náhodně, manipulace s nimi.  

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Je podporováno time to live (TTL) pro Cassandra tabulky? 
Ano, je podporováno TTL. 

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>Je možné monitorování uzlu stav, stav repliky, globální katalog a parametry operačního systému starší pomocí různých nástrojů? Co je třeba sledovat nyní?
Azure Cosmos DB je služba platformy, která vám pomůže zvýšit produktivitu a nestarat se o správu a sledování infrastruktury. Potřebujete postará o propustnosti, která je k dispozici na portálu metriky najít, pokud jste jsou získávání omezené a zvětšit nebo zmenšit tento propustnost. Monitorování [SLA](monitor-accounts.md).
Použití [metriky](use-metrics.md) použití [diagnostické protokoly](logging.md).

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Které klientské sady SDK můžete pracovat s Apache Cassandra rozhraní API služby Azure Cosmos DB?
V privátní Preview verzi Apache Cassandra sady SDK klienta byly použity ovladače, které používají CQLv3 programů klienta. Pokud máte další ovladače, které můžete použít nebo pokud se setkávají problémy, pošlete e-mailu [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com). 

### <a name="is-composite-partition-key-supported"></a>Klíč oddílu složené podporovány?
Ano, můžete regulární syntaxe pro vytvoření oddílu složeného klíče. 

### <a name="can-i-use-sstable-loader-for-data-loading"></a>Můžete použít sstable zavaděč pro nahrávání dat?
Ne, verzi Preview nepodporuje sstable zavaděč. 

### <a name="can-an-on-premises-cassandra-cluster-be-paired-with-azure-cosmos-dbs-apache-cassandra-api"></a>Můžete místní cluster cassandra spárovat s rozhraním API Cassandra Apache Azure Cosmos DB?
Na přítomen Azure DB Cosmos má optimalizované prostředí pro cloudové prostředí bez režie operací. Pokud požadujete párování, pošlete prosím e-mailu [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) s popisem váš scénář.

### <a name="does-cassandra-api-provide-full-backups"></a>Poskytuje rozhraní API Cassandra úplné zálohy? 
Azure Cosmos DB poskytuje dva volné úplné zálohy pořízené v intervalu čtyři hodiny dnes mezi všechna rozhraní API. Tím se zajistí, že není potřeba nastavit plán zálohování atd. Pokud chcete upravit dobu uchovávání a četnost, pošlete e-mail na [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) nebo vyvolat případu podpory. Informace o možnostech zálohování najdete v [automatické online zálohování a obnovení Azure Cosmos DB](online-backup-and-restore.md) článku. 

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Jak účet Cassandra API zpracovávat převzetí služeb při selhání případě oblast výpadku? 
Rozhraní API služby Azure DB Cosmos Cassandra vychází z globálně distribuované platformu Azure Cosmos DB jazyků. Aby se zajistilo, že vaše aplikace může tolerovat výpadků datacenter, povolte alespoň jeden další oblast pro účet na portálu Azure Cosmos DB [vývoj s více oblast Azure Cosmos DB účty](regional-failover.md). Můžete nastavit prioritu oblasti pomocí portálu [vývoj s více oblast Azure Cosmos DB účty](regional-failover.md). 

Můžete přidat jako v mnoha oblastech jako pro účet a řídit, kde ji můžete převzetí služeb při selhání tím, že poskytuje prioritu převzetí služeb při selhání. K použití databáze, musíte zadat aplikaci existuje příliš. Pokud tak učiníte, nebudou vaši zákazníci se setkávají výpadku. 

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Rozhraní API Cassandra Apache všechny atributy indexu entity ve výchozím nastavení?
Ano, jsou všechny atributy entity indexovaný ve výchozím nastavení Azure Cosmos DB. Další informace najdete v tématu [Cosmos databázi Azure: indexování zásady](indexing-policies.md). Získat výhody zaručené výkonu s konzistentní indexování a trvanlivé kvora potvrzené zapíše vždycky. 

### <a name="does-this-mean-i-do-not-have-to-create-multiple-indexes-to-satisfy-the-queries"></a>Neodpovídá, znamená to není nutné vytvořit více indexů, aby pokryl dotazy? 
Ano, Azure Cosmos DB poskytuje automatické indexování všechny atributy bez jakékoli definice schématu. Tato automatizace uvolní vývojáři zaměřit se na aplikace a nikoli na vytvoření indexu a správu. Další informace najdete v tématu [Cosmos databázi Azure: indexování zásady](indexing-policies.md).

### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Lze použít novou sadu SDK API Cassandra místně pomocí emulátoru?
Plánujeme podporu této možnosti v budoucnosti. 

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-changefeed-and-other-functionality-will-these-capabilities-be-added-to-the-cassandra-api"></a>Azure DB Cosmos jako platformu zdá se, že máte spoustu možností, jako je například changefeed a další funkce. Tyto funkce přidá do rozhraní API Cassandra? 
Rozhraní API Cassandra Apache poskytuje stejné funkce jako Apache Cassandra CQL. Plánujeme viděl vhodnosti podporu různé možnosti v budoucnosti.

### <a name="feature-x-of-regular-cassandra-api-is-not-working-as-today-where-can-the-feedback-be-provided"></a>Funkce x regulární Cassandra rozhraní API nepracuje jako dnes, kde můžete zajistit zpětnou vazbu?
Poskytnutí zpětné vazby pomocí [názorů na webu uservoice](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md
