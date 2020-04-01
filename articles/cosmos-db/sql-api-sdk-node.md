---
title: 'Azure Cosmos DB: ROZHRANÍ API SQL Node.js, sada SDK & prostředky'
description: Přečtěte si vše o rozhraní SQL Node.js API a SDK, včetně dat vydání, dat vyřazení a změn provedených mezi jednotlivými verzemi sady SDK Azure Cosmos DB Node.js.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 09/24/2018
ms.author: dech
ms.openlocfilehash: 181f43e060e3b4b41babeee375365a97e1d224aa
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411275"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Node.js SDK pro rozhraní SQL API: Poznámky k vydání a prostředky
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Informační kanál o změně .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Poskytovatel prostředků REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Hromadný vykonavatel - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Hromadný vykonavatel - Java](sql-api-sdk-bulk-executor-java.md)

|Prostředek  |Odkaz  |
|---------|---------|
|Stáhnout sadu SDK  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|Dokumentace rozhraní API  |  [Referenční dokumentace sady JavaScript SDK](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest)
|Pokyny k instalaci sady SDK  |  [Pokyny k instalaci](https://github.com/Azure/azure-cosmos-js#installation)
|Přispívat do sady SDK | [GitHubu](https://github.com/Azure/azure-cosmos-js/tree/master)
| ukázky | [Ukázky kódu node.js](sql-api-nodejs-samples.md)
| Výuka Začínáme | [Začínáme se sadou JavaScript SDK](sql-api-nodejs-get-started.md)
| Kurz webové aplikace | [Vytvoření webové aplikace Node.js pomocí Azure Cosmos DB](sql-api-nodejs-application.md)
| Aktuální podporovaná platforma | [Node.js v12.x](https://nodejs.org/en/blog/release/v12.7.0/) - SDK verze 3.x.x<br/>[Node.js v10.x](https://nodejs.org/en/blog/release/v10.6.0/) - SDK verze 3.x.x<br/>[Node.js v8.x](https://nodejs.org/en/blog/release/v8.16.0/) - SDK verze 3.x.x<br/>[Node.js v6.x](https://nodejs.org/en/blog/release/v6.10.3/) - SDK verze 2.x.x<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)- SDK verze 1.x.x<br/> [Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)- SDK verze 1.x.x<br/> [Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)- SDK verze 1.x.x

## <a name="release-notes"></a>Poznámky k verzi

### <a name=""></a><a name="3.1.0"/>3.1.0</a>
* Nastavte výchozí hodnotu ResponseContinuationTokenLimitInKB na 1kb. Ve výchozím nastavení toto omezujeme na 1 kb, abychom se vyhnuli dlouhým záhlavím (Soubor Node.js má globální limit velikosti záhlaví). Uživatel může nastavit toto pole povolit delší záhlaví, což může pomoci back-endu optimalizovat provádění dotazů.
* Odebrat disableSSLVerification. Tato možnost má nové alternativy popsané v [#388](https://github.com/Azure/azure-cosmos-js/pull/388)

### <a name=""></a><a name="3.0.4"/>3.0.4</a>
* Povolit záhlaví iniciály explicitně nastavit záhlaví klíče oddílu
* Použití souborů package.json#, chcete-li zabránit publikování cizích souborů
* Oprava chyby řazení mapy směrování ve starší verzi uzlu+v8
* Opravuje chybu, když uživatel poskytuje možnosti částečného opakování

### <a name=""></a><a name="3.0.3"/>3.0.3</a>
* Zabránit webpack u řešení modulů volaný s vyžadovat

### <a name=""></a><a name="3.0.2"/>3.0.2</a>
* Opravuje dlouho nevyřízené chyby, kde ru byly vždy hlášeny jako 0 pro souhrnné dotazy

### <a name=""></a><a name="3.0.0"/>3.0.0</a>

🎉 v3 vydání! 🎉 Mnoho nových funkcí, opravy chyb a několik nejnovějších změn. Hlavní cíle této verze:

* Implementace hlavních nových funkcí
  * ODLIŠNÉ dotazy
  * DOTAZY LIMIT/OFFSET
  * Uživatel ruší požadavky
* Aktualizace na nejnovější verzi rozhraní COSMOS REST API, kde všechny kontejnery mají neomezené měřítko
* Usnadnění používání cosmosu z prohlížeče
* Lepší přizpůsobení novým pokynům sady Azure JS SDK

#### <a name="migration-guide-for-breaking-changes"></a>Průvodce migrací pro nejnovější změny
##### <a name="improved-client-constructor-options"></a>Vylepšené možnosti konstruktoru klienta

Možnosti konstruktoru byly zjednodušeny:

* masterKey byl přejmenován na klíč a přesunut na nejvyšší úroveň
* Vlastnosti dříve v rámci options.auth byly přesunuty na nejvyšší úroveň

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

##### <a name="simplified-queryiterator-api"></a>Zjednodušené rozhraní API queryiteror
Ve v2 bylo mnoho různých způsobů, jak iterate nebo načíst výsledky z dotazu. Pokusili jsme se zjednodušit rozhraní API v3 a odebrat podobná nebo duplicitní rozhraní API:

* Odstraňte iterator.next() a iterator.current(). Použijte fetchNext() získat stránky výsledků.
* Odstraňte iterator.forEach(). Místo toho použijte asynchronní iterátory.
* iterator.executeNext() přejmenován na iterator.fetchNext()
* iterator.toArray() přejmenován na iterator.fetchAll()
* Stránky jsou nyní správné objekty odezvy namísto prostých objektů JS
* const container = client.database(dbId).container(containerId)

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

##### <a name="fixed-containers-are-now-partitioned"></a>Pevné kontejnery jsou nyní rozděleny
Služba Cosmos nyní podporuje klíče oddílů ve všech kontejnerech, včetně těch, které byly dříve vytvořeny jako pevné kontejnery. Sada SDK verze v3 se aktualizuje na nejnovější verzi rozhraní API, která implementuje tuto změnu, ale není rozdělena. Pokud nezadáte klíč oddílu pro operace, budeme výchozí systémový klíč, který pracuje se všemi existujícíkontejnery a dokumenty.

##### <a name="upsert-removed-for-stored-procedures"></a>Upsert odstraněn pro uložené procedury
Dříve upsert bylo povoleno pro kolekce bez oddílů, ale s aktualizací verze rozhraní API, všechny kolekce jsou rozděleny oddíly, takže jsme odstranili úplně.

##### <a name="item-reads-will-not-throw-on-404"></a>Čtení položky nebude hodit na 404
const container = client.database(dbId).container(containerId)

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

##### <a name="default-multi-region-write"></a>Výchozí zápis ve více oblastech
Sada SDK bude nyní ve výchozím nastavení zapisovat do více oblastí, pokud ji podporuje konfigurace Cosmos. Dříve se jednalo o chování při vysazení.

##### <a name="proper-error-objects"></a>Správné chybové objekty
Neúspěšné požadavky nyní vyvolat vlastní chyba nebo podtřídy error. Dříve házeli obyčejné předměty JS.

#### <a name="new-features"></a>Nové funkce
##### <a name="user-cancelable-requests"></a>Požadavky na zrušení uživatelem
Přesun načíst interně nám umožňuje používat prohlížeč AbortController API pro podporu uživatele zrušitelné operace. V případě operací, kde potenciálně probíhá více požadavků (například dotazy mezi oddíly), budou zrušeny všechny požadavky na operaci. Moderní uživatelé prohlížeče již budou mít AbortController. Uživatelé souboru Node.js budou muset používat knihovnu polyvýplní

``` js
 const controller = new AbortController()
 const {result: item} = await items.query('SELECT * from c', { abortSignal: controller.signal});
 controller.abort()
```

##### <a name="set-throughput-as-part-of-dbcontainer-create-operation"></a>Nastavit propustnost jako součást operace vytvoření db/kontejneru
``` js
const { database }  = client.databases.create({ id: 'my-database', throughput: 10000 })
database.containers.create({ id: 'my-container', throughput: 10000 })
```

##### <a name="azurecosmos-sign"></a>@azure/cosmos-sign
Generování tokenu záhlaví bylo rozděleno @azure/cosmos-signdo nové knihovny . Každý, kdo volá cosmos REST API přímo můžete použít k @azure/cosmospodpisu záhlaví pomocí stejného kódu voláme uvnitř .

##### <a name="uuid-for-generated-ids"></a>UUID pro vygenerovaná ID
v2 měl vlastní kód pro generování ID zboží. Přešli jsme na známou a udržovanou komunitní knihovnu.

##### <a name="connection-strings"></a>Připojovací řetězce
Nyní je možné předat připojovací řetězec zkopírovaný z webu Azure Portal:

``` js
const client = new CosmosClient("AccountEndpoint=https://test-account.documents.azure.com:443/;AccountKey=c213asdasdefgdfgrtweaYPpgoeCsHbpRTHhxuMsTaw==;")
Add DISTINCT and LIMIT/OFFSET queries (#306)
 const { results } = await items.query('SELECT DISTINCT VALUE r.name FROM ROOT').fetchAll()
 const { results } = await items.query('SELECT * FROM root r OFFSET 1 LIMIT 2').fetchAll()
```

#### <a name="improved-browser-experience"></a>Vylepšené prostředí prohlížeče
I když bylo možné použít v2 SDK v prohlížeči to nebyl ideální zážitek. Potřebovali jste polyvyplnit několik předdefinovaných knihoven node.js a použít bundler jako Webpack nebo Parcel. V3 SDK dělá out of the box zkušenosti mnohem lepší pro uživatele prohlížeče.

* Nahradit interní požadavky načtením (#245)
* Odebrání použití vyrovnávací paměti (#330)
* Odebrání integrovaného použití uzlu ve prospěch univerzálních balíčků/api (#328)
* Přepnout na řadič uzlu a přerušení (#294)

#### <a name="bug-fixes"></a>Opravy chyb
* Oprava nabídky číst a přivést zpět nabídku testy (#224)
* Oprava enableendpointDiscovery (#207)
* Oprava chybějících ru na stránkovaných výsledcích (#360)
* Rozbalit typ parametru dotazu SQL (#346)
* Přidat ttl do ItemDefinition (#341)
* Oprava metrik dotazu CP (#311)
* Přidat activityId do FeedResponse (#293)
* Přepnout typ _ts z řetězce na číslo (#252)(#295)
* Oprava agregace poplatků za požadavky (#289)
* Povolit prázdné klíče oddílů řetězce (#277)
* Přidání řetězce do typu konfliktního dotazu (#237)
* Přidat uniqueKeyPolicy do kontejneru (#234)

#### <a name="engineering-systems"></a>Inženýrské systémy
Ne vždy nejviditelnější změny, ale pomáhají našemu týmu doručovat lepší kód rychleji.

* Použití souhrnu pro produkční sestavení (#104)
* Aktualizace na typescript 3.5 (#327)
* Převést na odkazy na projekt TS. Extrahovat testovací složku (#270)
* Povolit noUnusedLocals a noUnusedParameters (#275)
* Azure Pipelines YAML pro sestavení CI (#298)

### <a name=""></a><a name="2.1.5"/>2.1.5</a>
* Žádné změny kódu. Opravuje problém, kdy některé další soubory byly zahrnuty v balíčku 2.1.4.

### <a name=""></a><a name="2.1.4"/>2.1.4</a>
* Oprava místního převzetí služeb při selhání v rámci zásad opakování
* Oprava vlastnosti HasMoreResults kanálu ChangeFeed
* Aktualizace závislostí dev
* Přidat PolicheckExclusions.txt

### <a name=""></a><a name="2.1.3"/>2.1.3</a>
* Přepnutí typu _ts z řetězce na číslo
* Oprava výchozích testů indexování
* Backport uniqueKeyPolicy to v2 Backport uniqueKeyPolicy to v2 Backport uniqueKeyPolicy to v2 Backport
* Ukázkové a demo ladění opravy

### <a name=""></a><a name="2.1.2"/>2.1.2</a>
* Opravy nabídek Backport z větve v3
* Oprava chyby v podpisu typu executeNext()
* Opravy překlepů

### <a name=""></a><a name="2.1.1"/>2.1.1</a>
* Budovat restrukturalizaci. Umožňuje vytažení verze sady SDK v době sestavení.

### <a name=""></a><a name="2.1.0"/>2.1.0</a>
#### <a name="new-features"></a>Nové funkce
* Přidána podpora ChangeFeed (#196)
* Přidán datový typ MultiPolygon pro indexování (#191)
* Přidat vlastnost "key" do konstruktoru jako alias pro masterKey (#202)

#### <a name="fixes"></a>Opravy
* Oprava chyby, kde next() vracela nesprávnou hodnotu na iterátoru

#### <a name="engineering-improvements"></a>Technická vylepšení
* Přidání testu integrace pro spotřebu psacího stroje (#199)
* Povolení instalace přímo z GitHubu (#194)

### <a name=""></a><a name="2.0.5"/>2.0.5</a>
* Přidá rozhraní pro typ agenta uzlu. Uživatelé typescriptu již @types/node nemusí instalovat jako závislost
* Preferovaná místa jsou nyní řádně respektována
* Vylepšení přispívající dokumentace pro vývojáře
* Různé opravy překlepů

### <a name=""></a><a name="2.0.4"/>2.0.4</a>
* Opravuje problém s definicí typu zavedený v po.0.3.

### <a name=""></a><a name="2.0.3"/>2.0.3</a>
* Odebrat `big-integer` závislost
* Přepněte na referenční direktivy pro asyncIterable typ. Uživatelé typescriptu již nebudou muset přizpůsobovat své nastavení "lib".
* Překlep opravy

### <a name=""></a><a name="2.0.2"/>2.0.2</a>
* Oprava odkazů readme

### <a name=""></a><a name="2.0.1"/>2.0.1</a>
* Oprava implementace rozhraní opakování

### <a name=""></a><a name="2.0.0"/>2.0.0</a>
* GA verze 2.0.0 sady JavaScript SDK
* Přidána podpora pro zápisy ve více oblastech.

### <a name=""></a><a name="2.0.0-3"/>2.0.0-3</a>
* RC1 verze 2.0.0 sady JavaScript SDK pro verzi Public Preview.
* Nový objektový model s cosmosclient nejvyšší úrovně a metodami rozdělenými mezi příslušné třídy Database, Container a Item. 
* Podpora [slibů](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). 
* Sada SDK převedena na typescript.

### <a name=""></a><a name="1.14.4"/>1.14.4</a>
* npm dokumentace opravena.

### <a name=""></a><a name="1.14.3"/>1.14.3</a>
* Byla přidána podpora pro výchozí pokusy o připojení.
* Přidána podpora pro čtení kanálu změn kolekce.
* Opravena chyba konzistence relace, která občas způsobila "čtení relace není k dispozici".
* Byla přidána podpora pro metriky dotazů.
* Byl změněn maximální počet připojení protokolu HTTP Agent.

### <a name=""></a><a name="1.14.2"/>1.14.2</a>
* Aktualizovaná dokumentace pro odkaz na Azure Cosmos DB namísto Azure DocumentDB.
* Přidána podpora pro nastavení proxyUrl v connectionpolicy.

### <a name=""></a><a name="1.14.1"/>1.14.1</a>
* Menší oprava pro systémy souborů rozlišující malá a velká písmena.

### <a name=""></a><a name="1.14.0"/>1.14.0</a>
* Přidá podporu konzistence relace.
* Tato verze sady SDK vyžaduje nejnovější verzi emulátoru Azure https://aka.ms/cosmosdb-emulatorCosmos DB, která je k dispozici ke stažení z aplikace .

### <a name=""></a><a name="1.13.0"/>1.13.0</a>
* Rozdělit proofed křížové oddíly dotazy.
* Přidá podporu pro propojení prostředků s úvodními a koncovými lomítky (a odpovídajícími testy).

### <a name=""></a><a name="1.12.2"/>1.12.2</a>
*   npm dokumentace opravena.

### <a name=""></a><a name="1.12.1"/>1.12.1</a>
* Opravena chyba v executeStoredProcedure, kde dokumenty měly speciální znaky Unicode (LS, PS).
* Opravena chyba při zpracování dokumentů se znaky Unicode v klíči oddílu.
* Pevná podpora pro vytváření kolekcí s názvem média. Problém GitHubu #114.
* Pevná podpora tokenu autorizace oprávnění. #178 problému GitHubu.

### <a name=""></a><a name="1.12.0"/>1.12.0</a>
* Přidána podpora pro novou [úroveň konzistence](consistency-levels.md) s názvem ConsistentPrefix.
* Přidána podpora pro UriFactory.
* Opravena chyba podpory Unicode. GitHub problém #171.

### <a name=""></a><a name="1.11.0"/>1.11.0</a>
* Přidána podpora pro agregační dotazy (COUNT, MIN, MAX, SUMA a AVG).
* Byla přidána možnost pro řízení stupně paralelismu pro dotazy mezi oddíly.
* Přidána možnost pro zakázání ověření TLS při spuštění proti Emulátoru Azure Cosmos DB.
* Snížená minimální propustnost dělených kolekcí z 10 100 RU/s na 2500 RU/s.
* Opravena chyba tokenu pokračování pro kolekci jednoho oddílu. GitHub problém #107.
* Opravena chyba executeStoredProcedure při zpracování 0 jako jeden param. GitHub problém #155.

### <a name=""></a><a name="1.10.2"/>1.10.2</a>
* Opravena hlavička user-agenta, která zahrnovala verzi sady SDK.
* Menší vyčištění kódu.

### <a name=""></a><a name="1.10.1"/>1.10.1</a>
* Zakázání ověření TLS při použití sady SDK k cílení emulátoru(hostname=localhost).
* Přidána podpora pro povolení protokolování skriptů během provádění uložené procedury.

### <a name=""></a><a name="1.10.0"/>1.10.0</a>
* Přidána podpora paralelních dotazů mezi oddíly.
* Přidána podpora pro dotazy TOP/ORDER BY pro rozdělené kolekce.

### <a name=""></a><a name="1.9.0"/>1.9.0</a>
* Přidána podpora zásad opakování pro omezené požadavky. (Omezené požadavky obdrží příliš velkou výjimku, kód chyby 429.) Ve výchozím nastavení Azure Cosmos DB opakuje devětkrát pro každý požadavek při výskytu kódu chyby 429, ctít retryAfter čas v záhlaví odpovědi. Pevný interval opakování čas lze nyní nastavit jako součást RetryOptions vlastnost na ConnectionPolicy objektu, pokud chcete ignorovat retryAfter čas vrácenservermezi opakování. Azure Cosmos DB teď čeká maximálně 30 sekund pro každý požadavek, který je omezen (bez ohledu na počet opakování) a vrátí odpověď s kódem chyby 429. Tento čas lze také přepsat ve vlastnosti RetryOptions na objektu ConnectionPolicy.
* Cosmos DB nyní vrátí x-ms-throttle-retry-count a x-ms-throttle-retry-wait-time-ms jako hlavičky odezvy v každém požadavku k označení počtu opakování škrticí klapky a kumulativní doby, po kterou požadavek čekal mezi opakovanými pokusy.
* Byla přidána třída RetryOptions, která vystavila vlastnost RetryOptions ve třídě ConnectionPolicy, kterou lze použít k přepsání některých výchozích možností opakování.

### <a name=""></a><a name="1.8.0"/>1.8.0</a>
* Přidána podpora pro databázové účty s více oblastmi.

### <a name=""></a><a name="1.7.0"/>1.7.0</a>
* Přidána podpora funkce Time To Live(TTL) pro dokumenty.

### <a name=""></a><a name="1.6.0"/>1.6.0</a>
* Implementované [rozdělené kolekce](partition-data.md) a [uživatelem definované úrovně výkonu](performance-levels.md).

### <a name=""></a><a name="1.5.6"/>1.5.6</a>
* Opravena chyba RangePartitionResolver.resolveForRead, kde nevracela odkazy kvůli chybnému konkatu výsledků.

### <a name=""></a><a name="1.5.5"/>1.5.5</a>
* OpravenhashPartitionResolver resolveForRead(): Když nebyl zadán žádný klíč oddílu, místo toho, aby vracel seznam všech registrovaných odkazů.

### <a name=""></a><a name="1.5.4"/>1.5.4</a>
* Opravuje problém [#100](https://github.com/Azure/azure-documentdb-node/issues/100) – vyhrazený agent HTTPS: Vyhněte se úpravám globálního agenta pro účely Azure Cosmos DB. Použijte vyhrazenéagent pro všechny požadavky lib.

### <a name=""></a><a name="1.5.3"/>1.5.3</a>
* Opravuje problém [#81](https://github.com/Azure/azure-documentdb-node/issues/81) - Správně zpracovat pomlčky v ID médií.

### <a name=""></a><a name="1.5.2"/>1.5.2</a>
* Opravuje problém [#95](https://github.com/Azure/azure-documentdb-node/issues/95) - EventEmitter naslouchací proces upozornění.

### <a name=""></a><a name="1.5.1"/>1.5.1</a>
* Opravuje problém [#92](https://github.com/Azure/azure-documentdb-node/issues/90) - přejmenujte složku Hash na hash pro systémy rozlišující malá a velká písmena.

### <a name=""></a><a name="1.5.0"/>1.5.0</a>
* Implementujte podporu dělení přidáním překladačů oddílů & rozsahu hash.

### <a name=""></a><a name="1.4.0"/>1.4.0</a>
* Implementovat Upsert. Nové metody upsertXXX na documentClient.

### <a name=""></a><a name="1.3.0"/>1.3.0</a>
* Přeskočeno, aby se čísla verzí zarovnávala s ostatními sadami SDK.

### <a name=""></a><a name="1.2.2"/>1.2.2</a>
* Split Q slibuje obálku do nového úložiště.
* Aktualizace souboru balíčku pro registr npm.

### <a name=""></a><a name="1.2.1"/>1.2.1</a>
* Implementuje směrování založené na ID.
* Opravuje problém [#49](https://github.com/Azure/azure-documentdb-node/issues/49) - aktuální vlastnost konflikty s metodou current().

### <a name=""></a><a name="1.2.0"/>1.2.0</a>
* Přidána podpora pro index GeoSpatial.
* Ověří vlastnost id pro všechny prostředky. ID prostředků nemůže obsahovat ?, /, #, &#47;&#47;, znaky nebo konec mezerou.
* Přidá do položky ResourceResponse nové záhlaví "průběh transformace indexu".

### <a name=""></a><a name="1.1.0"/>1.1.0</a>
* Implementuje zásady indexování V2.

### <a name=""></a><a name="1.0.3"/>1.0.3</a>
* Problém [#40](https://github.com/Azure/azure-documentdb-node/issues/40) - Implementované eslint a grunt konfigurace v jádru a slibují SDK.

### <a name=""></a><a name="1.0.2"/>1.0.2</a>
* Problém [#45](https://github.com/Azure/azure-documentdb-node/issues/45) - Obálka slibů neobsahuje záhlaví s chybou.

### <a name=""></a><a name="1.0.1"/>1.0.1</a>
* Implementovaná schopnost dotazovat se na konflikty přidáním readConflicts, readConflictAsync a queryConflicts.
* Aktualizovaná dokumentace rozhraní API.
* Problém [#41](https://github.com/Azure/azure-documentdb-node/issues/41) - chyba client.createDocumentAsync.

### <a name=""></a><a name="1.0.0"/>1.0.0</a>
* GA SDK.

## <a name="release--retirement-dates"></a>Uvolnit & data odchodu do důchodu
Společnost Microsoft poskytuje oznámení nejméně **12 měsíců** před vyřazením sady SDK za účelem vyhlazení přechodu na novější/podporovanou verzi.

Nové funkce a funkce a optimalizace jsou přidány pouze do aktuální sady SDK, jako takové se doporučuje vždy upgradovat na nejnovější verzi sady SDK co nejdříve.

Jakýkoli požadavek na Cosmos DB pomocí vyřazené sady SDK bude službou odmítnut.

> [!WARNING]
> Všechny verze **1.x** sady SDK klienta uzlu pro rozhraní SQL API budou **vyřazeny 30.** To má vliv pouze na klientskou uzlu SDK a nemá vliv na skripty na straně serveru (uložené procedury, aktivační události a ufls).
> 
>
<br/>

| Version | Datum vydání | Datum odchodu do důchodu |
| --- | --- | --- |
| [3.1.0](#3.1.0) |26. července 2019July 26, 2019 |--- |
| [3.0.4](#3.0.4) |22. července 2019July 22, 2019 |--- |
| [3.0.3](#3.0.3) |17. července 2019July 17, 2019 |--- |
| [3.0.2](#3.0.2) |9. července 2019 |--- |
| [3.0.0](#3.0.0) |28. června 2019 |--- |
| [2.1.5](#2.1.5) |20. března 2019 |--- |
| [2.1.4](#2.1.4) |15. března 2019 |--- |
| [2.1.3](#2.1.3) |8. března 2019 |--- |
| [2.1.2](#2.1.2) |28. ledna 2019 January 28, 2019 |--- |
| [2.1.1](#2.1.1) |5. prosince 2018 |--- |
| [2.1.0](#2.1.0) |4. prosince 2018 |--- |
| [2.0.5](#2.0.5) |7. listopadu 2018November 7, 2018 |--- |
| [2.0.4](#2.0.4) |30. října 2018October 30, 2018 |--- |
| [2.0.3](#2.0.3) |30. října 2018October 30, 2018 |--- |
| [2.0.2](#2.0.2) |10. října 2018October 10, 2018 |--- |
| [2.0.1](#2.0.1) |25. září 2018 |--- |
| [2.0.0](#2.0.0) |Září 24, 2018 |--- |
| [2.0.0-3 (RC)](#2.0.0-3) |2. srpna 2018August 2, 2018 |--- |
| [1.14.4](#1.14.4) |Květen 03, 2018 |30. srpna 2020 |
| [1.14.3](#1.14.3) |Květen 03, 2018 |30. srpna 2020 |
| [1.14.2](#1.14.2) |21. prosince 2017December 21, 2017 |30. srpna 2020 |
| [1.14.1](#1.14.1) |10. listopadu 2017November 10, 2017 |30. srpna 2020 |
| [1.14.0](#1.14.0) |9. listopadu 2017November 9, 2017 |30. srpna 2020 |
| [1.13.0](#1.13.0) |11. října 2017October 11, 2017 |30. srpna 2020 |
| [1.12.2](#1.12.2) |10. srpna 2017August 10, 2017 |30. srpna 2020 |
| [1.12.1](#1.12.1) |10. srpna 2017August 10, 2017 |30. srpna 2020 |
| [1.12.0](#1.12.0) |10. května 2017 |30. srpna 2020 |
| [1.11.0](#1.11.0) |16. března 2017 March 16, 2017 |30. srpna 2020 |
| [1.10.2](#1.10.2) |27. ledna 2017 January 27, 2017 |30. srpna 2020 |
| [1.10.1](#1.10.1) |22. prosince 2016December 22, 2016 |30. srpna 2020 |
| [1.10.0](#1.10.0) |Říjen 03, 2016 |30. srpna 2020 |
| [1.9.0](#1.9.0) |7. července 2016July 07, 2016 |30. srpna 2020 |
| [1.8.0](#1.8.0) |14. června 2016 |30. srpna 2020 |
| [1.7.0](#1.7.0) |26. dubna 2016 |30. srpna 2020 |
| [1.6.0](#1.6.0) |29. března 2016March 29, 2016 |30. srpna 2020 |
| [1.5.6](#1.5.6) |Březen 08, 2016 |30. srpna 2020 |
| [1.5.5](#1.5.5) |Únor 02, 2016 |30. srpna 2020 |
| [1.5.4](#1.5.4) |Únor 01, 2016 |30. srpna 2020 |
| [1.5.2](#1.5.2) |26. ledna 2016 January 26, 2016 |30. srpna 2020 |
| [1.5.2](#1.5.2) |22. ledna 2016 January 22, 2016 |30. srpna 2020 |
| [1.5.1](#1.5.1) |4. ledna 2016 January 4, 2016 |30. srpna 2020 |
| [1.5.0](#1.5.0) |31. prosince 2015 December 31, 2015 |30. srpna 2020 |
| [1.4.0](#1.4.0) |Říjen 06, 2015 |30. srpna 2020 |
| [1.3.0](#1.3.0) |Říjen 06, 2015 |30. srpna 2020 |
| [1.2.2](#1.2.2) |10. září 2015September 10, 2015 |30. srpna 2020 |
| [1.2.1](#1.2.1) |15. srpna 2015August 15, 2015 |30. srpna 2020 |
| [1.2.0](#1.2.0) |Srpen 05, 2015 |30. srpna 2020 |
| [1.1.0](#1.1.0) |09. července 2015 July 09, 2015 |30. srpna 2020 |
| [1.0.3](#1.0.3) |600. |30. srpna 2020 |
| [1.0.2](#1.0.2) |23. května 2015May 23, 2015 |30. srpna 2020 |
| [1.0.1](#1.0.1) |15. května 2015May 15, 2015 |30. srpna 2020 |
| [1.0.0](#1.0.0) |Duben 08, 2015 |30. srpna 2020 |

## <a name="faq"></a>Nejčastější dotazy
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Viz také
Další informace o Službě Cosmos DB najdete na stránce [služby Microsoft Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/)

