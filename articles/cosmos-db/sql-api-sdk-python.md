---
title: 'Azure Cosmos DB: Rozhraní API pro SQL Python, sadu SDK a prostředky | Microsoft Docs'
description: Další informace o rozhraní API pro Python SQL a sady SDK, včetně data vydání, vyřazení dat a změny provedené mezi každou verzi sady Azure Cosmos DB Python SDK.
services: cosmos-db
documentationcenter: python
author: rnagpal
manager: kfile
editor: cgronlun
ms.assetid: 3ac344a9-b2fa-4a3f-a4cc-02d287e05469
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 1/4/2018
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cab4e44f8c5612a71aa9e024b4327a55bd2b8b87
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Azure Python SDK Cosmos DB pro SQL API: poznámky k verzi a prostředky
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

<table>

<tr><td>**Stáhněte si sadu SDK**</td><td>[Úložiště PyPI](https://pypi.python.org/pypi/pydocumentdb)</td></tr>

<tr><td>**Dokumentaci k rozhraní API**</td><td>[Referenční dokumentace rozhraní API jazyka Python](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.html)</td></tr>

<tr><td>**Pokyny k instalaci sady SDK**</td><td>[Pokyny k instalaci Python SDK](http://azure.github.io/azure-documentdb-python/)</td></tr>

<tr><td>**Můžete přispět k sadě SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-python)</td></tr>

<tr><td>**Začínáme**</td><td>[Začínáme s Python SDK](sql-api-python-application.md)</td></tr>

<tr><td>**Aktuální podporované platformy**</td><td>[Python 2.7](https://www.python.org/downloads/) a [Python 3.5](https://www.python.org/downloads/)</td></tr>
</table></br>

## <a name="release-notes"></a>Poznámky k verzi
### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* Aktualizované dokumentace k odkazu na Azure DB Cosmos místo Azure DocumentDB.

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Tato verze sady SDK vyžaduje nejnovější verze emulátoru DB Cosmos Azure k dispozici ke stažení https://aka.ms/cosmosdb-emulator.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Oprava chyby pro agregační slovník.
* Oprava chyb ořezávání lomítka v odkazu prostředku.
* Přidání testů pro kódování Unicode.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Přidaná podpora pro novou úroveň konzistence volá ConsistentPrefix.


### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Přidaná podpora pro dotazy agregace (COUNT, MIN, MAX, součet a průměr).
* Přidat možnost pro zákaz protokolu SSL ověření při spuštění emulátoru DB Cosmos.
* Odebrat závislé požadavky modulu být přesně 2.10.0 omezení.
* Snížena minimální propustnosti na dělené kolekce z 10,100 RU/s na 2 500 RU/s.
* Přidaná podpora pro povolení protokolování skriptu během provádění uložené procedury.
* Verze rozhraní REST API bumped k 2017-01-19' v této verzi.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Redakční změny provedené v dokumentační komentáře.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Přidaná podpora pro Python 3.5.
* Přidaná podpora pro sdružování připojení pomocí modulu požadavky.
* Přidaná podpora pro konzistence typu relace.
* Byla přidána podpora pro dotazy na nejvyšší nebo ORDERBY pro dělené kolekce.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Podpora zásad přidané opakování omezenému požadavky. (Omezenému požadavky obdrží žádost o míra příliš velký výjimka, kód chyby 429.) Ve výchozím nastavení Azure Cosmos DB opakuje devětkrát pro každý požadavek vyskytne kód chyby 429, aby byla dodržena retryAfter čas v hlavičku odpovědi. Časový interval opakování pevné lze nyní nastavit jako součást RetryOptions vlastnost v objektu ConnectionPolicy Pokud budete chtít ignorovat čas retryAfter vrácená serverem mezi jednotlivými pokusy o odeslání. Azure Cosmos DB nyní čeká maximálně 30 sekund pro každý požadavek, který je omezené (bez ohledu na počet opakování) a vrátí odpověď s kódem chyby 429. Nyní lze přepsat také ve vlastnosti RetryOptions ConnectionPolicy objektu.
* Cosmos DB nyní vrátí x-ms omezení--počet opakování a x-ms-throttle-retry-wait-time-ms jako opakovat hlavičky odpovědi v každé žádosti k označení omezení počtu a kumulativní čas požadavku čekali mezi jednotlivými pokusy o odeslání.
* Odebrat RetryPolicy třídu a vlastnost odpovídající (retry_policy) zveřejněné na třídě document_client a místo toho zavedl třídu RetryOptions vystavení vlastnost RetryOptions u ConnectionPolicy třídy, které je možné přepsat některé výchozí možnosti opakování.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Přidaná podpora pro účty databáze více oblast.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Přidaná podpora pro funkce čas k Live(TTL) pro dokumenty.

### <a name="a-name161161"></a><a name="1.6.1"/>1.6.1
* Opravy chyb související s serverové vytváření oddílů umožňuje speciální znaky v cestě klíče oddílu.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Implementovat [oddíly kolekce](partition-data.md) a [úrovně výkonu uživatelem definované](performance-levels.md). 

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Přidejte hodnotu Hash p & ro oddílu překladače pomoct s aplikacemi horizontálního dělení napříč více oddílů.

### <a name="a-name142142"></a><a name="1.4.2"/>1.4.2
* Implementujte Upsert. Přidaná kvůli podpoře funkcí Upsert nové metody UpsertXXX.
* Implementujte, na základě ID směrování. Žádné změny veřejné rozhraní API, všechny změny interní.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Podporuje geoprostorové index.
* Ověří vlastnost id pro všechny prostředky. Identifikátory prostředků nesmí obsahovat?, /, #, \, znaků nebo končit mezerou.
* Přidá nové záhlaví "index transformace průběh" ResourceResponse.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Implementuje zásady indexování V2.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Podporuje pro připojení proxy serveru.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK.

## <a name="release--retirement-dates"></a>Verze & vyřazení kalendářních dat
Společnost Microsoft poskytuje oznámení alespoň **dobu 12 měsíců** předem vyřazení sady SDK k funkce smooth přechodu na novější nebo podporované verzi.

Nové funkce a funkce a optimalizace, jsou přidány pouze v aktuální sadě SDK, jako takový je doporučujeme, aby vždy upgradu na nejnovější verze sady SDK v míře. 

Každá žádost o DB Cosmos pomocí vyřazeno SDK odmítají službou.

> [!WARNING]
> Všechny verze sady SDK SQL Azure pro jazyk Python starší než verze **1.0.0** bylo vyřazeno na **29. února 2016**. 
> 
> 

<br/>

| Verze | Datum vydání | Datum vyřazení |
| --- | --- | --- |
| [2.3.1](#2.3.1) |21 prosince 2017 |--- |
| [2.3.0](#2.3.0) |10 listopadu 2017 |--- |
| [2.2.1](#2.2.1) |Září 29, 2017 |--- |
| [2.2.0](#2.2.0) |10. května 2017 |--- |
| [2.1.0](#2.1.0) |01 může 2017 |--- |
| [2.0.1](#2.0.1) |30. října 2016 |--- |
| [2.0.0](#2.0.0) |29. září 2016 |--- |
| [1.9.0](#1.9.0) |07 července 2016 |--- |
| [1.8.0](#1.8.0) |14. června 2016 |--- |
| [1.7.0](#1.7.0) |26. dubna 2016 |--- |
| [1.6.1](#1.6.1) |08. dubna 2016 |--- |
| [1.6.0](#1.6.0) |29. března 2016 |--- |
| [1.5.0](#1.5.0) |03 leden 2016 |--- |
| [1.4.2](#1.4.2) |06 říjen 2015 |--- |
| [1.4.1](#1.4.1) |06 říjen 2015 |--- |
| [1.2.0](#1.2.0) |06 srpen 2015 |--- |
| [1.1.0](#1.1.0) |09 července 2015 |--- |
| [1.0.1](#1.0.1) |25 květen 2015 |--- |
| [1.0.0](#1.0.0) |07 duben 2015 |--- |
| 0.9.4-prelease |14 leden 2015 |29. února 2016 |
| 0.9.3-prelease |09 prosince 2014 |29. února 2016 |
| 0.9.2-prelease |25 listopadu 2014 |29. února 2016 |
| 0.9.1-prelease |23. září 2014 |29. února 2016 |
| 0.9.0-prelease |21 srpen 2014 |29. února 2016 |

## <a name="faq"></a>Nejčastější dotazy
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Další informace najdete v tématech
Další informace o Cosmos DB najdete v tématu [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stránku služby. 

