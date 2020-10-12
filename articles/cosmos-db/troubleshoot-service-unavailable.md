---
title: Řešení potíží s nedostupnými výjimkami služby Azure Cosmos DB
description: Naučte se diagnostikovat a opravovat nedostupné výjimky služby Azure Cosmos DB.
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 749592b778612c6903c9c15e336de3fb00978199
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88870863"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable-exceptions"></a>Diagnostika a řešení potíží s nedostupnými výjimkami služby Azure Cosmos DB
Sada SDK se nemohla připojit k Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží
Následující seznam obsahuje známé příčiny a řešení nedostupných výjimek služby.

### <a name="the-required-ports-are-being-blocked"></a>Požadované porty jsou zablokované.
Ověřte, zda jsou všechny [požadované porty](performance-tips-dotnet-sdk-v3-sql.md#networking) povoleny.

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