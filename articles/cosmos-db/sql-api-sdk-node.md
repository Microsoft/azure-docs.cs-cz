---
title: 'Azure Cosmos DB: rozhraní API pro SQL Node.js, & prostředky SDK'
description: Seznamte se se všemi informacemi o rozhraních API pro SQL Node.js a sadě SDK, včetně dat vydání, data odchodu a změn provedených mezi jednotlivými verzemi sady Azure Cosmos DB Node.js SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-js
ms.openlocfilehash: c3834b1890cb20b717462c4cb8ed1f779570c675
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92275207"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Node.js SDK pro SQL API: poznámky k verzi a prostředky
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [Sada .NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [Sada .NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Sada Java SDK v4](sql-api-sdk-java-v4.md)
> * [Sada Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sada Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Konektor Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Poskytovatel prostředků REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulk Executor – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor – Java](sql-api-sdk-bulk-executor-java.md)

|Prostředek  |Odkaz  |
|---------|---------|
|Stáhnout sadu SDK  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|Dokumentace k rozhraní API  |  [Referenční dokumentace sady JavaScript SDK](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest&preserve-view=true)
|Pokyny k instalaci sady SDK  |  [Pokyny k instalaci](https://github.com/Azure/azure-sdk-for-js)
|Přispívání do sady SDK | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| ukázky | [ Ukázky kóduNode.js](sql-api-nodejs-samples.md)
| Úvodní kurz | [Začínáme se sadou JavaScript SDK](sql-api-nodejs-get-started.md)
| Kurz webové aplikace | [Vytvoření Node.js webové aplikace pomocí Azure Cosmos DB](sql-api-nodejs-application.md)
| Aktuální podporovaná platforma | [Node.js V12. x](https://nodejs.org/en/blog/release/v12.7.0/) -SDK verze 3. x. x<br/>[Node.js v10 za účelem. x](https://nodejs.org/en/blog/release/v10.6.0/) -SDK verze 3. x. x<br/>[Node.js V8. x](https://nodejs.org/en/blog/release/v8.16.0/) -SDK verze 3. x. x<br/>[Node.js v6. x](https://nodejs.org/en/blog/release/v6.10.3/) -SDK verze 2. x. x<br/>[Node.js v 4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)– sada SDK verze 1. x. x<br/> [Node.js v 0.12](https://nodejs.org/en/blog/release/v0.12.0/)– sada SDK verze 1. x. x<br/> [Node.js v 0.10](https://nodejs.org/en/blog/release/v0.10.0/)– sada SDK verze 1. x. x

## <a name="release-notes"></a>Poznámky k verzi

### <a name="310"></a><a name="3.1.0"></a>3.1.0
* Nastavte výchozí ResponseContinuationTokenLimitInKB na 1 KB. Ve výchozím nastavení jsme cappingi na 1 KB, aby nedocházelo k dlouhým hlavičkám (Node.js má omezení velikosti globálního záhlaví). Uživatel může nastavit toto pole tak, aby povoloval delší hlavičky, což může přispět k provádění dotazů optimalizace back-endu.
* Odeberte disableSSLVerification. Tato možnost obsahuje nové alternativy popsané v tématu [#388](https://github.com/Azure/azure-cosmos-js/pull/388)

### <a name="304"></a><a name="3.0.4"></a>3.0.4
* Povoluje, aby initialHeaders explicitně nastavil hlavičku klíče oddílu.
* Pokud chcete zabránit publikování cizích souborů, použijte package.jsna # Files.
* Oprava chyby řazení mapy směrování ve starší verzi Node + V8
* Opravuje chybu, když uživatel zadá částečné možnosti opakování.

### <a name="303"></a><a name="3.0.3"></a>3.0.3
* Zabránit nástroji Webpack v překladu modulů s názvem vyžadovat

### <a name="302"></a><a name="3.0.2"></a>3.0.2
* Opravuje dlouhou nevyřízenou chybu, kde ru byly vždycky hlášeny jako 0 u agregačních dotazů.

### <a name="300"></a><a name="3.0.0"></a>3.0.0

Verze 🎉 v3! 🎉 mnoho nových funkcí, opravy chyb a některé zásadní změny. Primární cíle této verze:

* Implementovat hlavní nové funkce
  * JEDINEČNÉ dotazy
  * LIMITY a POSUNUTí dotazů
  * Požadavky na zrušení uživatele
* Aktualizace na nejnovější verzi REST API Cosmos, kde všechny kontejnery mají neomezenou škálu
* Zjednodušení používání Cosmos z prohlížeče
* Lepší zarovnání s novými pokyny pro Azure JS SDK

#### <a name="migration-guide-for-breaking-changes"></a>Průvodce migrací pro průlomové změny
##### <a name="improved-client-constructor-options"></a>Vylepšené možnosti klientského konstruktoru

Možnosti konstruktoru byly zjednodušeny:

* masterKey se přejmenoval klíč a přesunul se na nejvyšší úroveň.
* Vlastnosti dříve v části Možnosti. ověření se přesunuly na nejvyšší úroveň.

``` js
// v2
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    auth: {
        masterKey: "your-primary-key"
    }
})

// v3
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    key: "your-primary-key"
})
```

##### <a name="simplified-queryiterator-api"></a>Zjednodušené rozhraní QueryIterator API
V v2 existovalo mnoho různých způsobů iterace nebo načítání výsledků dotazu. Pokusili jsme se zjednodušit rozhraní V3 API a odebrat podobná nebo duplicitní rozhraní API:

* Odeberte iterátory. Next () a iterátor. Current (). Stránky výsledků získáte pomocí fetchNext ().
* Odeberte iterátor. forEach (). Místo toho použijte asynchronní iterátory.
* iterator.executeNext () přejmenováno na iterátor. fetchNext ()
* iterátor. ToArray – () přejmenován na iterátor. fetchAll ()
* Stránky jsou nyní vhodné objekty odpovědi namísto prostých objektů JS.
* const Container = Client. Database (dbId). Container (containerId)

``` js
// v2
container.items.query('SELECT * from c').toArray()
container.items.query('SELECT * from c').executeNext()
container.items.query('SELECT * from c').forEach(({ body: item }) => { console.log(item.id) })

// v3
container.items.query('SELECT * from c').fetchAll()
container.items.query('SELECT * from c').fetchNext()
for await(const { result: item } in client.databases.readAll().getAsyncIterator()) {
    console.log(item.id)
}
```

##### <a name="fixed-containers-are-now-partitioned"></a>Pevné kontejnery se teď dělí na oddíly.
Služba Cosmos nyní podporuje klíče oddílů ve všech kontejnerech, včetně těch, které byly dříve vytvořeny jako pevné kontejnery. Sada SDK V3 se aktualizuje na nejnovější verzi rozhraní API, která tuto změnu implementuje, ale neprovádí se jejich přerušení. Pokud pro operace nezadáte klíč oddílu, použijeme výchozí systémový klíč, který bude fungovat se všemi vašimi stávajícími kontejnery a dokumenty.

##### <a name="upsert-removed-for-stored-procedures"></a>Upsert odebrané pro uložené procedury
Dřív Upsert byl povolený pro nedělené kolekce, ale při aktualizaci verze API se všechny kolekce dělí na oddíly, takže jsme ho odebrali úplně.

##### <a name="item-reads-will-not-throw-on-404"></a>Čtení položky nebude vyvolávat na 404.
const Container = Client. Database (dbId). Container (containerId)

``` js
// v2
try {
    container.items.read(id, undefined)
} catch (e) {
    if (e.code === 404) { console.log('item not found') }
}

// v3
const { result: item }  = container.items.read(id, undefined)
if (item === undefined) { console.log('item not found') }
```

##### <a name="default-multi-region-write"></a>Výchozí zápis pro více oblastí
Sada SDK teď bude ve výchozím nastavení zapisovat do několika oblastí, pokud je vaše konfigurace Cosmos podporuje. Toto bylo dříve chování při výslovných přihlášení.

##### <a name="proper-error-objects"></a>Objekty správné chyby
Neúspěšné požadavky teď vyvolávají správnou chybu nebo podtřídy chyby. Dřív vyvolala prosté objekty JS.

#### <a name="new-features"></a>Nové funkce
##### <a name="user-cancelable-requests"></a>Požadavky na zrušení uživatelem
Když se přesunete do interního načtení, můžeme použít rozhraní API prohlížeče AbortController k podpoře operací, které lze zrušit uživatelem. V případě operací, které mohou být v průběhu více požadavků (například dotazů mezi jednotlivými oddíly), budou všechny požadavky na operaci zrušeny. Uživatelé moderních prohlížečů již budou mít AbortController. Node.js uživatelé budou muset použít knihovnu Refill.

``` js
 const controller = new AbortController()
 const {result: item} = await items.query('SELECT * from c', { abortSignal: controller.signal});
 controller.abort()
```

##### <a name="set-throughput-as-part-of-dbcontainer-create-operation"></a>Nastavení propustnosti v rámci operace vytvoření databáze nebo kontejneru
``` js
const { database }  = client.databases.create({ id: 'my-database', throughput: 10000 })
database.containers.create({ id: 'my-container', throughput: 10000 })
```

##### <a name="azurecosmos-sign"></a>@azure/cosmos-sign
Generování tokenu hlaviček bylo rozděleno do nové knihovny, @azure/cosmos-sign . Kdokoli, kdo volá Cosmos REST API, může přímo použít k podepsání hlaviček pomocí stejného kódu, který voláme uvnitř @azure/cosmos .

##### <a name="uuid-for-generated-ids"></a>UUID pro vygenerované identifikátory
V2 má vlastní kód pro generování ID položek. Přepnuli jsme se na známý a udržovaný UUID pro komunitní knihovny.

##### <a name="connection-strings"></a>Připojovací řetězce
Je teď možné předat připojovací řetězec zkopírovaný z Azure Portal:

``` js
const client = new CosmosClient("AccountEndpoint=https://test-account.documents.azure.com:443/;AccountKey=c213asdasdefgdfgrtweaYPpgoeCsHbpRTHhxuMsTaw==;")
Add DISTINCT and LIMIT/OFFSET queries (#306)
 const { results } = await items.query('SELECT DISTINCT VALUE r.name FROM ROOT').fetchAll()
 const { results } = await items.query('SELECT * FROM root r OFFSET 1 LIMIT 2').fetchAll()
```

#### <a name="improved-browser-experience"></a>Vylepšené prostředí prohlížeče
I když bylo možné v prohlížeči použít sadu v2 SDK, nejednalo se o ideální prostředí. Museli jste vyplnit několik node.js integrovaných knihoven a používat například sadu prostředků, jako je například Webpack nebo parcela. Sada V3 SDK usnadňuje vykonávání prostředí pro uživatele v prohlížeči, a to mnohem lépe.

* Nahradit interní požadavky pomocí načtení (#245)
* Odebrat využití vyrovnávací paměti (#330)
* Odebrat předdefinovaná použití uzlu ve prospěch univerzálních balíčků/rozhraní API (#328)
* Přepnout na uzel-přerušení-kontroler (#294)

#### <a name="bug-fixes"></a>Opravy chyb
* Oprava nabídky načíst a vrátit zpět nabídky testů (#224)
* Oprava EnableEndpointDiscovery (#207)
* Oprava chybějícího ru pro stránkované výsledky (#360)
* Rozšířit typ parametru dotazu SQL (#346)
* Přidat hodnotu TTL do ItemDefinition (#341)
* Oprava metrik dotazů CP (#311)
* Přidat activityId do FeedResponse (#293)
* Přepnout _ts typ z řetězce na číslo (#252) (#295)
* Oprava agregace poplatků za požadavek (#289)
* Povolení klíčů oddílu s prázdným řetězcem (#277)
* Přidat řetězec do typu dotazu na konflikt (#237)
* Přidat uniqueKeyPolicy do kontejneru (#234)

#### <a name="engineering-systems"></a>Technické systémy
Ne vždy nejčastěji viditelné změny, ale pomůžou náš tým dodat lepší kód rychleji.

* Použít souhrn pro sestavení v produkčním prostředí (#104)
* Aktualizace na TypeScript 3,5 (#327)
* Převést na odkazy na projekt TS Extrahovat složku testu (#270)
* Povolit noUnusedLocals a noUnusedParameters (#275)
* Azure Pipelines YAML pro sestavení CI (#298)

### <a name="215"></a><a name="2.1.5"></a>2.1.5
* Žádné změny kódu. Opravuje problém, kdy byly některé další soubory zahrnuté do balíčku 2.1.4.

### <a name="214"></a><a name="2.1.4"></a>2.1.4
* Oprava místního převzetí služeb při selhání v rámci zásad opakování
* Oprava vlastnosti ChangeFeed hasMoreResults
* Aktualizace závislostí pro vývoj
* Přidat PolicheckExclusions.txt

### <a name="213"></a><a name="2.1.3"></a>2.1.3
* Přepnout _ts typ z řetězce na číslo
* Oprava výchozích testů indexování
* Backport uniqueKeyPolicy na v2
* Ukázka a ukázka oprav ladění

### <a name="212"></a><a name="2.1.2"></a>2.1.2
* Backport nabízí opravy z větve v3.
* Oprava chyby v signatuře typu executeNext ()
* Opravy překlepu

### <a name="211"></a><a name="2.1.1"></a>bodů
* Restrukturalizace sestavení Umožňuje vystavení verze sady SDK v době sestavení.

### <a name="210"></a><a name="2.1.0"></a>2.1.0
#### <a name="new-features"></a>Nové funkce
* Přidání podpory ChangeFeed (#196)
* Přidaný datový typ s více mnohoúhelníky pro indexování (#191)
* Přidat vlastnost Key do konstruktoru jako alias pro masterKey (#202)

#### <a name="fixes"></a>Opravy
* Oprava chyby, kde Next () vrátila nesprávnou hodnotu pro iterátor

#### <a name="engineering-improvements"></a>Technické vylepšení
* Přidat test integrace pro spotřebu TypeScript (#199)
* Povolit instalaci přímo z GitHubu (#194)

### <a name="205"></a><a name="2.0.5"></a>2.0.5
* Přidá rozhraní pro typ agenta uzlu. Uživatelé TypeScript už nebudou muset instalovat @types/node jako závislost.
* Upřednostňovaná umístění jsou nyní správně dodržena.
* Vylepšení přispívající k dokumentaci pro vývojáře
* Různé opravy překlepu

### <a name="204"></a><a name="2.0.4"></a>2.0.4
* Opravuje problém definice typu představený v 2.0.3

### <a name="203"></a><a name="2.0.3"></a>2.0.3
* Odebrat `big-integer` závislost
* Přepněte na direktivy Reference pro typ AsyncIterable. Uživatelé TypeScript již nemusí přizpůsobovat své nastavení "lib".
* Opravy překlepu

### <a name="202"></a><a name="2.0.2"></a>2.0.2
* Opravit odkazy souboru Readme

### <a name="201"></a><a name="2.0.1"></a>2.0.1
* Opravit implementaci rozhraní opakování

### <a name="200"></a><a name="2.0.0"></a>2.0.0
* GA verze 2.0.0 sady SDK pro JavaScript
* Byla přidána podpora pro zápisy ve více oblastech.

### <a name="200-3"></a><a name="2.0.0-3"></a>2.0.0-3
* RC1 verze 2.0.0 sady JavaScript SDK pro verzi Public Preview.
* Nový objektový model s CosmosClient a metodami na nejvyšší úrovni se rozdělí mezi relevantní databáze, kontejnery a třídy položek. 
* Podpora pro [příslibů](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). 
* Sada SDK se převedla na TypeScript.

### <a name="1144"></a><a name="1.14.4"></a>1.14.4
* byla opravena dokumentace k npm.

### <a name="1143"></a><a name="1.14.3"></a>1.14.3
* Přidání podpory pro výchozí opakování při potížích s připojením
* Přidala se podpora pro čtení kanálu změn kolekce.
* Opravená chyba konzistence relace, která občas způsobila, že "relace čtení není k dispozici".
* Přidání podpory pro metriky dotazů.
* Byl změněn maximální počet připojení agenta http.

### <a name="1142"></a><a name="1.14.2"></a>1.14.2
* Aktualizovaná dokumentace, aby odkazovala Azure Cosmos DB místo Azure DocumentDB.
* Přidání podpory pro nastavení proxyUrl v ConnectionPolicy

### <a name="1141"></a><a name="1.14.1"></a>1.14.1
* Menší oprava pro systémy souborů s rozlišením malých a velkých písmen.

### <a name="1140"></a><a name="1.14.0"></a>1.14.0
* Přidá podporu pro konzistenci relace.
* Tato verze sady SDK vyžaduje nejnovější verzi [emulátoru Azure Cosmos DB](https://aka.ms/cosmosdb-emulator).

### <a name="1130"></a><a name="1.13.0"></a>1.13.0
* Rozdělte dotazy na více oddílů.
* Přidá podporu pro odkaz na prostředek s úvodním a koncovým lomítkem (a odpovídajícími testy).

### <a name="1122"></a><a name="1.12.2"></a>1.12.2
*    byla opravena dokumentace k npm.

### <a name="1121"></a><a name="1.12.1"></a>1.12.1
* Opravili jsme chybu v executeStoredProcedure, kde se v dokumentech podílely speciální znaky Unicode (LS, PS).
* Opravili jsme chybu ve zpracování dokumentů se znaky Unicode v klíči oddílu.
* Pevná podpora pro vytváření kolekcí s názvem média #114 problému GitHubu.
* Pevná podpora autorizačního tokenu oprávnění #178 problému GitHubu.

### <a name="1120"></a><a name="1.12.0"></a>1.12.0
* Přidání podpory pro novou [úroveň konzistence](consistency-levels.md) s názvem ConsistentPrefix.
* Byla přidána podpora pro UriFactory.
* Opravili jsme chybu podpory kódování Unicode. #171 problému GitHubu.

### <a name="1110"></a><a name="1.11.0"></a>1.11.0
* Přidali jsme podporu agregačních dotazů (COUNT, MIN, MAX, SUM a AVG).
* Přidání možnosti pro řízení míry paralelismu pro dotazy na více oddílů.
* Přidání možnosti pro zakázání ověřování TLS při spuštění v emulátoru Azure Cosmos DB.
* Byla snížena minimální propustnost u dělených kolekcí z 10 100 RU/s na 2500 RU/s.
* Opravila se chyba tokenu pro pokračování pro kolekci s jedním oddílem. #107 problému GitHubu.
* Opravili jsme chybu executeStoredProcedure při zpracovávání 0 jako jeden parametr. #155 problému GitHubu.

### <a name="1102"></a><a name="1.10.2"></a>1.10.2
* Opravené záhlaví User-agentu, které bude obsahovat verzi sady SDK.
* Vyčištění vedlejšího kódu.

### <a name="1101"></a><a name="1.10.1"></a>1.10.1
* Zakazuje se ověřování TLS při použití sady SDK k zacílení na emulátor (název hostitele = localhost).
* Přidání podpory pro povolení protokolování skriptu během provádění uložených procedur.

### <a name="1100"></a><a name="1.10.0"></a>1.10.0
* Byla přidána podpora paralelních dotazů mezi oddíly.
* Přidání podpory pro dotazy TOP/ORDER BY pro dělené kolekce

### <a name="190"></a><a name="1.9.0"></a>1.9.0
* Přidala se podpora zásad opakování pro omezené požadavky. (Omezení požadavků obdrží příliš velkou výjimku, kód chyby 429.) Ve výchozím nastavení se Azure Cosmos DB opakuje devět časů pro každý požadavek, pokud se zjistil kód chyby 429, který respektuje čas retryAfter v hlavičce odpovědi. Pevný čas intervalu opakování se teď dá nastavit jako součást vlastnosti RetryOptions objektu ConnectionPolicy, pokud chcete ignorovat dobu retryAfter vrácenou serverem mezi opakovanými pokusy. Azure Cosmos DB nyní čeká maximálně 30 sekund pro každý požadavek, který je omezený (bez ohledu na počet opakování), a vrátí odpověď s kódem chyby 429. Tuto dobu lze také přepsat ve vlastnosti RetryOptions objektu ConnectionPolicy.
* Cosmos DB nyní vrací hodnoty x-MS-Throttle-Return-Count a x-MS-Throttle-Retry-Time-MS jako hlavičky odpovědi v každé žádosti, aby se poznamenalo, že počet opakování omezení a kumulativní doba, jakou požadavek mezi opakovanými pokusy čekal.
* Byla přidána třída RetryOptions, která vystavuje vlastnost RetryOptions třídy ConnectionPolicy, kterou lze použít k přepsání některých výchozích možností opakování.

### <a name="180"></a><a name="1.8.0"></a>1.8.0
* Přidání podpory pro účty databáze ve více oblastech.

### <a name="170"></a><a name="1.7.0"></a>1.7.0
* Přidali jsme podporu funkce Time to Live (TTL) pro dokumenty.

### <a name="160"></a><a name="1.6.0"></a>1.6.0
* Implementované [dělené kolekce](partitioning-overview.md) a [uživatelem definované úrovně výkonu](performance-levels.md).

### <a name="156"></a><a name="1.5.6"></a>1.5.6
* Opravila se chyba RangePartitionResolver. resolveForRead, kde nevrátila odkazy z důvodu nesprávného concatu výsledků.

### <a name="155"></a><a name="1.5.5"></a>1.5.5
* Opravená hashPartitionResolver resolveForRead (): Pokud nebyl žádný zadaný klíč oddílu vyvolal výjimku, místo vrácení seznamu všech registrovaných odkazů.

### <a name="154"></a><a name="1.5.4"></a>1.5.4
* Opravuje problémový [#100](https://github.com/Azure/azure-documentdb-node/issues/100) vyhrazený agent https: Neměňte globální agenta pro Azure Cosmos DB účely. Použijte vyhrazeného agenta pro všechny požadavky lib.

### <a name="153"></a><a name="1.5.3"></a>1.5.3
* Opravuje [#81](https://github.com/Azure/azure-documentdb-node/issues/81) potíží – správně zpracovávají pomlčky v ID médií.

### <a name="152"></a><a name="1.5.2"></a>2.5.1
* Opravuje problém [#95](https://github.com/Azure/azure-documentdb-node/issues/95) -EventEmitter upozornění na nevracení naslouchacího procesu.

### <a name="151"></a><a name="1.5.1"></a>1.5.1
* Opravuje problém [#92](https://github.com/Azure/azure-documentdb-node/issues/90) -přejmenovat hodnotu hash složky na hodnotu hash pro systémy citlivé na velká a malá písmena.

### <a name="150"></a><a name="1.5.0"></a>1.5.0
* Implementujte podporu horizontálního dělení přidáním překladačů oddílů rozsahu & hash.

### <a name="140"></a><a name="1.4.0"></a>1.4.0
* Implementujte Upsert. Nové metody upsertXXX v documentClient.

### <a name="130"></a><a name="1.3.0"></a>1.3.0
* Vynecháno pro uvedení čísel verzí v zarovnání s jinými sadami SDK.

### <a name="122"></a><a name="1.2.2"></a>1.2.2
* Oddělte obálku Q příslibů do nového úložiště.
* Aktualizujte soubor balíčku pro registr npm.

### <a name="121"></a><a name="1.2.1"></a>1.2.1
* Implementuje směrování na základě ID.
* Opravuje problém [#49](https://github.com/Azure/azure-documentdb-node/issues/49) – aktuální vlastnost je v konfliktu s metodou Current ().

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* Byla přidána podpora geoprostorového indexu.
* Ověří vlastnost ID pro všechny prostředky. Identifikátory pro prostředky nesmí obsahovat znaky?,/, #,  &#47;&#47;, nebo končit mezerou.
* Přidá novou hlavičku průběh transformace indexu do ResourceResponse.

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* Implementuje zásadu indexování v2.

### <a name="103"></a><a name="1.0.3"></a>1.0.3
* Vydejte [#40](https://github.com/Azure/azure-documentdb-node/issues/40) a konfigurace grunt v základní sadě a sadě SDK pro příslib.

### <a name="102"></a><a name="1.0.2"></a>1.0.2
* Problém [#45](https://github.com/Azure/azure-documentdb-node/issues/45) – obálka příslibů neobsahuje hlavičku s chybou.

### <a name="101"></a><a name="1.0.1"></a>1.0.1
* Byla implementována možnost dotazování na konflikty přidáním readConflicts, readConflictAsync a queryConflicts.
* Aktualizovala se dokumentace k rozhraní API.
* Problém [#41](https://github.com/Azure/azure-documentdb-node/issues/41) – chyba Client. metody createdocumentasync

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* GA SDK.

## <a name="release--retirement-dates"></a>Data vyřazení & vydání

Microsoft poskytuje oznámení alespoň **12 měsíců** před vyřazením sady SDK z důvodu hladkého přechodu na novější/podporovanou verzi. Nové funkce, možnosti a optimalizace se přidávají jen do aktuální sady SDK, proto vám doporučujeme, abyste vždy co nejdříve provedli aktualizaci na nejnovější verzi sady SDK.

| Verze | Datum vydání | Datum vyřazení |
| --- | --- | --- |
| [3.1.0](#3.1.0) |26. července 2019 |--- |
| [3.0.4](#3.0.4) |22. července 2019 |--- |
| [3.0.3](#3.0.3) |17. července 2019 |--- |
| [3.0.2](#3.0.2) |9\. července 2019 |--- |
| [3.0.0](#3.0.0) |28. června 2019 |--- |
| [2.1.5](#2.1.5) |20. března 2019 |--- |
| [2.1.4](#2.1.4) |15. března 2019 |--- |
| [2.1.3](#2.1.3) |8. března 2019 |--- |
| [2.1.2](#2.1.2) |28. ledna 2019 |--- |
| [bodů](#2.1.1) |5. prosince 2018 |--- |
| [2.1.0](#2.1.0) |4. prosince 2018 |--- |
| [2.0.5](#2.0.5) |7. listopadu 2018 |--- |
| [2.0.4](#2.0.4) |30. října 2018 |--- |
| [2.0.3](#2.0.3) |30. října 2018 |--- |
| [2.0.2](#2.0.2) |10. října 2018 |--- |
| [2.0.1](#2.0.1) |25. září 2018 |--- |
| [2.0.0](#2.0.0) |Září 24, 2018 |--- |
| [2.0.0-3 (RC)](#2.0.0-3) |2. srpna 2018 |--- |
| [1.14.4](#1.14.4) |Květen 03, 2018 |30. srpna 2020 |
| [1.14.3](#1.14.3) |Květen 03, 2018 |30. srpna 2020 |
| [1.14.2](#1.14.2) |21. prosince 2017 |30. srpna 2020 |
| [1.14.1](#1.14.1) |10. listopadu 2017 |30. srpna 2020 |
| [1.14.0](#1.14.0) |9. listopadu 2017 |30. srpna 2020 |
| [1.13.0](#1.13.0) |11. října 2017 |30. srpna 2020 |
| [1.12.2](#1.12.2) |10. srpna 2017 |30. srpna 2020 |
| [1.12.1](#1.12.1) |10. srpna 2017 |30. srpna 2020 |
| [1.12.0](#1.12.0) |10. května 2017 |30. srpna 2020 |
| [1.11.0](#1.11.0) |16. března 2017 |30. srpna 2020 |
| [1.10.2](#1.10.2) |27. ledna 2017 |30. srpna 2020 |
| [1.10.1](#1.10.1) |22. prosince 2016 |30. srpna 2020 |
| [1.10.0](#1.10.0) |03. října 2016 |30. srpna 2020 |
| [1.9.0](#1.9.0) |7. července 2016 |30. srpna 2020 |
| [1.8.0](#1.8.0) |14. června 2016 |30. srpna 2020 |
| [1.7.0](#1.7.0) |26. dubna 2016 |30. srpna 2020 |
| [1.6.0](#1.6.0) |29. března 2016 |30. srpna 2020 |
| [1.5.6](#1.5.6) |08 8. března 2016 |30. srpna 2020 |
| [1.5.5](#1.5.5) |2. února 2016 |30. srpna 2020 |
| [1.5.4](#1.5.4) |Únor 01, 2016 |30. srpna 2020 |
| [2.5.1](#1.5.2) |26. ledna 2016 |30. srpna 2020 |
| [2.5.1](#1.5.2) |22. ledna 2016 |30. srpna 2020 |
| [1.5.1](#1.5.1) |4. ledna 2016 |30. srpna 2020 |
| [1.5.0](#1.5.0) |31. prosince 2015 |30. srpna 2020 |
| [1.4.0](#1.4.0) |06 6. října 2015 |30. srpna 2020 |
| [1.3.0](#1.3.0) |06 6. října 2015 |30. srpna 2020 |
| [1.2.2](#1.2.2) |10. září 2015 |30. srpna 2020 |
| [1.2.1](#1.2.1) |15. srpna 2015 |30. srpna 2020 |
| [1.2.0](#1.2.0) |05. srpna 2015 |30. srpna 2020 |
| [1.1.0](#1.1.0) |9. července 2015 |30. srpna 2020 |
| [1.0.3](#1.0.3) |04. června 2015 |30. srpna 2020 |
| [1.0.2](#1.0.2) |23. května 2015 |30. srpna 2020 |
| [1.0.1](#1.0.1) |15. května 2015 |30. srpna 2020 |
| [1.0.0](#1.0.0) |08 8. dubna 2015 |30. srpna 2020 |

## <a name="faq"></a>Nejčastější dotazy
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Viz také
Další informace o Cosmos DB najdete na stránce služby [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

