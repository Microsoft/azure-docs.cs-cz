---
title: 'Azure Cosmos DB: SQL Python API, SDK & zdroje | Dokumentace Microsoftu'
description: Další informace o SQL Python API a sady SDK, včetně data vydání, vyřazení dat a změny provedené mezi každou verzi sady Azure Cosmos DB Python SDK.
services: cosmos-db
author: rnagpal
manager: kfile
editor: cgronlun
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 11/29/2018
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5b67f6bbb9ef2a8ee6bd42c89de56a0ff5a3024d
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52724158"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Python SDK pro rozhraní SQL API: poznámky k verzi a prostředky
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

<table>

<tr><td>**Stáhnout sadu SDK**</td><td>[PyPI](https://pypi.org/project/azure-cosmos)</td></tr>

<tr><td>**Dokumentace k rozhraní API**</td><td>[Referenční dokumentace rozhraní Python API](https://docs.microsoft.com/python/api/overview/azure/cosmosdb?view=azure-python)</td></tr>

<tr><td>**Pokyny k instalaci sady SDK**</td><td>[Pokyny k instalaci sady Python SDK](https://github.com/Azure/azure-cosmos-python)</td></tr>

<tr><td>**Přispívat k sadě SDK**</td><td>[GitHub](https://github.com/Azure/azure-cosmos-python)</td></tr>

<tr><td>**Začínáme**</td><td>[Začínáme s využitím Python SDK](sql-api-python-application.md)</td></tr>

<tr><td>**Aktuální podporovanou platformu**</td><td>[Python 2.7](https://www.python.org/downloads/) a [Python 3.5](https://www.python.org/downloads/)</td></tr>
</table></br>

## <a name="release-notes"></a>Poznámky k verzi

### <a name="a-name302302"></a><a name="3.0.2"/>3.0.2
* Přidání podpory pro datový typ MultiPolygon
* Oprava chyby v relaci, přečtěte si zásady opakování
* Oprava chyby u nesprávné odsazení problémů při dekódování výplně řetězce base 64

### <a name="a-name301301"></a><a name="3.0.1"/>3.0.1
* Oprava chyby v LocationCache
* Oprava chyby logiky opakování koncového bodu
* Oprava dokumentace

### <a name="a-name300300"></a><a name="3.0.0"/>3.0.0
* Podpora pro více oblastí zápisu.
* Namespace změní na azure.cosmos.
* Přejmenovat na kontejneru a položku, document_client přejmenován na cosmos_client koncepty kolekce a dokumentu. 

### <a name="a-name233233"></a><a name="2.3.3"/>2.3.3
* Přidání podpory pro proxy server
* Přidání podpory pro čtení kanálu změn
* Přidání podpory pro kolekce hlaviček kvóty
* Opravu pro velké relaci tokeny problém
* Opravu pro ReadMedia rozhraní API
* Opravu v mezipaměti rozsah klíče oddílu

### <a name="a-name232232"></a><a name="2.3.2"/>2.3.2
* Přidání podpory pro výchozí opakovaných pokusů o problémech s připojením.

### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* Aktualizace dokumentace k odkazu na službu Azure Cosmos DB místo Azure DocumentDB.

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Tato verze sady SDK vyžaduje nejnovější verzi Azure emulátor služby Cosmos DB k dispozici ke stažení z https://aka.ms/cosmosdb-emulator.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Oprava chyby pro agregační slovníku.
* Oprava chyby pro oříznutí lomítka v odkazu prostředku.
* Přidání testů pro kódování Unicode.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Volá se přidání podpory pro nové úrovně konzistence ConsistentPrefix.


### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Přidání podpory pro dotazy agregace (počet, MIN, MAX, součet a průměr).
* Přidat možnost pro zakázání ověření SSL při spuštění emulátoru Cosmos DB.
* Odebrat omezení se přesně 2.10.0 závislé požadavky modulu.
* Snížili minimální propustnost na dělené kolekce z 10,100 RU/s na 2 500 RU/s.
* Přidání podpory pro povolení protokolování skriptu při spuštění uložené procedury.
* Verze rozhraní REST API přešla na "2017-01-19' v této vydané verzi.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Redakční změny provedené v komentáře k dokumentaci.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Přidání podpory pro Python 3.5.
* Přidání podpory pro sdružování připojení pomocí modulu požadavky.
* Přidání podpory pro konzistence typu relace.
* Přidání podpory pro TOP/ORDERBY dotazů používejte u dělených kolekcí.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Podpora zásad přidané opakování pro omezené požadavky. (Omezené požadavky přijímat žádost o rychlost příliš velký výjimky, kód chyby 429.) Ve výchozím nastavení služby Azure Cosmos DB zopakuje pokus o devětkrát pro každý požadavek vyskytne kód chyby 429 dodržením retryAfter čas v hlavičce odpovědi. Časový interval opakování pevné můžete nyní nastavit jako součást vlastnost RetryOptions objekt ConnectionPolicy Pokud chcete ignorovat retryAfter čas vrácená serverem mezi opakované pokusy. Azure Cosmos DB teď čeká maximálně 30 sekund pro každý požadavek, který je omezovaná (bez ohledu na počet opakování) a vrátí odpověď s kódem chyby 429. Tentokrát lze také přepsat ve vlastnosti RetryOptions ConnectionPolicy objektu.
* Cosmos DB nyní vrátí x-ms omezení--počet opakování a x-ms-throttle-retry-wait-time-ms hlavičky odpovědi v každé žádosti k označení akcelerátor opakovat počet a kumulativní doba požadavku čekání mezi opakované pokusy.
* Odebrat třídu RetryPolicy a odpovídající vlastnosti (retry_policy) vystavené ve třídě document_client a místo toho zavedená třída RetryOptions vystavuje vlastnost RetryOptions ConnectionPolicy třídy, které můžete použít k přepsání některých výchozí možnosti opakování.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Přidání podpory pro účty databáze ve více oblastech.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Přidání podpory pro čas na Live(TTL) funkce na dokumenty.

### <a name="a-name161161"></a><a name="1.6.1"/>1.6.1
* Opravy chyb související s dělení na straně serveru povoleny speciální znaky v cestě ke klíči oddílů.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Implementovat [dělené kolekce](partition-data.md) a [uživatelsky definovaným výkonem úrovně](performance-levels.md). 

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Přidat hodnotu Hash & Rozsah oddílu překladače, která vám pomůže s horizontálního dělení aplikace napříč několika oddíly.

### <a name="a-name142142"></a><a name="1.4.2"/>1.4.2
* Implementace Upsert. Nové metody UpsertXXX přidaná kvůli podpoře funkcí Upsert.
* Směrování na základě ID implementace. Žádné změny veřejné rozhraní API, všechny změny interní.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Podporuje geoprostorové index.
* Ověří vlastnost id pro všechny prostředky. ID prostředků nemůže obsahovat?, /, #, \, znaky ani končit mezerou.
* Přidá nové záhlaví "index transformace průběh" ResourceResponse.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Implementuje zásady indexování V2.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Podporuje připojení proxy server.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK.

## <a name="release--retirement-dates"></a>Datum vydání verze a vyřazení z provozu
Společnost Microsoft poskytuje oznámení alespoň **12 měsíců** před vyřazením z provozu sady SDK ulehčení přechodu na verzi novější nebo podporované.

Nové funkce a funkce a optimalizace jsou přidány pouze aktuální sadu SDK, proto je doporučujeme vždy upgradovat na nejnovější SDK verzi co možná nejdříve. 

Služba odmítne všechny žádosti do služby Cosmos DB pomocí vyřazeno sady SDK.

> [!WARNING]
> Všechny verze SQL Azure SDK for Python starší než verze **1.0.0** byly dostupné jenom do **29. února 2016**. 
> 
> 

<br/>

| Verze | Datum vydání | Datum vyřazení z provozu |
| --- | --- | --- |
| [3.0.2](#3.0.2) |15. listopadu 2018 |--- |
| [3.0.1](#3.0.1) |04. října 2018 |--- |
| [2.3.3](#2.3.3) |08. září 2018 |--- |
| [2.3.2](#2.3.2) |08. května 2018 |--- |
| [2.3.1](#2.3.1) |21. prosince 2017 |--- |
| [2.3.0](#2.3.0) |10. listopadu 2017 |--- |
| [2.2.1](#2.2.1) |29. září 2017 |--- |
| [2.2.0](#2.2.0) |10. května 2017 |--- |
| [2.1.0](#2.1.0) |01. května 2017 |--- |
| [2.0.1](#2.0.1) |30. října 2016 |--- |
| [2.0.0](#2.0.0) |29. září 2016 |--- |
| [1.9.0](#1.9.0) |07. července 2016 |--- |
| [1.8.0](#1.8.0) |14. června 2016 |--- |
| [1.7.0](#1.7.0) |26. dubna 2016 |--- |
| [1.6.1](#1.6.1) |08. dubna 2016 |--- |
| [1.6.0](#1.6.0) |29. března 2016 |--- |
| [1.5.0](#1.5.0) |03. ledna 2016 |--- |
| [1.4.2](#1.4.2) |06. října 2015 |--- |
| [1.4.1](#1.4.1) |06. října 2015 |--- |
| [1.2.0](#1.2.0) |06. srpna 2015 |--- |
| [1.1.0](#1.1.0) |09. července 2015 |--- |
| [1.0.1](#1.0.1) |25. května 2015 |--- |
| [1.0.0](#1.0.0) |07. dubna 2015 |--- |
| 0.9.4-prelease |14. ledna 2015 |29. února 2016 |
| 0.9.3-prelease |09 prosinec 2014 |29. února 2016 |
| 0.9.2-prelease |K 25. listopadu 2014 |29. února 2016 |
| 0.9.1-prelease |23. září 2014 |29. února 2016 |
| 0.9.0-prelease |21. srpna 2014 |29. února 2016 |

## <a name="faq"></a>Nejčastější dotazy
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Další informace najdete v tématech
Další informace o službě Cosmos DB najdete v tématu [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stránku služby. 

