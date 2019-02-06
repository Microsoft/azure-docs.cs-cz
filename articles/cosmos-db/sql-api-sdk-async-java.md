---
title: 'Azure Cosmos DB: Rozhraní Java API SQL asynchronní, sady SDK a zdroje informací'
description: Další informace o SQL asynchronní Java API a sady SDK, včetně data vydání, vyřazení dat a změny provedené mezi každou verzi sady Azure Cosmos DB SQL asynchronní Java SDK.
author: moderakh
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 2/4/2019
ms.author: moderakh
ms.openlocfilehash: 15bab6af4054085d00ec5c2edb7afb993a620b55
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756253"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB asynchronní Java SDK pro rozhraní SQL API: Zpráva k vydání verze a prostředky
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
> * [SQL](sql-api-query-reference.md)
> * [BulkExecutor – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor – Java](sql-api-sdk-bulk-executor-java.md)

SQL API asynchronní Java SDK se liší od sady Java SDK API SQL tím, že poskytuje asynchronních operací s podporou [Netty knihovny](https://netty.io/). Existující [SQL API Java SDK](sql-api-sdk-java.md) nepodporuje asynchronní operace. 

<table>

<tr><td>**Stažení sady SDK**</td><td>[Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)</td></tr>

<tr><td>**Dokumentace k rozhraní API**</td><td>[Dokumentace k rozhraní Java API](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?view=azure-java-stable)</td></tr>

<tr><td>**Přispívat k sadě SDK**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-java)</td></tr>

<tr><td>**Začínáme**</td><td>[Začínáme se sadou SDK Async Javu](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started)</td></tr>

<tr><td>**Ukázka kódu**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)</td></tr>

<tr><td>**Tipy pro zvýšení výkonu**</td><td>[Soubor Readme pro GitHub](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)</td></tr>

