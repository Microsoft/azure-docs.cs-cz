---
title: Řešení potíží s Azure Cosmos DB zakázanými výjimkami
description: Naučte se diagnostikovat a opravovat zakázané výjimky.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 02/05/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 0854e5d2c323df695460908c45714673756328c2
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99971903"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-forbidden-exceptions"></a>Diagnostika a řešení potíží Azure Cosmos DB zakázané výjimky
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Stavový kód HTTP 403 představuje požadavek, který je zakázán.

## <a name="firewall-blocking-requests"></a>Blokování požadavků branou firewall
V tomto scénáři je běžné vidět chyby, jako jsou ty níže:

```
Request originated from client IP {...} through public internet. This is blocked by your Cosmos DB account firewall settings.
```

```
Request is blocked. Please check your authorization token and Cosmos DB account firewall settings
```

### <a name="solution"></a>Řešení
Ověřte, jestli je aktuální [nastavení brány firewall](how-to-configure-firewall.md) správné a jestli chcete zahrnout IP adresy nebo sítě, ze kterých se snažíte připojit.
Pokud jste je nedávno aktualizovali, pamatujte na to, že změny můžou trvat **až 15 minut**.

## <a name="next-steps"></a>Další kroky
* Nakonfigurujte [bránu firewall protokolu IP](how-to-configure-firewall.md).
* Konfigurace přístupu z [virtuálních sítí](how-to-configure-vnet-service-endpoint.md).
* Konfigurace přístupu z [privátních koncových bodů](how-to-configure-private-endpoints.md).
