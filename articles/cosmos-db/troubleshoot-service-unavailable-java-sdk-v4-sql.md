---
title: Řešení potíží s nedostupnými výjimkami služby Azure Cosmos DB pomocí sady Java v4 SDK
description: Naučte se diagnostikovat a opravovat nedostupné výjimky služby Azure Cosmos DB pomocí sady Java v4 SDK.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/28/2020
ms.author: kuthapar
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 03c899307b00010ca87b279ed720b92946d3673c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340055"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-java-v4-sdk-service-unavailable-exceptions"></a>Diagnostika a řešení potíží s nedostupnými výjimkami služby SDK pro Azure Cosmos DB Java v4
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
Sada Java v4 SDK se nemohla připojit k Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží
Následující seznam obsahuje známé příčiny a řešení nedostupných výjimek služby.

### <a name="the-required-ports-are-being-blocked"></a>Požadované porty jsou zablokované.
Ověřte, zda jsou všechny [požadované porty](sql-sdk-connection-modes.md#service-port-ranges) povoleny.

### <a name="client-side-transient-connectivity-issues"></a>Problémy s přechodným připojením na straně klienta
Pokud dochází k přechodným problémům s připojením, které způsobují vypršení časových limitů, můžou být nedostupné výjimky. Trasování zásobníku související s tímto scénářem bude obvykle obsahovat `ServiceUnavailableException` chybu s diagnostickými detaily. Například:

```java
Exception in thread "main" ServiceUnavailableException{userAgent=azsdk-java-cosmos/4.6.0 Linux/4.15.0-1096-azure JRE/11.0.8, error=null, resourceAddress='null', requestUri='null', statusCode=503, message=Service is currently unavailable, please retry after a while. If this problem persists please contact support.: Message: "" {"diagnostics"}
```

Postupujte podle [pokynů pro řešení potíží s časovým limitem požadavku](troubleshoot-request-timeout-java-sdk-v4-sql.md#troubleshooting-steps) .

### <a name="service-outage"></a>Výpadek služby
Zkontrolujte [stav Azure](https://status.azure.com/status) a zjistěte, jestli nedošlo k průběžnému problému.


## <a name="next-steps"></a>Další kroky
* [Diagnostikujte a řešte](troubleshoot-java-sdk-v4-sql.md) potíže při použití Azure Cosmos DB Java v4 SDK.
* Seznamte se s pokyny pro výkon pro [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).