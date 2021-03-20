---
title: Jak převést formáty tokenů relací v sadě .NET SDK – Azure Cosmos DB
description: Přečtěte si, jak převést formáty tokenů relace, aby se zajistila kompatibilita mezi různými verzemi sady .NET SDK.
author: vinhms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/30/2020
ms.author: vitrinh
ms.custom: devx-track-csharp
ms.openlocfilehash: 7ed06eeb7330989b4a251dc77e516eb8ac578bff
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93342041"
---
# <a name="convert-session-token-formats-in-net-sdk"></a>Převod formátů tokenů relací v sadě .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Tento článek vysvětluje, jak převádět mezi různými formáty tokenů relace, aby se zajistila kompatibilita mezi verzemi SDK.

> [!NOTE]
> Sada SDK ve výchozím nastavení automaticky sleduje token relace a používá nejnovější token relace.  Další informace najdete na webu [využití tokenů relací](how-to-manage-consistency.md#utilize-session-tokens). Pokyny v tomto článku se vztahují jenom na tyto podmínky:
> * Váš účet Azure Cosmos DB používá konzistenci relací.
> * Tokeny relace se spravují ručně.
> * Současně používáte více verzí sady SDK.

## <a name="session-token-formats"></a>Formáty tokenů relace

Existují dva formáty tokenů relace: **jednoduchý** a **vektorový**.  Tyto dva formáty nejsou zaměnitelné, takže při předávání do klientské aplikace s různými verzemi by měl být tento formát převeden.
- Rozhraní .NET SDK V1 používá k použití **jednoduchého** formátu tokenu relace (Microsoft.Azure.DocumentDB verze 1. x).
- Formát tokenu **vektorové** relace používá sada .NET SDK V2 (Microsoft.Azure.DocumentDB verze 2. x).

### <a name="simple-session-token"></a>Jednoduchý token relace

Jednoduchý token relace má tento formát: `{pkrangeid}:{globalLSN}`

### <a name="vector-session-token"></a>Token vektorové relace

Token relace Vector má následující formát: `{pkrangeid}:{Version}#{GlobalLSN}#{RegionId1}={LocalLsn1}#{RegionId2}={LocalLsn2}....#{RegionIdN}={LocalLsnN}`

## <a name="convert-to-simple-session-token"></a>Převést na token jednoduchých relací

Pokud chcete předat token relace klientovi pomocí sady .NET SDK V1, použijte **jednoduchý** formát tokenu relace.  Použijte například následující vzorový kód pro jeho převod.

```csharp
private static readonly char[] SegmentSeparator = (new[] { '#' });
private static readonly char[] PkRangeSeparator = (new[] { ':' });

// sessionTokenToConvert = session token from previous response
string[] items = sessionTokenToConvert.Split(PkRangeSeparator, StringSplitOptions.RemoveEmptyEntries);
string[] sessionTokenSegments = items[1].Split(SessionTokenHelpers.SegmentSeparator, StringSplitOptions.RemoveEmptyEntries);

string sessionTokenInSimpleFormat;

if (sessionTokenSegments.Length == 1)
{
    // returning the same token since it already has the correct format
    sessionTokenInSimpleFormat = sessionTokenToConvert;
}
else
{
    long version = 0;
    long globalLSN = 0;

    if (!long.TryParse(sessionTokenSegments[0], out version)
        || !long.TryParse(sessionTokenSegments[1], out globalLSN))
    {
        throw new ArgumentException("Invalid session token format", sessionTokenToConvert);
    }

    sessionTokenInSimpleFormat = string.Format("{0}:{1}", items[0], globalLSN);
}
```

## <a name="convert-to-vector-session-token"></a>Převést na token vektorové relace

Pokud chcete předat token relace klientovi pomocí .NET SDK v2, použijte formát tokenu **vektorové** relace.  Použijte například následující vzorový kód pro jeho převod.

```csharp

private static readonly char[] SegmentSeparator = (new[] { '#' });
private static readonly char[] PkRangeSeparator = (new[] { ':' });

// sessionTokenToConvert = session token from previous response
string[] items = sessionTokenToConvert.Split(PkRangeSeparator, StringSplitOptions.RemoveEmptyEntries);
string[] sessionTokenSegments = items[1].Split(SegmentSeparator, StringSplitOptions.RemoveEmptyEntries);

string sessionTokenInVectorFormat;

if (sessionTokenSegments.Length == 1)
{
    long globalLSN = 0;
    if (long.TryParse(sessionTokenSegments[0], out globalLSN))
    {
        sessionTokenInVectorFormat = string.Format("{0}:-2#{1}", items[0], globalLSN);
    }
    else
    {
        throw new ArgumentException("Invalid session token format", sessionTokenToConvert);
    }
}
else
{
    // returning the same token since it already has the correct format
    sessionTokenInVectorFormat = sessionTokenToConvert;
}
```

## <a name="next-steps"></a>Další kroky

Přečtěte si následující články:

* [Použití tokenů relací ke správě konzistence v Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Vyberte správnou úroveň konzistence v Azure Cosmos DB](./consistency-levels.md)
* [Kompromisy konzistence, dostupnosti a výkonu v Azure Cosmos DB](./consistency-levels.md)
* [Kompromisy týkající se dostupnosti a výkonu pro různé úrovně konzistence](./consistency-levels.md)