<tr><td>**Minimální podporovaný modul runtime**</td><td>[JDK 8](https://aka.ms/azure-jdks)</td></tr>
</table></br>

## <a name="release-notes"></a>Poznámky k verzi

### <a name="a-name240-beta-1240-beta-1"></a><a name="2.4.0-beta-1"/>2.4.0-beta-1
* Přidání podpory pro Direct.
* Rozhraní API přijímá java.util.Collection, pro kterou pořadí je důležité potvrďte java.util.List změnit.
  Nyní ConnectionPolicy#getPreferredLocations() JsonSerialization a PartitionKey(.) přijímají seznam.
* Je opravená chyba relace pro dotazování dokumentů v režimu brány.
* Upgradovat závislosti (netty 0.4.20 [githubu #79](https://github.com/Azure/azure-cosmosdb-java/issues/79), RxJava 1.3.8).

### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* Zpracování odpovědi na dotazy velmi velké opravy.
* Při vytváření instance klienta opravy zpracování tokenu prostředku ([githubu #78](https://github.com/Azure/azure-cosmosdb-java/issues/78)).
* Upgradovat zranitelné závislost jackson-databind ([githubu #77](https://github.com/Azure/azure-cosmosdb-java/pull/77)).

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Opravili jsme chybu nevrácení prostředků.
* Přidání podpory pro MultiPolygon
* Přidání podpory pro vlastní hlavičky RequestOptions.

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* Je opravená chyba, balení.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Je opravená chyba NPE v cestě k zápisu zkuste to znovu.
* Je opravená chyba NPE v koncového bodu správy.
* Upgradovat zranitelné závislosti ([Githubu #68](https://github.com/Azure/azure-cosmosdb-java/issues/68)).
* Přidání podpory pro protokolování Netty sítě pro řešení potíží.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Přidání podpory pro více oblastí zápisu.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Přidání podpory pro proxy server.
* Přidání podpory pro autorizační token prostředku.
* Oprava chyby v nakládání s klíči velkých oddílů ([63 Githubu](https://github.com/Azure/azure-cosmosdb-java/issues/63)).
* Vylepšili jsme dokumentaci.
* Sada SDK rozdělí do podrobnější moduly.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Oprava chyby u jiných jazycích než angličtině ([51 Githubu](https://github.com/Azure/azure-cosmosdb-java/issues/51)).
* Přidání pomocné metody v konfliktu prostředků.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Nahrazuje org.json závislost jackson kvůli kvůli výkonu a licencování ([29 Githubu](https://github.com/Azure/azure-cosmosdb-java/issues/29)).
* Odebrat zastaralé OfferV2 třídy.
* Přidání přístupové metody pro třídu nabídky pro propustnost obsah.
* V dokumentu nebo prostředek, vrátí typy org.json změněn na návratový typ objektu jackson libovolnou metodu.
* Metoda getObject(.) tříd rozšiřování JsonSerializable změny k vrácení jackson ObjectNode typu.
* Metoda getCollection(.) změněn na vrácení kolekce ObjectNode.
* Odebrané JsonSerializable podtřídy konstruktory s org.json.JSONObject arg.
* JsonSerializable.toJson (SerializationFormattingPolicy.Indented) nyní používá dvě mezery pro odsazení.
  
### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2
* Přidání podpory pro zásady jedinečný Index.
* Přidání podpory pro omezení velikost odpovědi token pokračování v možnostech informačního kanálu.
* Přidání podpory pro rozdělení oddílů v dotazu napříč oddíly.
* Oprava chyby v serializaci Json časové razítko ([32 Githubu](https://github.com/Azure/azure-cosmosdb-java/issues/32)).
* Oprava chyby v serializaci Json výčtu.
* Oprava chyby při správě dokumenty s velikostí 2MB ([Githubu č. 33](https://github.com/Azure/azure-cosmosdb-java/issues/33)).
* Závislost com.fasterxml.jackson.core:jackson-databind upgradovat na 2.9.5 kvůli chybě ([jackson databind: GitHub #1599](https://github.com/FasterXML/jackson-databind/issues/1599))
* Závislost na upgradovat na 0.8.0.17 kvůli chybě rxjava – funkce ([rxjava funkce: GitHub #30](https://github.com/davidmoten/rxjava-extras/issues/30)).
* Popis metadat v souboru pom tier\web inline se zbytkem dokumentaci.
* Zlepšení syntaxe ([Githubu č. 41](https://github.com/Azure/azure-cosmosdb-java/issues/41)), ([Githubu č. 40](https://github.com/Azure/azure-cosmosdb-java/issues/40)).

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Přidání podpory protitlak v dotazu.
* Přidání podpory pro id rozsah klíče oddílu v dotazu.
* Opravu, která umožňuje větší token pro pokračování v hlavičce požadavku (opravu 24 Githubu).
* Upgradovat na 4.1.22.Final zajistit JVM netty závislost ukončí po dokončení hlavního vlákna.
* Opravte a vyhněte se předávání tokenu relace při čtení hlavní prostředky.
* Přidat další příklady.
* Přidat další srovnávací testy scénáře.
* Oprava hlavičkové soubory Java pro generování dokumentu správné java.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Sady SDK verze GA s podporou začátku do konce neblokující pomocí vstupně-výstupních operací [Netty knihovny](https://netty.io/) v režimu brány. 

## <a name="release-and-retirement-dates"></a>Datum vydání verze a vyřazení z provozu
Microsoft bude poskytovat oznámení alespoň **12 měsíců** před vyřazením z provozu sady SDK ulehčení přechodu na verzi novější nebo podporované.

Nové funkce a funkce a optimalizace jsou přidány pouze aktuální sadu SDK. Proto doporučujeme vždy upgradovat na nejnovější verzi sady SDK co nejdříve.

Jakoukoli žádost do služby Cosmos DB pomocí vyřazeno sady SDK budou odmítnuty službou.

<br/>

| Verze | Datum vydání | Datum vyřazení z provozu |
| --- | --- | --- |
| [2.4.0-beta-1](#2.4.0-beta-1) |4. února 2019|--- |
| [2.3.1](#2.3.1) |15. ledna 2019|--- |
| [2.3.0](#2.3.0) |29. listopadu 2018|--- |
| [2.2.2](#2.2.2) |8. listopadu 2018|--- |
| [2.2.1](#2.2.1) |2. listopadu 2018|--- |
| [2.2.0](#2.2.0) |22. září 2018|--- |
| [2.1.0](#2.1.0) |5. září 2018|--- |
| [2.0.1](#2.0.1) |16. srpna 2018|--- |
| [2.0.0](#2.0.0) |20. června 2018|--- |
| [1.0.2](#1.0.2) |18. května 2018|--- |
| [1.0.1](#1.0.1) |20. dubna 2018|--- |
| [1.0.0](#1.0.0) |27. února 2018|--- |

## <a name="faq"></a>Nejčastější dotazy
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Další informace najdete v tématech
Další informace o službě Cosmos DB najdete v tématu [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stránku služby.

