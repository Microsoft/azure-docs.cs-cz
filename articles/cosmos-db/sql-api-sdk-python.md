---
title: 'Azure Cosmos DB: SQL Python API, sada SDK & prostředky'
description: Seznamte se se všemi informacemi o rozhraních API SQL Pythonu a sadě SDK, včetně dat vydání, data odchodu a změn provedených mezi jednotlivými verzemi Azure Cosmos DB Python SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 11/29/2018
ms.author: sngun
ms.openlocfilehash: ed90c22fa8c5b94567a9886ca71c9b35fbb103f0
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624625"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Python SDK pro SQL API: Poznámky k verzi a zdroje informací
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

| |  |
|---|---|
|**Stáhnout sadu SDK**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**Dokumentace k rozhraní API**|[Referenční dokumentace k rozhraní Python API](https://docs.microsoft.com/python/api/azure-cosmos/?view=azure-python)|
|**Pokyny k instalaci sady SDK**|[Pokyny k instalaci sady Python SDK](https://github.com/Azure/azure-cosmos-python)|
|**Přispívat k sadě SDK**|[GitHub](https://github.com/Azure/azure-cosmos-python)|
|**Začínáme**|[Začínáme se sadou Python SDK](sql-api-python-application.md)|
|**Aktuální podporovaná platforma**|[Python 2,7](https://www.python.org/downloads/) a [Python 3,5](https://www.python.org/downloads/)|

## <a name="release-notes"></a>Poznámky k verzi

### <a name="a-name302302"></a><a name="3.0.2"/>3.0.2
* Přidání podpory pro datový typ s více mnohoúhelníky
* Oprava chyby v zásadách opakování při čtení relace
* Oprava chyb pro nesprávné problémy s odsazením při dekódování základních řetězců 64

### <a name="a-name301301"></a><a name="3.0.1"/>3.0.1
* Oprava chyb v LocationCache
* Oprava chyby koncovým bodem – logika opakování
* Pevná dokumentace

### <a name="a-name300300"></a><a name="3.0.0"/>3.0.0
* Podpora zápisů ve více oblastech.
* Obor názvů se změnil na Azure. Cosmos.
* Koncepty shromažďování a dokumentů byly přejmenovány na kontejner a položku, document_client byly přejmenovány na cosmos_client. 

### <a name="a-name233233"></a><a name="2.3.3"/>2.3.3
* Přidání podpory pro proxy server
* Přidání podpory pro čtení kanálu změn
* Přidání podpory pro záhlaví kvóty kolekce
* Opravu chyb pro problémy s tokeny velkých relací
* Opravu chyb pro rozhraní API pro ReadMedia
* Opravu chyb v mezipaměti rozsahu klíčů oddílu

### <a name="a-name232232"></a><a name="2.3.2"/>2.3.2
* Přidání podpory pro výchozí opakování při potížích s připojením

### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* Aktualizovaná dokumentace, aby odkazovala Azure Cosmos DB místo Azure DocumentDB.

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Tato verze sady SDK vyžaduje nejnovější verzi Azure emulátor služby Cosmos DB k dispozici ke stažení z https://aka.ms/cosmosdb-emulator.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Oprava chyby pro agregovaný slovník
* Oprava chyby pro ořezávání lomítek v odkazu na prostředek
* Byly přidány testy pro kódování Unicode.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Volá se přidání podpory pro nové úrovně konzistence ConsistentPrefix.


### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Přidání podpory pro dotazy agregace (počet, MIN, MAX, součet a průměr).
* Přidání možnosti pro zákaz ověřování SSL při spuštění v emulátoru Cosmos DB.
* Odebralo se omezení modulu závislých požadavků, aby byl přesně 2.10.0.
* Snížili minimální propustnost na dělené kolekce z 10,100 RU/s na 2 500 RU/s.
* Přidání podpory pro povolení protokolování skriptu během provádění uložených procedur.
* V této verzi je REST APIá verze předaná na 2017-01-19.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Byly provedeny redakční změny dokumentačních komentářů.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Byla přidána podpora Pythonu 3,5.
* Přidala se podpora sdružování připojení pomocí modulu požadavků.
* Byla přidána podpora konzistence relace.
* Přidání podpory pro dotazy TOP/ORDERBY pro dělené kolekce.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Přidala se podpora zásad opakování pro omezené požadavky. (Omezení požadavků obdrží příliš velkou výjimku, kód chyby 429.) Ve výchozím nastavení se Azure Cosmos DB opakuje devět časů pro každý požadavek, pokud se zjistil kód chyby 429, který respektuje čas retryAfter v hlavičce odpovědi. Pevný čas intervalu opakování se teď dá nastavit jako součást vlastnosti RetryOptions objektu ConnectionPolicy, pokud chcete ignorovat dobu retryAfter vrácenou serverem mezi opakovanými pokusy. Azure Cosmos DB nyní čeká maximálně 30 sekund pro každý požadavek, který je omezený (bez ohledu na počet opakování), a vrátí odpověď s kódem chyby 429. Tuto dobu lze také přepsat ve vlastnosti RetryOptions objektu ConnectionPolicy.
* Cosmos DB nyní vrací hodnoty x-MS-Throttle-Return-Count a x-MS-Throttle-Retry-Time-MS jako hlavičky odpovědi v každé žádosti, aby se poznamenalo, že počet opakování omezení a kumulativní doba, jakou požadavek mezi opakovanými pokusy čekal.
* Odebrali jsme třídu RetryPolicy a odpovídající vlastnost (retry_policy) zveřejněnou ve třídě document_client a místo toho jste zavedli RetryOptions třídu, která vystavuje vlastnost RetryOptions třídy ConnectionPolicy, kterou lze použít k přepsání některých z výchozí možnosti opakování

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Přidání podpory pro účty databáze ve více oblastech.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Přidali jsme podporu funkce Time to Live (TTL) pro dokumenty.

### <a name="a-name161161"></a><a name="1.6.1"/>1.6.1
* Opravy chyb související s vytvářením oddílů na straně serveru pro povolení speciálních znaků v cestě klíče oddílu.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Implementovat [dělené kolekce](partition-data.md) a [uživatelsky definovaným výkonem úrovně](performance-levels.md). 

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Přidat hodnotu Hash & Rozsah oddílu překladače, která vám pomůže s horizontálního dělení aplikace napříč několika oddíly.

### <a name="a-name142142"></a><a name="1.4.2"/>1.4.2
* Implementace Upsert. Přidaly se nové metody UpsertXXX, které podporují funkci Upsert.
* Směrování na základě ID implementace. Žádné změny veřejné rozhraní API, všechny změny interní.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Podporuje geoprostorové indexy.
* Ověří vlastnost id pro všechny prostředky. ID prostředků nemůže obsahovat?, /, #, \, znaky ani končit mezerou.
* Přidá nové záhlaví "index transformace průběh" ResourceResponse.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Implementuje zásadu indexování v2.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Podporuje připojení k proxy serveru.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK.

## <a name="release--retirement-dates"></a>Data vyřazení & vydání
Microsoft poskytuje oznámení alespoň **12 měsíců** před vyřazením sady SDK z důvodu hladkého přechodu na novější/podporovanou verzi.

Nové funkce a funkce a optimalizace se přidávají jenom do aktuální sady SDK, protože je to proto, že doporučujeme vždy co nejdříve upgradovat na nejnovější verzi sady SDK. 

Všechny žádosti o Cosmos DB používání vyřazené sady SDK jsou službou odmítnuty.

> [!WARNING]
> Všechny verze sady Azure SQL SDK pro Python před verzí **1.0.0** byly vyřazení od **29. února 2016**. 
> 
> 

<br/>

| Version | Datum vydání | Datum vyřazení z provozu |
| --- | --- | --- |
| [3.0.2](#3.0.2) |15. listopadu 2018 |--- |
| [3.0.1](#3.0.1) |Říjen 04, 2018 |--- |
| [2.3.3](#2.3.3) |Září 08, 2018 |--- |
| [2.3.2](#2.3.2) |8\. května 2018 |--- |
| [2.3.1](#2.3.1) |21. prosince 2017 |--- |
| [2.3.0](#2.3.0) |10. listopadu 2017 |--- |
| [2.2.1](#2.2.1) |Září 29, 2017 |--- |
| [2.2.0](#2.2.0) |10. května 2017 |--- |
| [2.1.0](#2.1.0) |Květen 01, 2017 |--- |
| [2.0.1](#2.0.1) |30. října 2016 |--- |
| [2.0.0](#2.0.0) |29. září 2016 |--- |
| [1.9.0](#1.9.0) |7\. července 2016 |--- |
| [1.8.0](#1.8.0) |14. června 2016 |--- |
| [1.7.0](#1.7.0) |26. dubna 2016 |--- |
| [1.6.1](#1.6.1) |08 8. dubna 2016 |--- |
| [1.6.0](#1.6.0) |29. března 2016 |--- |
| [1.5.0](#1.5.0) |Leden 03, 2016 |--- |
| [1.4.2](#1.4.2) |06 6. října 2015 |--- |
| 1.4.1 |06 6. října 2015 |--- |
| [1.2.0](#1.2.0) |6\. srpna 2015 |--- |
| [1.1.0](#1.1.0) |09. července 2015 |--- |
| [1.0.1](#1.0.1) |25. května 2015 |--- |
| [1.0.0](#1.0.0) |07. dubna 2015 |--- |
| 0.9.4-prelease |14. ledna 2015 |29. února 2016 |
| 0.9.3-prelease |9\. prosince 2014 |29. února 2016 |
| 0.9.2-prelease |25. listopadu 2014 |29. února 2016 |
| 0.9.1-prelease |23. září 2014 |29. února 2016 |
| 0.9.0-prelease |21. srpna 2014 |29. února 2016 |

## <a name="faq"></a>Nejčastější dotazy
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Další informace najdete v tématech
Další informace o službě Cosmos DB najdete v tématu [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stránku služby. 

