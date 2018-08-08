---
title: 'Azure Cosmos DB: SQL Node.js API, SDK & zdroje | Dokumentace Microsoftu'
description: Další informace o SQL Node.js API a sady SDK, včetně data vydání, vyřazení dat a změny provedené mezi každou verzi sady Azure Cosmos DB Node.js SDK.
services: cosmos-db
author: rnagpal
manager: kfile
editor: cgronlun
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 5/3/2018
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e19c1cb7b297d2537e969e0dd632dae3e1c3d211
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39594347"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Node.js SDK pro rozhraní SQL API: poznámky k verzi a prostředky
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Kanál změn .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Poskytovatel prostředků REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [BulkExecutor – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor – Java](sql-api-sdk-bulk-executor-java.md)

|Prostředek  |Odkaz  |
|---------|---------|
|Stáhnout sadu SDK  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|Dokumentace API  |  [Referenční dokumentace jazyka JavaScript SDK](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest)
|Pokyny k instalaci sady SDK  |  [Pokyny k instalaci](https://github.com/Azure/azure-cosmos-js#installation)
|Přispívat k sadě SDK | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| Ukázky | [Ukázky kódu Node.js](sql-api-nodejs-samples-preview.md)
| Kurz Začínáme | [Začínáme se sadou SDK jazyka JavaScript](sql-api-nodejs-get-started-preview.md)
| Kurz vývoje webové aplikace | [Vytvoření webové aplikace Node.js využívající službu Azure Cosmos DB](sql-api-nodejs-application-preview.md)
| Aktuální podporovanou platformu | [Node.js verze 6.x](https://nodejs.org/en/blog/release/v6.10.3/) – povinný argument pro sadu SDK verze 2.0.0 a vyšší.<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)<br/> [Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/> [Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/) 

## <a name="release-notes"></a>Poznámky k verzi

### <a name="2.0.0-3"/>2.0.0-3</a>
* RC1 JavaScript SDK verze 2.0.0 ve veřejné verzi preview.
* Nový model objektu, pomocí metod a nejvyšší úrovně CosmosClient rozdělit mezi příslušné databáze, kontejneru a položku třídy. 
* Podpora pro [slibuje](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). 
* Sada SDK převést na TypeScript.

### <a name="1.14.4"/>1.14.4</a>
* npm dokumentace opravila.

### <a name="1.14.3"/>1.14.3</a>
* Přidání podpory pro výchozí opakovaných pokusů o problémech s připojením.
* Přidání podpory pro čtení změnit kolekci informačního kanálu.
* Pevné relace chyb konzistence, která někdy způsobovala "čtení relace není k dispozici".
* Přidání podpory pro dotaz metriky.
* Změnit maximální počet připojení http agenta.

### <a name="1.14.2"/>1.14.2</a>
* Aktualizace dokumentace k odkazu na službu Azure Cosmos DB místo Azure DocumentDB.
* Přidání podpory pro nastavení adresa URL proxy serveru v ConnectionPolicy.

### <a name="1.14.1"/>1.14.1</a>
* Menší opravy pro systémy souborů velká a malá písmena.

### <a name="1.14.0"/>1.14.0</a>
* Přidává podporu pro konzistence typu relace.
* Tato verze sady SDK vyžaduje nejnovější verzi Azure emulátor služby Cosmos DB k dispozici ke stažení z https://aka.ms/cosmosdb-emulator.

### <a name="1.13.0"/>1.13.0</a>
* Rozdělení zabezpečeny pro různé dotazy oddílu.
* Přidá podporuje pro odkaz na prostředek s úvodní a koncové lomítka (a odpovídající testy).

### <a name="1.12.2"/>1.12.2</a>
*   npm dokumentace opravila.

### <a name="1.12.1"/>1.12.1</a>
* Oprava chyby v executeStoredProcedure kde dokumentů, které má speciální znaky kódování Unicode (LS, PS).
* Opravili jsme chybu při zpracování dokumentů se znaky Unicode v klíč oddílu.
* Oprava podpory pro vytvoření kolekce s názvem média. #114 problém Githubu.
* Oprava podpory pro oprávnění autorizační token. #178 problém Githubu.

### <a name="1.12.0"/>1.12.0</a>
* Přidali jsme podporu pro nové [úrovně konzistence](consistency-levels.md) volá ConsistentPrefix.
* Přidání podpory pro UriFactory.
* Je opravená chyba, podporu kódování Unicode. #171 problém Githubu.

### <a name="1.11.0"/>1.11.0</a>
* Přidání podpory pro dotazy agregace (počet, MIN, MAX, součet a průměr).
* Přidání možnosti pro řízení stupeň paralelismu pro různé dotazy oddílu.
* Přidat možnost pro zakázání ověření SSL při spouštění emulátoru služby Azure Cosmos DB.
* Snížili minimální propustnost na dělené kolekce z 10,100 RU/s na 2 500 RU/s.
* Opravili jsme chybu token pokračování pro kolekce jednoho oddílu. #107 problém Githubu.
* Opravili jsme chybu executeStoredProcedure zpracování 0 jako jeden parametr. #155 problém Githubu.

### <a name="1.10.2"/>1.10.2</a>
* Oprava identifikační záhlaví zahrnout verzi sady SDK.
* Vyčištění drobné úpravy v kódu.

### <a name="1.10.1"/>1.10.1</a>
* Zakázání ověření SSL při použití sady SDK pro cílení emulator(hostname=localhost).
* Přidání podpory pro povolení protokolování skriptu při spuštění uložené procedury.

### <a name="1.10.0"/>1.10.0</a>
* Přidání podpory pro různé paralelní dotazy oddílu.
* Přidání podpory pro TOP/ORDER BY dotazů používejte u dělených kolekcí.

### <a name="1.9.0"/>1.9.0</a>
* Podpora zásad přidané opakování pro omezené požadavky. (Omezené požadavky přijímat žádost o rychlost příliš velký výjimky, kód chyby 429.) Ve výchozím nastavení služby Azure Cosmos DB zopakuje pokus o devětkrát pro každý požadavek vyskytne kód chyby 429 dodržením retryAfter čas v hlavičce odpovědi. Časový interval opakování pevné můžete nyní nastavit jako součást vlastnost RetryOptions objekt ConnectionPolicy Pokud chcete ignorovat retryAfter čas vrácená serverem mezi opakované pokusy. Azure Cosmos DB teď čeká maximálně 30 sekund pro každý požadavek, který je omezovaná (bez ohledu na počet opakování) a vrátí odpověď s kódem chyby 429. Tentokrát lze také přepsat ve vlastnosti RetryOptions ConnectionPolicy objektu.
* Cosmos DB nyní vrátí x-ms omezení--počet opakování a x-ms-throttle-retry-wait-time-ms hlavičky odpovědi v každé žádosti k označení akcelerátor opakovat počet a kumulativní doba požadavku čekání mezi opakované pokusy.
* Třída RetryOptions byl přidán vystavuje vlastnost RetryOptions ConnectionPolicy třídu, která je možné přepsat některé výchozí možnosti opakování.

### <a name="1.8.0"/>1.8.0</a>
* Přidání podpory pro účty databáze ve více oblastech.

### <a name="1.7.0"/>1.7.0</a>
* Přidání podpory pro čas na Live(TTL) funkce na dokumenty.

### <a name="1.6.0"/>1.6.0</a>
* Implementovat [dělené kolekce](partition-data.md) a [uživatelsky definovaným výkonem úrovně](performance-levels.md).

### <a name="1.5.6"/>1.5.6</a>
* Oprava chyby RangePartitionResolver.resolveForRead, kde ji se vrací odkazy z důvodu chybné concat výsledků.

### <a name="1.5.5"/>1.5.5</a>
* Oprava hashParitionResolver resolveForRead(): není zadaný klíč oddílu byl při vyvolání výjimky, místo vrácení seznamu všechny registrované odkazů.

### <a name="1.5.4"/>1.5.4</a>
* Oprava potíží [#100](https://github.com/Azure/azure-documentdb-node/issues/100) – vyhrazený Agent HTTPS: neměli upravovat globální agenta pro účely služby Azure Cosmos DB. Použijte vyhrazený agent pro všechny požadavky na nástroj lib.

### <a name="1.5.3"/>1.5.3</a>
* Oprava potíží [#81](https://github.com/Azure/azure-documentdb-node/issues/81) – správně zpracovat pomlčky v ID média.

### <a name="1.5.2"/>1.5.2</a>
* Oprava potíží [#95](https://github.com/Azure/azure-documentdb-node/issues/95) -naslouchací proces EventEmitter způsobit únik těchto upozornění.

### <a name="1.5.1"/>1.5.1</a>
* Oprava potíží [#92](https://github.com/Azure/azure-documentdb-node/issues/90) -přejmenovat složku hodnota Hash algoritmu hash pro systémy malá a velká písmena.

### <a name="1.5.0"/>1.5.0</a>
* Implementace horizontálního dělení podpora přidáním hodnoty hash & Rozsah oddílu překladačů.

### <a name="1.4.0"/>1.4.0</a>
* Implementace Upsert. Nové metody upsertXXX na documentClient.

### <a name="1.3.0"/>1.3.0</a>
* Přeskočeno čísla verzí pro soulad s jiných sad SDK.

### <a name="1.2.2"/>1.2.2</a>
* Rozdělení Q slibuje obálku pro nové úložiště.
* Aktualizace balíčku v souboru registru npm.

### <a name="1.2.1"/>1.2.1</a>
* Implementuje ID založené na směrování.
* Oprava potíží [#49](https://github.com/Azure/azure-documentdb-node/issues/49) -aktuální vlastnost je v konfliktu s metoda má objekt current().

### <a name="1.2.0"/>1.2.0</a>
* Přidání podpory pro geoprostorové index.
* Ověří vlastnost id pro všechny prostředky. ID prostředků nemůže obsahovat?, /, #, &#47; &#47;, znaky ani končit mezerou.
* Přidá nové záhlaví "index transformace průběh" ResourceResponse.

### <a name="1.1.0"/>1.1.0</a>
* Implementuje zásady indexování V2.

### <a name="1.0.3"/>1.0.3</a>
* Problém [#40](https://github.com/Azure/azure-documentdb-node/issues/40) – implementované eslint grunt konfigurací v základní a zaručit SDK.

### <a name="1.0.2"/>1.0.2</a>
* Problém [#45](https://github.com/Azure/azure-documentdb-node/issues/45) – obálky příslibů neobsahuje záhlaví s chybou.

### <a name="1.0.1"/>1.0.1</a>
* Implementováno možnost dotazu konflikty přidáním readConflicts readConflictAsync a queryConflicts.
* Aktualizace dokumentace k rozhraní API.
* Problém [#41](https://github.com/Azure/azure-documentdb-node/issues/41) -client.createDocumentAsync chyby.

### <a name="1.0.0"/>1.0.0</a>
* GA SDK.

## <a name="release--retirement-dates"></a>Verze & data vyřazení z provozu
Společnost Microsoft poskytuje oznámení alespoň **12 měsíců** před vyřazením z provozu sady SDK ulehčení přechodu na verzi novější nebo podporované.

Nové funkce a funkce a optimalizace jsou přidány pouze aktuální sadu SDK, proto se doporučuje, vždy upgradovat na nejnovější SDK verzi co možná nejdříve.

Jakákoli žádost pomocí služby Cosmos DB je vyřazeno SDK zamítne službou.

<br/>

| Verze | Datum vydání | Datum vyřazení z provozu |
| --- | --- | --- |
| [2.0.0-3 (RC)](#2.0.0-3) |2. srpna 2018 |--- |
| [1.14.4](#1.14.4) |03. května 2018 |--- |
| [1.14.3](#1.14.3) |03. května 2018 |--- |
| [1.14.2](#1.14.2) |21. prosince 2017 |--- |
| [1.14.1](#1.14.1) |10. listopadu 2017 |--- |
| [1.14.0](#1.14.0) |9. listopadu 2017 |--- |
| [1.13.0](#1.13.0) |11. října 2017 |--- |
| [1.12.2](#1.12.2) |10. srpna 2017 |--- |
| [1.12.1](#1.12.1) |10. srpna 2017 |--- |
| [1.12.0](#1.12.0) |10. května 2017 |--- |
| [1.11.0](#1.11.0) |16. března 2017 |--- |
| [1.10.2](#1.10.2) |27. ledna 2017 |--- |
| [1.10.1](#1.10.1) |22. prosince 2016 |--- |
| [1.10.0](#1.10.0) |03. října 2016 |--- |
| [1.9.0](#1.9.0) |07. července 2016 |--- |
| [1.8.0](#1.8.0) |14. června 2016 |--- |
| [1.7.0](#1.7.0) |26. dubna 2016 |--- |
| [1.6.0](#1.6.0) |29. března 2016 |--- |
| [1.5.6](#1.5.6) |08. března 2016 |--- |
| [1.5.5](#1.5.5) |02. února 2016 |--- |
| [1.5.4](#1.5.4) |01. února 2016 |--- |
| [1.5.2](#1.5.2) |26. ledna 2016 |--- |
| [1.5.2](#1.5.2) |22. ledna 2016 |--- |
| [1.5.1](#1.5.1) |4. ledna 2016 |--- |
| [1.5.0](#1.5.0) |31. prosince 2015 |--- |
| [1.4.0](#1.4.0) |06. října 2015 |--- |
| [1.3.0](#1.3.0) |06. října 2015 |--- |
| [1.2.2](#1.2.2) |10. září 2015 |--- |
| [1.2.1](#1.2.1) |15. srpna 2015 |--- |
| [1.2.0](#1.2.0) |05. srpna 2015 |--- |
| [1.1.0](#1.1.0) |09. července 2015 |--- |
| [1.0.3](#1.0.3) |04 červen 2015 |--- |
| [1.0.2](#1.0.2) |23. května 2015 |--- |
| [1.0.1](#1.0.1) |15. května 2015 |--- |
| [1.0.0](#1.0.0) |08. dubna 2015 |--- |

## <a name="faq"></a>Nejčastější dotazy
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Další informace najdete v tématech
Další informace o službě Cosmos DB najdete v tématu [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stránku služby.

