---
title: 'Azure Cosmos DB: API Java asynchronní SQL, sadu SDK a prostředky | Microsoft Docs'
description: Další informace o rozhraní API Java asynchronní SQL a sady SDK, včetně data vydání, vyřazení dat a změny provedené mezi každou verzi sady Azure Cosmos DB SQL asynchronní Java SDK.
services: cosmos-db
documentationcenter: java
author: SnehaGunda
manager: kfile
ms.assetid: a452ffa2-c15d-4b0a-a8c1-ec9b750ce52b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 05/18/2018
ms.author: sngun
ms.openlocfilehash: 9dae401bc007b78d8ee3c6993735650e3b26b9d1
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB asynchronní Java SDK pro rozhraní API pro SQL: poznámky k verzi a prostředky
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

SQL API asynchronní Java SDK se liší od Java SDK pro rozhraní API SQL tím, že poskytuje asynchronní operace se podporuje [Netty knihovny](http://netty.io/). Již existující [SQL API Java SDK](sql-api-sdk-java.md) nepodporuje asynchronní operace. 

<table>

<tr><td>**Stažení sady SDK**</td><td>[Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)</td></tr>

<tr><td>**Dokumentaci k rozhraní API**</td><td>[Referenční dokumentace rozhraní API Java](https://azure.github.io/azure-cosmosdb-java/)</td></tr>

<tr><td>**Můžete přispět k sadě SDK**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-java)</td></tr>

<tr><td>**Začínáme**</td><td>[Začínáme s Async Java SDK](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started)</td></tr>

<tr><td>**Ukázka kódu**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)</td></tr>

<tr><td>**Tipy pro zvýšení výkonu**</td><td>[Soubor readme Githubu](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)</td></tr>

<tr><td>**Minimální podporovaný modul runtime**</td><td>[JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)</td></tr>
</table></br>

## <a name="release-notes"></a>Poznámky k verzi

### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2
* Přidaná podpora pro jedinečný Index zásad.
* Přidaná podpora pro omezení velikosti odpovědi token pokračování v možnostech informačního kanálu.
* Přidaná podpora pro rozdělení oddílů v křížové dotaz oddílu.
* Pevné chyby v serializaci Json časové razítko ([githubu #32](https://github.com/Azure/azure-cosmosdb-java/issues/32)).
* Pevná chyby ve výčtu serializace Json.
* Pevné chyb při správě dokumentů velikosti 2MB ([githubu #. 33](https://github.com/Azure/azure-cosmosdb-java/issues/33)).
* Závislost com.fasterxml.jackson.core:jackson-databind upgradovat na 2.9.5 z důvodu chyby ([jackson databind: githubu #1599](https://github.com/FasterXML/jackson-databind/issues/1599))
* Závislost na upgradovat na 0.8.0.17 z důvodu chyby rxjava – funkce ([funkce rxjava: githubu #30](https://github.com/davidmoten/rxjava-extras/issues/30)).
* Popis metadata v souboru pom aktualizován za účelem vložené se zbytkem dokumentaci.
* Syntaxe zlepšování ([githubu #41](https://github.com/Azure/azure-cosmosdb-java/issues/41)), ([githubu #40](https://github.com/Azure/azure-cosmosdb-java/issues/40)).

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Byla přidána podpora back přetížení v dotazu.
* Přidaná podpora pro id klíče rozsahu oddílu v dotazu.
* Opravu, která umožňuje větší token pokračování v hlavičce žádosti (bugfix githubu #24).
* netty závislostí upgradovat na 4.1.22.Final zajistit JVM vypne po dokončení hlavního vlákna.
* Opravte předejdete předání relace token při čtení hlavní prostředky.
* Přidat další příklady.
* Přidat další testu typovou úlohou scénáře.
* Soubory hlaviček pevné Java pro generování doc správné java.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK s podporou začátku do konce neblokující vstupně-výstupní operace pomocí [Netty knihovny](http://netty.io/) v režimu brány. 

## <a name="release-and-retirement-dates"></a>Datum vydání a vyřazování z provozu
Microsoft bude poskytovat oznámení alespoň **dobu 12 měsíců** předem vyřazení sady SDK k funkce smooth přechodu na novější nebo podporované verzi.

Nové funkce a funkce a optimalizace, jsou přidány pouze v aktuální sadě SDK. Proto se doporučuje, že jste vždy upgrade na nejnovější verzi sady SDK co nejdříve.

Každá žádost o DB Cosmos pomocí vyřazeno sady SDK budou odmítnuty službou.

<br/>

| Verze | Datum vydání | Datum vyřazení |
| --- | --- | --- |
| [1.0.2](#1.0.2) |18 může 2018|--- |
| [1.0.1](#1.0.1) |20 duben 2018|--- |
| [1.0.0](#1.0.0) |27. února 2018|--- |

## <a name="faq"></a>Nejčastější dotazy
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Další informace najdete v tématech
Další informace o Cosmos DB najdete v tématu [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stránku služby.

