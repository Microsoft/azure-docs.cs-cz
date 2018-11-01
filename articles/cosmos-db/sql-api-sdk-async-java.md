---
title: 'Azure Cosmos DB: SQL asynchronní Java API, SDK & zdroje | Dokumentace Microsoftu'
description: Další informace o SQL asynchronní Java API a sady SDK, včetně data vydání, vyřazení dat a změny provedené mezi každou verzi sady Azure Cosmos DB SQL asynchronní Java SDK.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 09/05/2018
ms.author: sngun
ms.openlocfilehash: 57172927011532af9b14fa7ff09d6aad9bac1e92
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50417595"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB asynchronní Java SDK pro rozhraní SQL API: poznámky k verzi a prostředky
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

SQL API asynchronní Java SDK se liší od sady Java SDK API SQL tím, že poskytuje asynchronních operací s podporou [Netty knihovny](http://netty.io/). Existující [SQL API Java SDK](sql-api-sdk-java.md) nepodporuje asynchronní operace. 

<table>

<tr><td>**Stažení sady SDK**</td><td>[Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)</td></tr>

<tr><td>**Dokumentace k rozhraní API**</td><td>[Dokumentace k rozhraní Java API](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client?view=azure-java-stable)</td></tr>

<tr><td>**Přispívat k sadě SDK**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-java)</td></tr>

<tr><td>**Začínáme**</td><td>[Začínáme se sadou SDK Async Javu](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started)</td></tr>

<tr><td>**Ukázka kódu**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)</td></tr>

<tr><td>**Tipy pro zvýšení výkonu**</td><td>[Soubor Readme pro Github](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)</td></tr>

<tr><td>**Minimální podporovaný modul runtime**</td><td>[JDK 8](https://aka.ms/azure-jdks)</td></tr>
</table></br>

## <a name="release-notes"></a>Poznámky k verzi

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Přidání podpory pro proxy server.
* Přidání podpory pro autorizační token prostředku.
* Oprava chyby v nakládání s klíči velkých oddílů ([githubu #63](https://github.com/Azure/azure-cosmosdb-java/issues/63)).
* Vylepšili jsme dokumentaci.
* Sada SDK rozdělí do podrobnější moduly.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Oprava chyby u jiných jazycích než angličtině ([githubu #51](https://github.com/Azure/azure-cosmosdb-java/issues/51)).
* Přidání pomocné metody v konfliktu prostředků.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Nahrazuje org.json závislost jackson kvůli kvůli výkonu a licencování ([githubu #29](https://github.com/Azure/azure-cosmosdb-java/issues/29)).
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
* Oprava chyby v serializaci Json časové razítko ([githubu #32](https://github.com/Azure/azure-cosmosdb-java/issues/32)).
* Oprava chyby v serializaci Json výčtu.
* Oprava chyby při správě dokumenty s velikostí 2MB ([githubu #33](https://github.com/Azure/azure-cosmosdb-java/issues/33)).
* Závislost com.fasterxml.jackson.core:jackson-databind upgradovat na 2.9.5 kvůli chybě ([jackson databind: github #1599](https://github.com/FasterXML/jackson-databind/issues/1599))
* Závislost na upgradovat na 0.8.0.17 kvůli chybě rxjava – funkce ([rxjava extra: github #30](https://github.com/davidmoten/rxjava-extras/issues/30)).
* Popis metadat v souboru pom tier\web inline se zbytkem dokumentaci.
* Zlepšení syntaxe ([githubu #41](https://github.com/Azure/azure-cosmosdb-java/issues/41)), ([githubu #40](https://github.com/Azure/azure-cosmosdb-java/issues/40)).

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Přidání podpory protitlak v dotazu.
* Přidání podpory pro id rozsah klíče oddílu v dotazu.
* Opravu, která umožňuje větší token pro pokračování v hlavičce požadavku (github opravu #24).
* Upgradovat na 4.1.22.Final zajistit JVM netty závislost ukončí po dokončení hlavního vlákna.
* Opravte a vyhněte se předávání tokenu relace při čtení hlavní prostředky.
* Přidat další příklady.
* Přidat další srovnávací testy scénáře.
* Oprava hlavičkové soubory Java pro generování dokumentu správné java.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Sady SDK verze GA s podporou začátku do konce neblokující pomocí vstupně-výstupních operací [Netty knihovny](http://netty.io/) v režimu brány. 

## <a name="release-and-retirement-dates"></a>Datum vydání verze a vyřazení z provozu
Microsoft bude poskytovat oznámení alespoň **12 měsíců** před vyřazením z provozu sady SDK ulehčení přechodu na verzi novější nebo podporované.

Nové funkce a funkce a optimalizace jsou přidány pouze aktuální sadu SDK. Proto doporučujeme vždy upgradovat na nejnovější verzi sady SDK co nejdříve.

Jakoukoli žádost do služby Cosmos DB pomocí vyřazeno sady SDK budou odmítnuty službou.

<br/>

| Verze | Datum vydání | Datum vyřazení z provozu |
| --- | --- | --- |
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

