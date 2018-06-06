---
title: 'Azure Cosmos DB: Rozhraní API Node.js SQL, sadu SDK a prostředky | Microsoft Docs'
description: Další informace o rozhraní API Node.js SQL a sady SDK, včetně data vydání, vyřazení dat a změny provedené mezi každou verzi sady Azure Cosmos DB Node.js SDK.
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
ms.openlocfilehash: 94d60325fa53668628ddefa68e8ce313c4fa75c6
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34797841"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Node.js SDK pro rozhraní API pro SQL: poznámky k verzi a prostředky
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Informační kanál změnu rozhraní .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Poskytovatel prostředků REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [BulkExecutor - rozhraní .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

<table>

<tr><td>**Stáhněte si sadu SDK**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>

<tr><td>**Dokumentaci k rozhraní API**</td><td>[Referenční dokumentace rozhraní API Node.js](https://docs.microsoft.com/javascript/api/documentdb/?view=azure-node-latest)</td></tr>

<tr><td>**Pokyny k instalaci sady SDK**</td><td>[Pokyny k instalaci](http://azure.github.io/azure-documentdb-node/)</td></tr>

<tr><td>**Můžete přispět k sadě SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>

<tr><td>**Ukázky**</td><td>[Ukázky kódu Node.js](sql-api-nodejs-samples.md)</td></tr>

<tr><td>**Kurz Začínáme**</td><td>[Začínáme s Node.js SDK](sql-api-nodejs-get-started.md)</td></tr>

<tr><td>**Kurz vývoje webové aplikace**</td><td>[Vytvoření webové aplikace Node.js pomocí Azure Cosmos DB](sql-api-nodejs-application.md)</td></tr>

<tr><td>**Aktuální podporované platformy**</td><td> 
[Verze 6.x Node.js](https://nodejs.org/en/blog/release/v6.10.3/)<br/> 
[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)<br/> 
[Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/> 
[Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/) 
</td></tr>
</table></br>

## <a name="release-notes"></a>Poznámky k verzi

### <a name="1.14.4"/>1.14.4</a>
* dokumentace npm pevné.

### <a name="1.14.3"/>1.14.3</a>
* Přidaná podpora pro výchozí opakování pokusů pro potíže s připojením.
* Přidaná podpora číst změnu kolekce informačního kanálu.
* Chyb konzistence pevné relace, která občas způsobila "čtení relace není k dispozici".
* Přidaná podpora pro dotaz metriky.
* Změnit maximální počet připojení protokolu http agenta.

### <a name="1.14.2"/>1.14.2</a>
* Aktualizované dokumentace k odkazu na Azure DB Cosmos místo Azure DocumentDB.
* Přidaná podpora pro nastavení proxyUrl v ConnectionPolicy.

### <a name="1.14.1"/>1.14.1</a>
* Menší opravy pro systémy souborů velká a malá písmena.

### <a name="1.14.0"/>1.14.0</a>
* Přidává podporu pro konzistence typu relace.
* Tato verze sady SDK vyžaduje nejnovější verze emulátoru DB Cosmos Azure k dispozici ke stažení https://aka.ms/cosmosdb-emulator.

### <a name="1.13.0"/>1.13.0</a>
* Rozdělení zabezpečeny křížové dotazy oddílu.
* Přidá podporuje pro prostředek odkaz s úvodní a koncové lomítka (a odpovídající testů).

### <a name="1.12.2"/>1.12.2</a>
*   dokumentace npm pevné.

### <a name="1.12.1"/>1.12.1</a>
* Pevná chyby ve executeStoredProcedure kde dokumentů, které měl speciální znaky znakové sady Unicode (LS, PS).
* Opravit chyby při zpracování dokumentů s znaky kódování Unicode v klíč oddílu.
* Opravené podpora pro vytváření kolekcí s názvem média. Problém Githubu #114.
* Opravené podpora oprávnění autorizační token. Problém Githubu #178.

### <a name="1.12.0"/>1.12.0</a>
* Přidaná podpora pro novou [úroveň konzistence](consistency-levels.md) názvem ConsistentPrefix.
* Přidaná podpora pro UriFactory.
* Opravit chyby Podpora kódování Unicode. #171 potíže Githubu.

### <a name="1.11.0"/>1.11.0</a>
* Přidaná podpora pro dotazy agregace (COUNT, MIN, MAX, součet a průměr).
* Přidání možnosti pro řízení stupně paralelního zpracování pro různé dotazy oddílu.
* Přidání možnosti pro zákaz protokolu SSL ověření při spuštění emulátoru DB Cosmos Azure.
* Snížena minimální propustnosti na dělené kolekce z 10,100 RU/s na 2 500 RU/s.
* Opravit chyby token pokračování pro kolekce tvořené jedním oddílem. Problém Githubu #107.
* Opravit chyby executeStoredProcedure při zpracování 0 jako jeden param. Problém Githubu #155.

### <a name="1.10.2"/>1.10.2</a>
* Záhlaví pevné user-agent zahrnout verze sady SDK.
* Méně závažné kód čištění.

### <a name="1.10.1"/>1.10.1</a>
* Zakázání ověřování SSL, když pomocí sady SDK cílit emulator(hostname=localhost).
* Přidaná podpora pro povolení protokolování skriptu během provádění uložené procedury.

### <a name="1.10.0"/>1.10.0</a>
* Přidaná podpora pro křížové paralelní dotazy oddílu.
* Byla přidána podpora pro horní nebo ORDER BY a dotazy pro dělené kolekce.

### <a name="1.9.0"/>1.9.0</a>
* Podpora zásad přidané opakování omezenému požadavky. (Omezenému požadavky obdrží žádost o míra příliš velký výjimka, kód chyby 429.) Ve výchozím nastavení Azure Cosmos DB opakuje devětkrát pro každý požadavek vyskytne kód chyby 429, aby byla dodržena retryAfter čas v hlavičku odpovědi. Časový interval opakování pevné lze nyní nastavit jako součást RetryOptions vlastnost v objektu ConnectionPolicy Pokud budete chtít ignorovat čas retryAfter vrácená serverem mezi jednotlivými pokusy o odeslání. Azure Cosmos DB nyní čeká maximálně 30 sekund pro každý požadavek, který je omezené (bez ohledu na počet opakování) a vrátí odpověď s kódem chyby 429. Nyní lze přepsat také ve vlastnosti RetryOptions ConnectionPolicy objektu.
* Cosmos DB nyní vrátí x-ms omezení--počet opakování a x-ms-throttle-retry-wait-time-ms jako opakovat hlavičky odpovědi v každé žádosti k označení omezení počtu a kumulativní čas požadavku čekali mezi jednotlivými pokusy o odeslání.
* Třída RetryOptions byla přidána vystavení vlastnost RetryOptions na ConnectionPolicy třídu, která slouží k některé z možností opakování výchozí přepsat.

### <a name="1.8.0"/>1.8.0</a>
* Přidaná podpora pro účty databáze více oblast.

### <a name="1.7.0"/>1.7.0</a>
* Přidaná podpora pro funkce čas k Live(TTL) pro dokumenty.

### <a name="1.6.0"/>1.6.0</a>
* Implementovat [oddíly kolekce](partition-data.md) a [úrovně výkonu uživatelem definované](performance-levels.md).

### <a name="1.5.6"/>1.5.6</a>
* Opravené chyby RangePartitionResolver.resolveForRead, kde ji nebyl vrácení odkazy z důvodu chybné concat výsledků.

### <a name="1.5.5"/>1.5.5</a>
* Pevné hashParitionResolver resolveForRead(): žádné předaný klíč oddílu byla při vyvolání výjimky, místo vrací seznam všech registrovaných odkazů.

### <a name="1.5.4"/>1.5.4</a>
* Řeší problém [#100](https://github.com/Azure/azure-documentdb-node/issues/100) -vyhrazené agenta HTTPS: neměli upravovat globální agenta pro účely Azure Cosmos DB. Použijte vyhrazenou agenta pro všechny požadavky lib.

### <a name="1.5.3"/>1.5.3</a>
* Řeší problém [#81](https://github.com/Azure/azure-documentdb-node/issues/81) – správně zpracovat pomlčky v ID média.

### <a name="1.5.2"/>1.5.2</a>
* Řeší problém [#95](https://github.com/Azure/azure-documentdb-node/issues/95) -naslouchací proces EventEmitter úniku upozornění.

### <a name="1.5.1"/>1.5.1</a>
* Řeší problém [#92](https://github.com/Azure/azure-documentdb-node/issues/90) -přejmenovat složku Hash na hodnotu hash pro systémy malá a velká písmena.

### <a name="1.5.0"/>1.5.0</a>
* Podpora horizontálního dělení implementace přidáním hash p & ro překladače oddílu.

### <a name="1.4.0"/>1.4.0</a>
* Implementujte Upsert. Nové metody upsertXXX na documentClient.

### <a name="1.3.0"/>1.3.0</a>
* Přeskočeno mají být předány čísla verzí zarovnání s dalších sadách SDK.

### <a name="1.2.2"/>1.2.2</a>
* Rozdělení Q nabízí obálku do nového úložiště.
* Aktualizace k souboru balíčku pro npm registru.

### <a name="1.2.1"/>1.2.1</a>
* Implementuje ID založené na směrování.
* Řeší problém [#49](https://github.com/Azure/azure-documentdb-node/issues/49) -aktuální vlastnost je v konfliktu s má objekt current() metoda.

### <a name="1.2.0"/>1.2.0</a>
* Byla přidána podpora pro geoprostorové index.
* Ověří vlastnost id pro všechny prostředky. Identifikátory prostředků nesmí obsahovat?, /, #, &#47; &#47;, znaky nebo končit mezerou.
* Přidá nové záhlaví "index transformace průběh" ResourceResponse.

### <a name="1.1.0"/>1.1.0</a>
* Implementuje zásady indexování V2.

### <a name="1.0.3"/>1.0.3</a>
* Problém [#40](https://github.com/Azure/azure-documentdb-node/issues/40) – implementována eslint grunt konfigurace v základní a promise SDK.

### <a name="1.0.2"/>1.0.2</a>
* Problém [#45](https://github.com/Azure/azure-documentdb-node/issues/45) -lišící obálku nezahrnuje hlavičky s chybou.

### <a name="1.0.1"/>1.0.1</a>
* Implementovaná možnost dotazu pro přidáním readConflicts, readConflictAsync a queryConflicts je v konfliktu.
* Aktualizované dokumentace rozhraní API.
* Problém [#41](https://github.com/Azure/azure-documentdb-node/issues/41) -client.createDocumentAsync chyby.

### <a name="1.0.0"/>1.0.0</a>
* GA SDK.

## <a name="release--retirement-dates"></a>Verze & vyřazení kalendářních dat
Společnost Microsoft poskytuje oznámení alespoň **dobu 12 měsíců** předem vyřazení sady SDK k funkce smooth přechodu na novější nebo podporované verzi.

Nové funkce a funkce a optimalizace, jsou přidány pouze v aktuální sadě SDK, jako takový se doporučuje, aby vždy upgradu na nejnovější verze sady SDK v míře.

Každá žádost o pomocí Cosmos DB, že je vyřazeno SDK odmítnuta službou.

<br/>

| Verze | Datum vydání | Datum vyřazení |
| --- | --- | --- |
| [1.14.4](#1.14.4) |03 může 2018 |--- |
| [1.14.3](#1.14.3) |03 může 2018 |--- |
| [1.14.2](#1.14.2) |21 prosince 2017 |--- |
| [1.14.1](#1.14.1) |10 listopadu 2017 |--- |
| [1.14.0](#1.14.0) |9 listopadu 2017 |--- |
| [1.13.0](#1.13.0) |11 říjen 2017 |--- |
| [1.12.2](#1.12.2) |10. srpnu 2017 |--- |
| [1.12.1](#1.12.1) |10. srpnu 2017 |--- |
| [1.12.0](#1.12.0) |10. května 2017 |--- |
| [1.11.0](#1.11.0) |16 března 2017 |--- |
| [1.10.2](#1.10.2) |27. ledna 2017 |--- |
| [1.10.1](#1.10.1) |22. prosinci 2016 |--- |
| [1.10.0](#1.10.0) |03. října 2016 |--- |
| [1.9.0](#1.9.0) |07 července 2016 |--- |
| [1.8.0](#1.8.0) |14. června 2016 |--- |
| [1.7.0](#1.7.0) |26. dubna 2016 |--- |
| [1.6.0](#1.6.0) |29. března 2016 |--- |
| [1.5.6](#1.5.6) |08 března 2016 |--- |
| [1.5.5](#1.5.5) |02. února 2016 |--- |
| [1.5.4](#1.5.4) |01. února 2016 |--- |
| [1.5.2](#1.5.2) |26 leden 2016 |--- |
| [1.5.2](#1.5.2) |22. ledna 2016 |--- |
| [1.5.1](#1.5.1) |4 leden 2016 |--- |
| [1.5.0](#1.5.0) |31. prosince 2015 |--- |
| [1.4.0](#1.4.0) |06 říjen 2015 |--- |
| [1.3.0](#1.3.0) |06 říjen 2015 |--- |
| [1.2.2](#1.2.2) |10 září 2015 |--- |
| [1.2.1](#1.2.1) |15 srpen 2015 |--- |
| [1.2.0](#1.2.0) |05 srpen 2015 |--- |
| [1.1.0](#1.1.0) |09 července 2015 |--- |
| [1.0.3](#1.0.3) |04 červen 2015 |--- |
| [1.0.2](#1.0.2) |23 květen 2015 |--- |
| [1.0.1](#1.0.1) |15. května 2015 |--- |
| [1.0.0](#1.0.0) |08 duben 2015 |--- |

## <a name="faq"></a>Nejčastější dotazy
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Další informace najdete v tématech
Další informace o Cosmos DB najdete v tématu [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stránku služby.

