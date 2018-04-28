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
ms.date: 03/20/2018
ms.author: sngun
ms.openlocfilehash: b80ad9837939af5406989d08e18f6f3d9fe3064f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
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
> 
> 

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

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Byla přidána podpora back přetížení v dotazu.
* Přidaná podpora pro id klíče rozsahu oddílu v dotazu.
* Opravu, která umožňuje větší token pokračování v hlavičce žádosti (bugfix githubu #24).
* netty závislostí upgradovat na 4.1.22.Final zajistit JVM vypne po dokončení hlavního vlákna.
* Opravte předejdete předání relace token při čtení hlavní prostředky.
* Přidat další příklady.
* Přidat další testu typovou úlohou scénáře.
* Soubory hlaviček pevné Java pro správné javadoc generování.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK s podporou začátku do konce neblokující vstupně-výstupní operace pomocí [Netty knihovny](http://netty.io/) v režimu brány. 

## <a name="release-and-retirement-dates"></a>Datum vydání a vyřazování z provozu
Microsoft bude poskytovat oznámení alespoň **dobu 12 měsíců** předem vyřazení sady SDK k funkce smooth přechodu na novější nebo podporované verzi.

Nové funkce a funkce a optimalizace, jsou přidány pouze v aktuální sadě SDK, jako takový je doporučujeme, aby vždy upgradu na nejnovější verze sady SDK v míře.

Každá žádost o DB Cosmos pomocí vyřazeno sady SDK budou odmítnuty službou.

<br/>

| Verze | Datum vydání | Datum vyřazení |
| --- | --- | --- |
| [1.0.1](#1.0.1) |20 duben 2018|--- |
| [1.0.0](#1.0.0) |27. února 2018|--- |

## <a name="faq"></a>Nejčastější dotazy
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Další informace najdete v tématech
Další informace o Cosmos DB najdete v tématu [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stránku služby.

