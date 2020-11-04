---
title: Řešení potíží s nedostupnými výjimkami služby Azure Cosmos DB
description: Naučte se diagnostikovat a opravovat nedostupné výjimky služby Azure Cosmos DB.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: d19d30c03412ba7212211b30646acb50c3f55ece
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340018"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable-exceptions"></a>Diagnostika a řešení potíží s nedostupnými výjimkami služby Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Sada SDK se nemohla připojit k Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží
Následující seznam obsahuje známé příčiny a řešení nedostupných výjimek služby.

### <a name="the-required-ports-are-being-blocked"></a>Požadované porty jsou zablokované.
Ověřte, zda jsou všechny [požadované porty](sql-sdk-connection-modes.md#service-port-ranges) povoleny.

### <a name="client-side-transient-connectivity-issues"></a>Problémy s přechodným připojením na straně klienta
Pokud dochází k přechodným problémům s připojením, které způsobují vypršení časových limitů, můžou být nedostupné výjimky. Trasování zásobníku související s tímto scénářem obvykle bude obsahovat `TransportException` chybu. Například:

```C#
TransportException: A client transport error occurred: The request timed out while waiting for a server response. 
(Time: xxx, activity ID: xxx, error code: ReceiveTimeout [0x0010], base error: HRESULT 0x80131500
```

Postupujte podle [pokynů pro řešení potíží s časovým limitem požadavku](troubleshoot-dot-net-sdk-request-timeout.md#troubleshooting-steps) .

### <a name="service-outage"></a>Výpadek služby
Zkontrolujte [stav Azure](https://status.azure.com/status) a zjistěte, jestli nedošlo k průběžnému problému.


## <a name="next-steps"></a>Další kroky
* [Diagnostikujte a řešte](troubleshoot-dot-net-sdk.md) potíže při použití sady Azure Cosmos DB .NET SDK.
* Seznamte se s pokyny k výkonu pro [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) a [.NET v2](performance-tips.md).