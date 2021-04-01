---
title: Migrace aplikace tak, aby používala sadu Azure Cosmos DB .NET SDK 2,0 (Microsoft. Azure. Cosmos)
description: Přečtěte si, jak upgradovat stávající aplikaci .NET ze sady v1 SDK na rozhraní .NET SDK v2 pro Core (SQL) API.
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/15/2020
ms.openlocfilehash: 88c40452da72ed4ab43d2d7613636136a5b78cfe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94550060"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-net-sdk-v2"></a>Migrace aplikace tak, aby používala sadu Azure Cosmos DB .NET SDK v2

> [!IMPORTANT]
> Je důležité si uvědomit, že verze V3 sady .NET SDK je [aktuálně dostupná a](migrate-dotnet-v3.md)že je k dispozici plán migrace z verze V2 na v3. Další informace o sadě Azure Cosmos DB .NET SDK v2 naleznete v [poznámkách k verzi](sql-api-sdk-dotnet.md), [úložišti GitHub .NET](https://github.com/Azure/azure-cosmos-dotnet-v2), [tipy pro výkon](performance-tips.md).NET SDK v2 a [Průvodci odstraňováním potíží](troubleshoot-dot-net-sdk.md).
>

Tento článek popisuje některé z důležitých informací o upgradu stávající aplikace .NET V1 na Azure Cosmos DB rozhraní .NET SDK v2 pro Core (SQL) API. Sada Azure Cosmos DB .NET SDK v2 odpovídá `Microsoft.Azure.DocumentDB` oboru názvů. Informace uvedené v tomto dokumentu můžete použít, pokud migrujete aplikaci z některé z následujících Azure Cosmos DB platforem .NET pro použití sady v2 SDK `Microsoft.Azure.Cosmos` :

* Sada Azure Cosmos DB .NET Framework v1 SDK pro SQL API
* Azure Cosmos DB .NET Core SDK V1 pro rozhraní SQL API

## <a name="whats-available-in-the-net-v2-sdk"></a>Co je k dispozici v sadě .NET v2 SDK

Sada v2 SDK obsahuje mnoho vylepšení použitelnosti a výkonu, včetně:

* Podpora režimu TCP Direct pro klienty s jiným systémem než Windows
* Podpora zápisu ve více oblastech
* Vylepšení výkonu dotazů
* Podpora pro geoprostorové/geometrické kolekce a indexování
* Zvýšená vylepšení pro diagnostiku přímých/TCP přenosů
* Aktualizace přenosového zásobníku TCP pro snížení počtu navázaných připojení
* Vylepšení snižování latence v RequestTimeout

Většina logiky opakování a nižší úrovně sady SDK zůstane převážně nezměněná.

## <a name="why-migrate-to-the-net-v2-sdk"></a>Proč migrovat do sady .NET v2 SDK

Kromě mnoha vylepšení výkonu se nové investice do funkcí v nejnovější sadě SDK nedají přenést do starších verzí.

Starší sady SDK budou navíc nahrazeny novějšími verzemi a sada v1 SDK bude přecházet do [režimu údržby](sql-api-sdk-dotnet.md). Pro nejlepší vývojové prostředí doporučujeme migrovat aplikaci na novější verzi sady SDK.

## <a name="major-changes-from-v1-sdk-to-v2-sdk"></a>Významné změny ze sady v1 SDK na verzi v2 SDK

### <a name="direct-mode--tcp"></a>Přímý režim + TCP

Sada .NET v2 SDK teď podporuje režim Direct i Gateway. Přímý režim podporuje připojení prostřednictvím protokolu TCP a nabízí lepší výkon, protože se připojuje přímo k replikám back-endu s menším počtem segmentů směrování sítě.

Další podrobnosti najdete v [příručce Azure Cosmos DB SQL SDK pro režimy připojení](sql-sdk-connection-modes.md).

### <a name="session-token-formatting"></a>Formátování tokenu relace

Sada v2 SDK již nepoužívá formát *jednoduchého* tokenu relace, jak je použit v V1, ale sada SDK používá formátování *vektoru* . Při předávání do klientské aplikace s různými verzemi by měl být tento formát převeden, protože formáty nejsou zaměnitelné.

Další informace najdete v tématu [Převod formátů tokenů relací v sadě .NET SDK](how-to-convert-session-token.md).

### <a name="using-the-net-change-feed-processor-sdk"></a>Používání sady SDK pro procesor změn .NET

Knihovna pro zpracování kanálu změn .NET 2.1. x vyžaduje `Microsoft.Azure.DocumentDB` 2,0 nebo novější.

Knihovna 2.1. x má následující klíčové změny:

* Vylepšení stability a diagnostiky
* Vylepšené zpracování chyb a výjimek
* Další podpora pro dělené kolekce zapůjčení
* Pokročilá rozšíření pro implementaci `ChangeFeedDocument` rozhraní a třídy pro další zpracování chyb a trasování
* Přidání podpory pro použití vlastního úložiště k uchování tokenů pokračování na oddíl

Další informace najdete v [poznámkách k verzi](sql-api-sdk-dotnet-changefeed.md)pro změny v knihovně procesoru Change feed.

### <a name="using-the-bulk-executor-library"></a>Použití knihovny hromadných prováděcích modulů

Knihovna hromadných prováděcích knihoven v2 aktuálně obsahuje závislost na sadě Azure Cosmos DB .NET SDK 2.5.1 nebo novějším.

Další informace najdete v tématu [Přehled knihovny hromadného prováděcího modulu Azure Cosmos DB](bulk-executor-overview.md) a [poznámky k verzi](sql-api-sdk-bulk-executor-dot-net.md)knihovny hromadného prováděcího modulu .NET.

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [dalších tipůch k výkonu](sql-api-get-started.md) , pomocí Azure Cosmos DB pro SQL API v2 získáte optimalizaci vaší aplikace za účelem dosažení maximálního výkonu.
* Další informace o [tom, co můžete dělat se sadou v2 SDK](sql-api-dotnet-samples.md)