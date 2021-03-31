---
title: Řešení potíží s Azure Cosmos DB výjimkou časových limitů žádostí o služby
description: Naučte se diagnostikovat a opravovat výjimky časového limitu žádostí o službu Azure Cosmos DB.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 5b188021de30561222f098e2b5782bada25d7ce0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94411239"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-timeout-exceptions"></a>Diagnostika a řešení potíží s Azure Cosmos DBmi výjimkami časového limitu požadavku
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB vrátil časový limit požadavku HTTP 408.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží
Následující seznam obsahuje známé příčiny a řešení pro výjimky časového limitu požadavku.

### <a name="check-the-sla"></a>Ověřit smlouvu SLA
Zkontrolujte [Azure Cosmos DB monitorování](monitor-cosmos-db.md) a zjistěte, jestli počet výjimek 408 porušuje smlouvu SLA Azure Cosmos DB.

#### <a name="solution-1-it-didnt-violate-the-azure-cosmos-db-sla"></a>Řešení 1: neporušilo se Azure Cosmos DB smlouvou SLA.
Aplikace by měla tento scénář zpracovat a zkusit to znovu s těmito přechodnými chybami.

#### <a name="solution-2-it-did-violate-the-azure-cosmos-db-sla"></a>Řešení 2: porušilo se Azure Cosmos DB smlouvou SLA.
Kontaktujte [podporu Azure](https://aka.ms/azure-support).
 
### <a name="hot-partition-key"></a>Klíč horkého oddílu
Azure Cosmos DB distribuuje celkovou zřízenou propustnost rovnoměrně napříč fyzickými oddíly. Pokud je k dispozici aktivní oddíl, jeden nebo více klíčů logického oddílu na fyzickém oddílu spotřebovává všechny jednotky žádostí fyzického oddílu za sekundu (RU/s). V současné době se RU/s na ostatních fyzických oddílech nepoužívá. Celkový počet spotřebovaných RU/s v podobě příznaku bude menší než celková zřízená RU/s v databázi nebo kontejneru. V požadavcích se stále zobrazuje omezení (429s) proti aktivnímu klíči logického oddílu. Pomocí [normalizované metriky pro spotřebu ru](monitor-normalized-request-units.md) můžete zjistit, jestli se u úlohy objevil aktivní oddíl. 

#### <a name="solution"></a>Řešení:
Vyberte dobrý klíč oddílu, který rovnoměrně distribuuje objem a úložiště žádostí. Přečtěte si, jak [změnit klíč oddílu](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

## <a name="next-steps"></a>Další kroky
* [Diagnostikujte a řešte](troubleshoot-dot-net-sdk.md) potíže při použití sady Azure Cosmos DB .NET SDK.
* Seznamte se s pokyny k výkonu pro [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) a [.NET v2](performance-tips.md).
* [Diagnostikujte a řešte](troubleshoot-java-sdk-v4-sql.md) potíže při použití Azure Cosmos DB Java v4 SDK.
* Seznamte se s pokyny pro výkon pro [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).