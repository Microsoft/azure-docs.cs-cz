---
title: 'Azure Cosmos DB: SQL Async Java API, sada SDK & prostředky'
description: Další informace o SQL asynchronní Java API a sady SDK, včetně data vydání, vyřazení dat a změny provedené mezi každou verzi sady Azure Cosmos DB SQL asynchronní Java SDK.
author: moderakh
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 07/01/2019
ms.author: moderakh
ms.openlocfilehash: 156699b8d8c1a645961f4e919bdd843d995a3d18
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142656"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Async Java SDK pro SQL API: Poznámky k verzi a zdroje informací
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
> * [Hromadný prováděcí modul – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Hromadný prováděcí modul – Java](sql-api-sdk-bulk-executor-java.md)

SQL API asynchronní Java SDK se liší od sady Java SDK API SQL tím, že poskytuje asynchronních operací s podporou [Netty knihovny](https://netty.io/). Existující [SQL API Java SDK](sql-api-sdk-java.md) nepodporuje asynchronní operace. 

| |  |
|---|---|
| **Stažení sady SDK** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**Dokumentace k rozhraní API** |[Dokumentace k rozhraní Java API](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?view=azure-java-stable) | 
|**Přispívat k sadě SDK** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**Začínáme** | [Začínáme se sadou SDK Async Javu](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**Ukázka kódu** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **Tipy pro zvýšení výkonu**| [Soubor Readme GitHubu](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **Minimální podporovaný modul runtime**|[JDK 8](https://aka.ms/azure-jdks) | 

## <a name="release-notes"></a>Poznámky k verzi

### <a name="a-name250250"></a><a name="2.5.0"/>2.5.0
* Režim protokolu TCP je teď ve výchozím nastavení zapnutý.
* Metriky dotazů v různých oddílech teď vrátí všechny oddíly.
* Globální Strong teď funguje správně.
* Převzetí služeb při selhání u dotazů, u kterých není správně pokusy o více hlavních serverů
* Nárazníky závislosti pro opravy hotfix zabezpečení

### <a name="a-name245245"></a><a name="2.4.5"/>2.4.5
* Podpora opravu chyb pro hash v2

### <a name="a-name243243"></a><a name="2.4.3"/>2.4.3
* Opravu chyb nevracení prostředků v klientovi # Close () ([github #88](https://github.com/Azure/azure-cosmosdb-java/issues/88)).

### <a name="a-name242242"></a><a name="2.4.2"/>2.4.2
* Byla přidána podpora tokenů pokračování pro dotazy mezi oddíly.

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1
* Opravili jsme některé chyby v přímém režimu.
* Vylepšené protokolování v přímém režimu.
* Vylepšená správa připojení.

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0
* Připojení k přímému režimu je teď všeobecně dostupné (GA). Ukázku, která využívá připojení k přímému režimu, najdete v tématu úložiště GitHub [Azure-cosmosdb-Java](https://github.com/Azure/azure-cosmosdb-java) .
* Byla přidána podpora pro QueryMetrics.
* Změnila se rozhraní API, která přijímají jazyk Java. util. Collection, pro který je důležité přijmout místo toho příkaz Java. util. list. Nyní ConnectionPolicy seznam přijmout # getPreferredLocations (), JsonSerialization a PartitionKey (.).

### <a name="a-name240-beta-1240-beta-1"></a><a name="2.4.0-beta-1"/>2.4.0-beta-1
* Přidání podpory pro přímý režim připojení
* Změnila se rozhraní API, která přijímají jazyk Java. util. Collection, pro který je důležité přijmout místo toho příkaz Java. util. list.
  Nyní ConnectionPolicy seznam přijmout # getPreferredLocations (), JsonSerialization a PartitionKey (.).
* Opravili jsme chybu relace pro dotaz na dokument v režimu brány.
* Upgradované závislosti (0.4.20 [#79 GitHubu](https://github.com/Azure/azure-cosmosdb-java/issues/79), RxJava 1.3.8).

### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* Opravuje zpracování velmi rozsáhlých odpovědí na dotazy.
* Opravuje zpracování tokenů prostředků při vytváření instance klienta ([#78 GitHub](https://github.com/Azure/azure-cosmosdb-java/issues/78)).
* Upgradované diagramy zranitelné závislosti – DataBind ([#77 GitHubu](https://github.com/Azure/azure-cosmosdb-java/pull/77)).

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Opravili jsme chybu nevrácení prostředků.
* Přidání podpory pro MultiPolygon
* Přidání podpory pro vlastní hlavičky RequestOptions.

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* Je opravená chyba, balení.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Je opravená chyba NPE v cestě k zápisu zkuste to znovu.
* Je opravená chyba NPE v koncového bodu správy.
* Upgradované ohrožené závislosti ([#68 GitHubu](https://github.com/Azure/azure-cosmosdb-java/issues/68)).
* Přidání podpory pro protokolování Netty sítě pro řešení potíží.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Přidání podpory pro více oblastí zápisu.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Přidání podpory pro proxy server.
* Přidání podpory pro autorizační token prostředku.
* Opravili jsme chybu při zpracování klíčů s velkými oddíly ([#63 GitHubu](https://github.com/Azure/azure-cosmosdb-java/issues/63)).
* Vylepšili jsme dokumentaci.
* Sada SDK rozdělí do podrobnější moduly.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Opravili jsme chybu pro jiné než anglické národní prostředí ([GitHub #51](https://github.com/Azure/azure-cosmosdb-java/issues/51)).
* Přidání pomocné metody v konfliktu prostředků.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Vyměněna závislost org. JSON z důvodu výkonu a licencování ([#29 GitHubu](https://github.com/Azure/azure-cosmosdb-java/issues/29)).
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
* Opravila se chyba serializace časového razítka JSON ([#32 GitHubu](https://github.com/Azure/azure-cosmosdb-java/issues/32)).
* Oprava chyby v serializaci Json výčtu.
* Opravili jsme chybu při správě dokumentů o velikosti 2 MB ([#33 GitHub](https://github.com/Azure/azure-cosmosdb-java/issues/33)).
* Dependency com. fasterxml. jacksonůves. Core: Jacksonův diagram – provedení upgradu na 2.9.5 z důvodu chyby[(Jacksonův-DataBind: #1599](https://github.com/FasterXML/jackson-databind/issues/1599)GitHubu)
* Závislost na rxjava-Extras upgradována na 0.8.0.17 z důvodu chyby ([rxjava-Extras: #30](https://github.com/davidmoten/rxjava-extras/issues/30)GitHubu).
* Popis metadat v souboru pom tier\web inline se zbytkem dokumentaci.
* Vylepšení syntaxe ([#41 GitHubu](https://github.com/Azure/azure-cosmosdb-java/issues/41)) (GitHub[#40](https://github.com/Azure/azure-cosmosdb-java/issues/40)).

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Přidání podpory protitlak v dotazu.
* Přidání podpory pro id rozsah klíče oddílu v dotazu.
* Oprava pro povolení většího pokračovacího tokenu v hlavičce požadavku (opravu chyb GitHub #24).
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

> [!WARNING]
> **30. srpna 2020**se vyřadí všechny verze **1. x** asynchronní sady Java SDK pro SQL API.
> 
>
<br/>

| Version | Datum vydání | Datum vyřazení z provozu |
| --- | --- | --- |
| [2.4.3](#2.4.3) |5\. března 2019|--- |
| [2.4.2](#2.4.2) |1\. březen 2019|--- |
| [2.4.1](#2.4.1) |20. února 2019|--- |
| [2.4.0](#2.4.0) |8\. února 2019|--- |
| [2.4.0-beta-1](#2.4.0-beta-1) |4\. února 2019|--- |
| [2.3.1](#2.3.1) |15. ledna 2019|--- |
| [2.3.0](#2.3.0) |29. listopadu 2018|--- |
| [2.2.2](#2.2.2) |8\. listopadu 2018|--- |
| [2.2.1](#2.2.1) |2\. listopadu 2018|--- |
| [2.2.0](#2.2.0) |22. září 2018|--- |
| [2.1.0](#2.1.0) |5\. září 2018|--- |
| [2.0.1](#2.0.1) |16. srpna 2018|--- |
| [2.0.0](#2.0.0) |20. června 2018|--- |
| [1.0.2](#1.0.2) |18. května 2018|30. srpna 2020 |
| [1.0.1](#1.0.1) |20. dubna 2018|30. srpna 2020 |
| [1.0.0](#1.0.0) |27. února 2018|30. srpna 2020 |

## <a name="faq"></a>Nejčastější dotazy
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Další informace najdete v tématech
Další informace o službě Cosmos DB najdete v tématu [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stránku služby.

