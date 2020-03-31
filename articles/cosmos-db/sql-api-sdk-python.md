---
title: 'Azure Cosmos DB: ROZHRANÍ SQL Python API, sada SDK & prostředky'
description: Přečtěte si vše o rozhraní SQL Python API a SDK, včetně data vydání, data vyřazení a změny provedené mezi jednotlivými verzemi Sady Azure Cosmos DB Python SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 11/29/2018
ms.author: sngun
ms.openlocfilehash: 6bc636b751d12bdb576e54f26536ac0045839229
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70137345"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Python SDK pro ROZHRANÍ SQL API: Poznámky k vydání a prostředky
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Informační kanál o změně .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [Odpočinku](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Poskytovatel prostředků REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Hromadný vykonavatel - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Hromadný vykonavatel - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Stáhnout sadu SDK**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**Dokumentace k rozhraní API**|[Referenční dokumentace rozhraní API pythonu](https://docs.microsoft.com/python/api/azure-cosmos/?view=azure-python)|
|**Pokyny k instalaci sady SDK**|[Pokyny k instalaci sady Python SDK](https://github.com/Azure/azure-cosmos-python)|
|**Přispívat do sady SDK**|[GitHub](https://github.com/Azure/azure-cosmos-python)|
|**Začínáme**|[Začínáme s sadou Python SDK](sql-api-python-application.md)|
|**Aktuální podporovaná platforma**|[Python 2.7](https://www.python.org/downloads/) a [Python 3.5](https://www.python.org/downloads/)|

## <a name="release-notes"></a>Poznámky k verzi

### <a name="302"></a><a name="3.0.2"/>3.0.2
* Přidána podpora datového typu MultiPolygon
* Zásady opakování čtení relace opravě chyby
* Oprava chyb y nesprávného odsazení při dekódování základních řetězců 64

### <a name="301"></a><a name="3.0.1"/>3.0.1
* Oprava chyby v LocationCache
* Logika opakování pokusu o opravu chyby
* Pevná dokumentace

### <a name="300"></a><a name="3.0.0"/>3.0.0
* Podpora zápisů ve více oblastech.
* Obor názvů byl změněn na azure.cosmos.
* Koncepty kolekce a dokumentů přejmenovány na kontejner a položku, document_client přejmenovány na cosmos_client. 

### <a name="233"></a><a name="2.3.3"/>2.3.3
* Přidána podpora pro proxy
* Přidána podpora pro čtení zdrojů změn
* Přidána podpora pro hlavičky kvót kolekce
* Oprava pro problém s tokeny velkých relací
* Oprava rozhraní ReadMedia API
* Oprava v mezipaměti rozsahu klíčů oddílu

### <a name="232"></a><a name="2.3.2"/>2.3.2
* Byla přidána podpora pro výchozí pokusy o připojení.

### <a name="231"></a><a name="2.3.1"/>2.3.1
* Aktualizovaná dokumentace pro odkaz na Azure Cosmos DB namísto Azure DocumentDB.

### <a name="230"></a><a name="2.3.0"/>2.3.0
* Tato verze sady SDK vyžaduje nejnovější verzi emulátoru Azure https://aka.ms/cosmosdb-emulatorCosmos DB, která je k dispozici ke stažení z aplikace .

### <a name="221"></a><a name="2.2.1"/>2.2.1
* Oprava chyb pro agregační slovník.
* Oprava chyby pro oříznutí lomítka v odkazu na prostředek.
* Přidány testy kódování Unicode.

### <a name="220"></a><a name="2.2.0"/>2.2.0
* Přidána podpora pro novou úroveň konzistence s názvem ConsistentPrefix.


### <a name="210"></a><a name="2.1.0"/>2.1.0
* Přidána podpora pro agregační dotazy (COUNT, MIN, MAX, SUMA a AVG).
* Přidána možnost pro zakázání ověření SSL při spuštění proti cosmos DB emulátoru.
* Bylo odstraněno omezení závislé požadavky modul být přesně 2.10.0.
* Snížená minimální propustnost dělených kolekcí z 10 100 RU/s na 2500 RU/s.
* Přidána podpora pro povolení protokolování skriptů během provádění uložené procedury.
* Verze ROZHRANÍ REST API se touto verzí nasákla na "2017-01-19".

### <a name="201"></a><a name="2.0.1"/>2.0.1
* Provedl redakční změny v komentářích k dokumentaci.

### <a name="200"></a><a name="2.0.0"/>2.0.0
* Přidána podpora pro Python 3.5.
* Přidána podpora pro sdružování připojení pomocí modulu požadavků.
* Přidána podpora konzistence relace.
* Přidána podpora pro dotazy TOP/ORDERBY pro rozdělené kolekce.

### <a name="190"></a><a name="1.9.0"/>1.9.0
* Přidána podpora zásad opakování pro omezené požadavky. (Omezené požadavky obdrží příliš velkou výjimku, kód chyby 429.) Ve výchozím nastavení Azure Cosmos DB opakuje devětkrát pro každý požadavek při výskytu kódu chyby 429, ctít retryAfter čas v záhlaví odpovědi. Pevný interval opakování čas lze nyní nastavit jako součást RetryOptions vlastnost na ConnectionPolicy objektu, pokud chcete ignorovat retryAfter čas vrácenservermezi opakování. Azure Cosmos DB teď čeká maximálně 30 sekund pro každý požadavek, který je omezen (bez ohledu na počet opakování) a vrátí odpověď s kódem chyby 429. Tento čas lze také přepsat ve vlastnosti RetryOptions na objektu ConnectionPolicy.
* Cosmos DB nyní vrátí x-ms-throttle-retry-count a x-ms-throttle-retry-wait-time-ms jako hlavičky odezvy v každém požadavku k označení počtu opakování škrticí klapky a kumulativní doby, po kterou požadavek čekal mezi opakovanými pokusy.
* Odebrána Třída RetryPolicy a odpovídající vlastnost (retry_policy) vystavené na document_client třídy a místo toho zavedla RetryOptions třídy vystavení RetryOptions vlastnost na ConnectionPolicy třídy, které lze použít k přepsání některé výchozí možnosti opakování.

### <a name="180"></a><a name="1.8.0"/>1.8.0
* Přidána podpora pro databázové účty s více oblastmi.

### <a name="170"></a><a name="1.7.0"/>1.7.0
* Přidána podpora funkce Time To Live(TTL) pro dokumenty.

### <a name="161"></a><a name="1.6.1"/>1.6.1
* Opravy chyb související s dělením na straně serveru umožňují speciální znaky v cestě klíče oddílu.

### <a name="160"></a><a name="1.6.0"/>1.6.0
* Implementované [rozdělené kolekce](partition-data.md) a [uživatelem definované úrovně výkonu](performance-levels.md). 

### <a name="150"></a><a name="1.5.0"/>1.5.0
* Přidejte překladače oddílů hash & rozsah, které vám pomohou s aplikacemi pro rozdělení napříč více oddíly.

### <a name="142"></a><a name="1.4.2"/>1.4.2
* Implementovat Upsert. Nové metody UpsertXXX přidané pro podporu funkce Upsert.
* Implementujte směrování založené na ID. Žádné změny veřejného rozhraní API, všechny změny interní.

### <a name="120"></a><a name="1.2.0"/>1.2.0
* Podporuje index GeoSpatial.
* Ověří vlastnost id pro všechny prostředky. Id pro prostředky nemůže obsahovat ?, /, #, \, znaky nebo konec mezery.
* Přidá do položky ResourceResponse nové záhlaví "průběh transformace indexu".

### <a name="110"></a><a name="1.1.0"/>1.1.0
* Implementuje zásady indexování V2.

### <a name="101"></a><a name="1.0.1"/>1.0.1
* Podporuje připojení k proxy serveru.

### <a name="100"></a><a name="1.0.0"/>1.0.0
* GA SDK.

## <a name="release--retirement-dates"></a>Uvolnění & data odchodu do důchodu
Společnost Microsoft poskytuje oznámení nejméně **12 měsíců** před vyřazením sady SDK za účelem vyhlazení přechodu na novější/podporovanou verzi.

Nové funkce a funkce a optimalizace jsou přidány pouze do aktuální sady SDK, jako takové je doporučeno vždy upgradovat na nejnovější verzi sady SDK co nejdříve. 

Všechny požadavky na Cosmos DB pomocí vyřazené sady SDK jsou odmítnuty službou.

> [!WARNING]
> **29. února 2016**byly vyřazeny všechny verze sady Python SDK pro rozhraní SQL API před verzí **1.0.0** . 
> 
> 

> [!WARNING]
> Všechny verze 1.x a 2.x pythonu SDK pro ROZHRANÍ SQL API budou **vyřazeny 30.** 
> 
> 

<br/>

| Version | Datum vydání | Datum odchodu do důchodu |
| --- | --- | --- |
| [3.0.2](#3.0.2) |15. listopadu 2018(15. |--- |
| [3.0.1](#3.0.1) |4. října 2018 |--- |
| [2.3.3](#2.3.3) |9. |30. srpna 2020 |
| [2.3.2](#2.3.2) |Květen 08, 2018 |30. srpna 2020 |
| [2.3.1](#2.3.1) |21. prosince 2017December 21, 2017 |30. srpna 2020 |
| [2.3.0](#2.3.0) |10. listopadu 2017November 10, 2017 |30. srpna 2020 |
| [2.2.1](#2.2.1) |Září 29, 2017 |30. srpna 2020 |
| [2.2.0](#2.2.0) |10. května 2017 |30. srpna 2020 |
| [2.1.0](#2.1.0) |Květen 01, 2017 |30. srpna 2020 |
| [2.0.1](#2.0.1) |30. října 2016October 30, 2016 |30. srpna 2020 |
| [2.0.0](#2.0.0) |29. září 2016September 29, 2016 |30. srpna 2020 |
| [1.9.0](#1.9.0) |7. července 2016July 07, 2016 |30. srpna 2020 |
| [1.8.0](#1.8.0) |14. června 2016 |30. srpna 2020 |
| [1.7.0](#1.7.0) |26. dubna 2016 |30. srpna 2020 |
| [1.6.1](#1.6.1) |Duben 08, 2016 |30. srpna 2020 |
| [1.6.0](#1.6.0) |29. března 2016March 29, 2016 |30. srpna 2020 |
| [1.5.0](#1.5.0) |3. ledna 2016 January 03, 2016 |30. srpna 2020 |
| [1.4.2](#1.4.2) |Říjen 06, 2015 |30. srpna 2020 |
| 1.4.1 |Říjen 06, 2015 |30. srpna 2020 |
| [1.2.0](#1.2.0) |Srpen 06, 2015 |30. srpna 2020 |
| [1.1.0](#1.1.0) |09. července 2015 July 09, 2015 |30. srpna 2020 |
| [1.0.1](#1.0.1) |25. května 2015May 25, 2015 |30. srpna 2020 |
| [1.0.0](#1.0.0) |Duben 07, 2015 |30. srpna 2020 |
| 0.9.4-prelease |14. ledna 2015 January 14, 2015 |29. února 2016 February 29, 2016 |
| 0.9.3-prelease |Prosinec 09, 2014 |29. února 2016 February 29, 2016 |
| 0.9.2-prelease |25. listopadu 2014November 25, 2014 |29. února 2016 February 29, 2016 |
| 0.9.1-vydání |23. září 2014September 23, 2014 |29. února 2016 February 29, 2016 |
| 0.9.0-prelease |21. srpna 2014August 21, 2014 |29. února 2016 February 29, 2016 |

## <a name="faq"></a>Nejčastější dotazy
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Viz také
Další informace o Službě Cosmos DB najdete na stránce [služby Microsoft Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/) 

