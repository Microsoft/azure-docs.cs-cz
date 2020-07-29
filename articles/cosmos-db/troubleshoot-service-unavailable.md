---
title: Řešení potíží s nedostupnou výjimkou služby Azure Cosmos DB
description: Jak diagnostikovat a opravit Cosmos DB nedostupná výjimka služby
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 12ecec7cf8f406ed53fb5e054fc304bf672cbbb0
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294106"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable"></a>Diagnostika a řešení potíží s Azure Cosmos DB službou není k dispozici
Sada SDK se nemohla připojit ke službě Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží
Následující seznam obsahuje známé příčiny a řešení nedostupných výjimek služby.

### <a name="1-the-required-ports-are-not-enabled"></a>1. požadované porty nejsou povolené.
Ověřte, zda jsou všechny [požadované porty](performance-tips-dotnet-sdk-v3-sql.md#networking) povoleny.

## <a name="if-an-existing-application-or-service-started-getting-503"></a>Pokud existující aplikace nebo služba začala získat 503

### <a name="1-there-is-an-outage"></a>1. došlo k výpadku.
Zkontrolujte [stav Azure](https://status.azure.com/status) a zjistěte, jestli nedochází k trvalému problému.

### <a name="2-snat-port-exhaustion"></a>2. vyčerpání portů SNAT
Postupujte podle pokynů v [Průvodci vyčerpáním portů SNAT](troubleshoot-dot-net-sdk.md#snat).

### <a name="3-the-required-ports-are-being-blocked"></a>3. požadované porty se zablokují.
Ověřte, zda jsou všechny [požadované porty](performance-tips-dotnet-sdk-v3-sql.md#networking) povoleny.

## <a name="next-steps"></a>Další kroky
* [Diagnostika a řešení potíží](troubleshoot-dot-net-sdk.md) při použití Azure Cosmos DB .NET SDK
* Informace o zásadách výkonu pro [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) a [.NET v2](performance-tips.md)