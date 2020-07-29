---
title: Řešení potíží s výjimkou časového limitu žádosti o službu Azure Cosmos DB
description: Diagnostika a oprava Cosmos DB výjimka časového limitu žádosti o službu
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: bd8102ee73721e2a56185709d1076e489ecc7607
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294110"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-timeout"></a>Diagnostika a řešení potíží s časovým limitem požadavku Azure Cosmos DB
Azure Cosmos DB vrátil časový limit žádosti HTTP 408.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží
Následující seznam obsahuje známé příčiny a řešení pro výjimky časového limitu požadavku.

### <a name="1-check-the-sla"></a>1. Projděte si smlouvu SLA.
Zákazník by měl kontrolovat [Azure Cosmos DB monitorování](monitor-cosmos-db.md) , aby zkontroloval, jestli počet výjimek 408 v rozporu s Cosmos DB smlouvou SLA.

#### <a name="solution-1-it-did-not-violate-the-cosmos-db-sla"></a>Řešení 1: neporušilo se Cosmos DB smlouvou SLA.
Aplikace by měla tento scénář zpracovat a zkusit to znovu s těmito přechodnými chybami.

#### <a name="solution-2-it-did-violate-the-cosmos-db-sla"></a>Řešení 2: porušilo se Cosmos DB smlouvou SLA.
Kontaktujte podporu Azure:https://aka.ms/azure-support
 
### <a name="2-hot-partition-key"></a>2. klíč oddílu Hot
Azure Cosmos DB distribuuje celkovou zřízenou propustnost rovnoměrně napříč fyzickými oddíly. Pokud je k dispozici aktivní oddíl, jeden nebo více klíčů logického oddílu na fyzickém oddílu spotřebovává RU/s fyzického oddílu, zatímco RU/s na ostatních fyzických oddílech se nepoužívá. Celkový počet spotřebovaných RU/s v podobě příznaku bude nižší než celkové zřízené RU/s v databázi nebo kontejneru, ale přesto se v požadavcích na aktivním klíči logického oddílu stále zobrazuje omezení (429s). Pomocí [normalizované metriky pro spotřebu ru](monitor-normalized-request-units.md) můžete zjistit, jestli se u úlohy objevil aktivní oddíl. 

#### <a name="solution"></a>Řešení:
Vyberte dobrý klíč oddílu, který rovnoměrně distribuuje objem a úložiště žádostí. Přečtěte si, jak [změnit klíč oddílu](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

## <a name="next-steps"></a>Další kroky
* [Diagnostika a řešení potíží](troubleshoot-dot-net-sdk.md) při použití Azure Cosmos DB .NET SDK
* Informace o zásadách výkonu pro [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) a [.NET v2](performance-tips.md)