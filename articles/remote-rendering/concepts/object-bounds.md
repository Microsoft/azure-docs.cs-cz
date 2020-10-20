---
title: Hranice objektů
description: Vysvětluje, jak lze dotazovat hranice prostorového objektu.
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 421265bf1ee488c8e7d0c41e3ec9a250392d6f3d
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92202782"
---
# <a name="object-bounds"></a>Hranice objektů

Hranice objektů reprezentují svazek, který zabírá [entita](entities.md) a její potomky. Ve vzdáleném vykreslování Azure se hranice objektů vždycky předávají jako *ohraničovací rámečky zarovnané na osy* (AABB). Hranice objektů mohou být buď v *místním prostoru* , nebo v *prostoru světa*. V obou případech jsou vždy zarovnané osy, což znamená, že rozsahy a svazky se mohou lišit mezi znázorněním místního a světového prostoru.

## <a name="querying-object-bounds"></a>Dotazování na hranice objektů

Místní AABB [sítě](meshes.md) lze dotazovat přímo z prostředku sítě. Tyto hranice je možné transformovat do místního prostoru nebo světového prostoru entity pomocí transformace entity.

Je možné vypočítat hranice celé hierarchie objektů tímto způsobem, ale to vyžaduje, aby procházela hierarchii, dotazoval hranice pro každou síť a byly kombinovány ručně. Tato operace je únavné a neefektivní.

Lepším způsobem je zavolat `QueryLocalBoundsAsync` nebo `QueryWorldBoundsAsync` na entitu. Výpočet se pak přesměruje na server a vrátí se s minimálním zpožděním.

```cs
private BoundsQueryAsync _boundsQuery = null;

public void GetBounds(Entity entity)
{
    _boundsQuery = entity.QueryWorldBoundsAsync();
    _boundsQuery.Completed += (BoundsQueryAsync bounds) =>
    {
        if (bounds.IsRanToCompletion)
        {
            Double3 aabbMin = bounds.Result.min;
            Double3 aabbMax = bounds.Result.max;
            // ...
        }
    };
}
```

```cpp
void GetBounds(ApiHandle<Entity> entity)
{
    ApiHandle<BoundsQueryAsync> boundsQuery = *entity->QueryWorldBoundsAsync();
    boundsQuery->Completed([](ApiHandle<BoundsQueryAsync> bounds)
    {
        if (bounds->GetIsRanToCompletion())
        {
            Double3 aabbMin = bounds->GetResult().min;
            Double3 aabbMax = bounds->GetResult().max;
            // ...
        }
    });
}
```

## <a name="api-documentation"></a>Dokumentace k rozhraní API

* [Entita jazyka C#. QueryLocalBoundsAsync](/dotnet/api/microsoft.azure.remoterendering.entity.querylocalboundsasync)
* [Entita C++:: QueryLocalBoundsAsync](/cpp/api/remote-rendering/entity#querylocalboundsasync)

## <a name="next-steps"></a>Další kroky

* [Prostorové dotazy](../overview/features/spatial-queries.md)