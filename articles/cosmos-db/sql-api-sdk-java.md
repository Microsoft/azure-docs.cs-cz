---
title: 'Azure Cosmos DB: Rozhraní API Java SQL, sadu SDK a prostředky | Microsoft Docs'
description: Další informace o rozhraní API Java SQL a sady SDK, včetně data vydání, vyřazení dat a změny provedené mezi každou verzi sady Azure Cosmos DB SQL Java SDK.
services: cosmos-db
documentationcenter: java
author: rnagpal
manager: kfile
editor: cgronlun
ms.assetid: 7861cadf-2a05-471a-9925-0fec0599351b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 05/16/2018
ms.author: khdang
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 62941b3654204533d982b11f56b6ca08d591a72f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK pro rozhraní API pro SQL: poznámky k verzi a prostředky
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

Java SDK pro rozhraní API SQL podporuje synchronní operace. Asynchronní podpora použít [SQL API asynchronní Java SDK](sql-api-sdk-async-java.md). 

<table>

<tr><td>**Stažení sady SDK**</td><td>[Maven](http://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)</td></tr>

<tr><td>**Dokumentaci k rozhraní API**</td><td>[Referenční dokumentace rozhraní API Java](/java/api/com.microsoft.azure.documentdb)</td></tr>

<tr><td>**Můžete přispět k sadě SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr>

<tr><td>**Začínáme**</td><td>[Začínáme s sady Java SDK](sql-api-java-get-started.md)</td></tr>

<tr><td>**Kurz vývoje webové aplikace**</td><td>[Vývoj webových aplikací s Azure Cosmos DB](sql-api-java-application.md)</td></tr>

<tr><td>**Minimální podporovaný modul runtime**</td><td>[JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr>
</table></br>

## <a name="release-notes"></a>Poznámky k verzi

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Přidaná podpora pro jedinečný Index.
* Přidaná podpora pro omezení velikosti token pokračování v možnostech informačního kanálu.
* Pevná chyby ve serializace Json (časové razítko).
* Pevná chyby ve serializace Json (výčtu).
* Závislost na com.fasterxml.jackson.core:jackson-databind upgradovat na 2.9.5.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
* Vylepšené sdružování pro přímý režim.
* Vylepšené předběžné zlepšení pro jiný orderby křížové dotaz oddílu.
* Vylepšené UUID generace.
* Vylepšená logika konzistence relace.
* Přidaná podpora pro multipolygon.
* Přidaná podpora pro rozsah statistiky klíč oddílu pro kolekci.
* Pevná chyby ve více oblasti podpory.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
* Lepší výkon serializace Json.
* Tato verze sady SDK vyžaduje nejnovější verze emulátoru DB Cosmos Azure k dispozici ke stažení https://aka.ms/cosmosdb-emulator.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
* Interní změny pro knihovny Microsoft přátel.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Byl opraven problém při čtení jednoho oddílu rozsahy klíčů.
* Byl opraven problém v ResourceID analýzy, která ovlivňuje databáze používají krátké názvy.
* Kódování klíče oddílu způsobit problém vyřešili.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Důležité opravy chyb pro zpracování během rozdělení oddílů žádosti.
* Oprava problému s úrovní konzistence silné a BoundedStaleness.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Přidaná podpora pro novou úroveň konzistence volá ConsistentPrefix.
* Opravit chyby při čtení kolekce v režimu relace.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Povolená podpora dělenou kolekci s jako málo jako odpovídající 2500 RU za sekundu a škálování v přírůstcích po 100 RU za sekundu.
* Chyby opraveny v nativní sestavení, které může způsobit výjimku NullRef v některé dotazy.

### <a name="a-name196196"></a><a name="1.9.6"/>1.9.6
* Vyřešený chyby v konfiguraci modulu dotazu, který může způsobit, že výjimky pro dotazy v režimu brány.
* Vyřešili několik chyb v kontejneru relace, který může způsobit výjimku "Vlastník prostředek nebyl nalezen" pro požadavky okamžitě po vytvoření kolekce.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Přidaná podpora pro dotazy agregace (COUNT, MIN, MAX, součet a průměr). V tématu [podporu agregace](sql-api-sql-query.md#Aggregates).
* Přidaná podpora pro změnu informačního kanálu.
* Byla přidána podpora pro informace o kvótě kolekce prostřednictvím RequestOptions.setPopulateQuotaInfo.
* Byla přidána podpora pro uloženou proceduru skriptu protokolování prostřednictvím RequestOptions.setScriptLoggingEnabled.
* Opravit chyby, kde dotazu v režimu DirectHttps může přestat reagovat při zjištění chyby omezení.
* Vyřešený chyby v režimu konzistence relace.
* Opravit chyby, které můžou způsobit výjimky NullReferenceException v HttpContext, když je vysoká rychlost požadavků.
* Vylepšený výkon DirectHttps režimu.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Podpora přidání jednoduchý klient založený na instancích proxy s rozhraním API ConnectionPolicy.setProxy().
* Přidání rozhraní API DocumentClient.close() správně instance DocumentClient vypnutí.
* Zvýšení výkonu dotazů v režimu přímého připojení odvozením plán dotazu z nativní sestavení místo brány.
* Nastavit FAIL_ON_UNKNOWN_PROPERTIES = false, takže uživatelé nepotřebují k definování JsonIgnoreProperties v jejich POJO.
* Rozdělili protokolování použití SLF4J.
* Vyřešili několik dalších chyb ve čtecím modulu konzistence.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Pevné chyby ve správě připojení k prevence úniků připojení v režimu přímého připojení.
* Vyřešený chyby v dotazu TOP, kde se může vyvolat NullReferenece výjimka.
* Lepší výkon snížením počtu volání sítě pro vnitřní mezipaměti.
* Přidání stavový kód, aktivity a URI požadavku v DocumentClientException pro lepší řešení potíží.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Ve správě připojení pro stabilitu byl opraven problém.

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1
* Přidaná podpora pro úrovně konzistence BoundedStaleness.
* Byla přidána podpora pro přímé připojení k síti pro operace CRUD pro dělené kolekce.
* Opravit chyby v dotazování databáze s SQL.
* Chyby opraveny v mezipaměti relace, kde token relace může být nastaveny nesprávně.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Přidaná podpora pro křížové paralelní dotazy oddílu.
* Byla přidána podpora pro horní nebo ORDER BY a dotazy pro dělené kolekce.
* Přidaná podpora pro silnou konzistenci.
* Byla přidána podpora pro název na základě požadavky při použití přímé připojení.
* Pevné aby zůstat konzistentní napříč všechny žádosti o opakování aktivity.
* Opravit chyby související s mezipaměti relace při opětovném vytváření kolekce se stejným názvem.
* Přidání mnohoúhelníku a datové typy LineString při zadávání kolekce indexování zásady pro geografického vymezení prostorových dotazů.
* Opravené problémy s Java dokumentace pro jazyk Java 1.8.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1
* Pevná chyby ve PartitionKeyDefinitionMap ukládat do mezipaměti kolekce tvořené jedním oddílem a zajistěte, aby nebyly navíc načtení oddílu o klíč.
* Opravit chyby není opakovat, je-li hodnotu klíče oddílu nesprávné.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Přidaná podpora pro účty databáze více oblast.
* Přidaná podpora pro automatické opakování u omezenému požadavků s možností přizpůsobení maximální počet opakovaných pokusů a doba čekání na maximální počet opakování.  Zobrazit RetryOptions a ConnectionPolicy.getRetryOptions().
* Nepoužívané IPartitionResolver na základě vlastního rozdělení kódu. Použijte dělené kolekce pro vyšší propustnost a úložiště.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Podpora zásad přidané opakování omezení.  

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Další čas live (TTL) podpory pro dokumenty.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Implementovat [oddíly kolekce](partition-data.md) a [úrovně výkonu uživatelem definované](performance-levels.md).

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* Pevná chyby ve HashPartitionResolver ke generování hodnoty hash v little endian, aby byla konzistentní se dalších sadách SDK.

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Přidejte hodnotu Hash p & ro oddílu překladače pomoct s aplikacemi horizontálního dělení napříč více oddílů.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Implementujte Upsert. Přidaná kvůli podpoře funkcí Upsert nové metody upsertXXX.
* Implementujte, na základě ID směrování. Žádné změny veřejné rozhraní API, všechny změny interní.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Verze přeskočen mají být předány číslo verze zarovnání s dalších sadách SDK

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Podporuje geoprostorové indexu
* Ověří vlastnost id pro všechny prostředky. Identifikátory prostředků nesmí obsahovat?, /, #, \, znaků nebo končit mezerou.
* Přidá nové záhlaví "index transformace průběh" ResourceResponse.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Implementuje zásady indexování V2

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK

## <a name="release-and-retirement-dates"></a>Datum vydání a vyřazování z provozu
Microsoft bude poskytovat oznámení alespoň **dobu 12 měsíců** předem vyřazení sady SDK k funkce smooth přechodu na novější nebo podporované verzi.

Nové funkce a funkce a optimalizace, jsou přidány pouze v aktuální sadě SDK, jako takový je doporučujeme, aby vždy upgradu na nejnovější verze sady SDK v míře.

Každá žádost o DB Cosmos pomocí vyřazeno sady SDK budou odmítnuty službou.

> [!WARNING]
> Všechny verze sady SDK SQL pro jazyk Java starší než verze **1.0.0** bylo vyřazeno na **29. února 2016**.
> 
> 

<br/>

| Verze | Datum vydání | Datum vyřazení |
| --- | --- | --- |
| [1.16.1](#1.16.1) |16 může 2018 |--- |
| [1.16.0](#1.16.0) |15. března 2018 |--- |
| [1.15.0](#1.15.0) |14 listopadu 2017 |--- |
| [1.14.0](#1.14.0) |28 OCT 2017 |--- |
| [1.13.0](#1.13.0) |25 srpen 2017 |--- |
| [1.12.0](#1.12.0) |11 července 2017 |--- |
| [1.11.0](#1.11.0) |10. května 2017 |--- |
| [1.10.0](#1.10.0) |11 března 2017 |--- |
| [1.9.6](#1.9.6) |21. února 2017 |--- |
| [1.9.5](#1.9.5) |31. ledna 2017 |--- |
| [1.9.4](#1.9.4) |24 od listopadu 2016 |--- |
| [1.9.3](#1.9.3) |30. října 2016 |--- |
| [1.9.2](#1.9.2) |28. října 2016 |--- |
| [1.9.1](#1.9.1) |26 října 2016 |--- |
| [1.9.0](#1.9.0) |03. října 2016 |--- |
| [1.8.1](#1.8.1) |30. června 2016 |--- |
| [1.8.0](#1.8.0) |14. června 2016 |--- |
| [1.7.1](#1.7.1) |30. dubna 2016 |--- |
| [1.7.0](#1.7.0) |27. dubna 2016 |--- |
| [1.6.0](#1.6.0) |29. března 2016 |--- |
| [1.5.1](#1.5.1) |31. prosince 2015 |--- |
| [1.5.0](#1.5.0) |04 prosince 2015 |--- |
| [1.4.0](#1.4.0) |05 říjen 2015 |--- |
| [1.3.0](#1.3.0) |05 říjen 2015 |--- |
| [1.2.0](#1.2.0) |05 srpen 2015 |--- |
| [1.1.0](#1.1.0) |09 července 2015 |--- |
| [1.0.1](#1.0.1) |12 květen 2015 |--- |
| [1.0.0](#1.0.0) |07 duben 2015 |--- |
| 0.9.5-prelease |09 března 2015 |29. února 2016 |
| 0.9.4-prelease |17 únor 2015 |29. února 2016 |
| 0.9.3-prelease |13. ledna 2015 |29. února 2016 |
| 0.9.2-prelease |19 prosince 2014 |29. února 2016 |
| 0.9.1-prelease |19 prosince 2014 |29. února 2016 |
| 0.9.0-prelease |10. prosince 2014 |29. února 2016 |

## <a name="faq"></a>Nejčastější dotazy
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Další informace najdete v tématech
Další informace o Cosmos DB najdete v tématu [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stránku služby.

