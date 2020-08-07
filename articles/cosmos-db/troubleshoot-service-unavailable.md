---
title: Řešení potíží s nedostupnou výjimkou služby Azure Cosmos DB
description: Jak diagnostikovat a opravit Cosmos DB nedostupná výjimka služby
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b9c24ee3b94be86ccf9d27b928c42fc194800a3b
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987371"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable"></a>Diagnostika a řešení potíží s Azure Cosmos DB službou není k dispozici
Sada SDK se nemohla připojit ke službě Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží
Následující seznam obsahuje známé příčiny a řešení nedostupných výjimek služby.

### <a name="1-the-required-ports-are-being-blocked"></a>1. požadované porty se zablokují.
Ověřte, zda jsou všechny [požadované porty](performance-tips-dotnet-sdk-v3-sql.md#networking) povoleny.

### <a name="2-client-side-transient-connectivity-issues"></a>2. problémy s přechodným připojením na straně klienta
Nedostupná plocha výjimek se může nacházet, pokud dojde k přechodným problémům s připojením, které způsobují vypršení časových limitů. Trasování zásobníku související s tímto scénářem bude obvykle obsahovat `TransportException` například:

```C#
TransportException: A client transport error occurred: The request timed out while waiting for a server response. 
(Time: xxx, activity ID: xxx, error code: ReceiveTimeout [0x0010], base error: HRESULT 0x80131500
```

Při řešení [potíží pořiďte časový limit žádosti](troubleshoot-dot-net-sdk-request-timeout.md#troubleshooting-steps) .

### <a name="3-service-outage"></a>3. výpadek služby
Zkontrolujte [stav Azure](https://status.azure.com/status) a zjistěte, jestli nedochází k trvalému problému.


## <a name="next-steps"></a>Další kroky
* [Diagnostika a řešení potíží](troubleshoot-dot-net-sdk.md) při použití Azure Cosmos DB .NET SDK
* Informace o zásadách výkonu pro [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) a [.NET v2](performance-tips.md)