---
title: Hranice objektů
description: Vysvětluje, jak lze dotazovat hranice prostorového objektu.
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: df04b767035dffb62fde89d1e74b808d62fcc943
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594480"
---
# <a name="object-bounds"></a>Hranice objektů

Hranice objektů reprezentují svazek, který zabírá [entita](entities.md) a její potomky. Ve vzdáleném vykreslování Azure se hranice objektů vždycky předávají jako *ohraničovací rámečky zarovnané na osy* (AABB). Hranice objektů mohou být buď v *místním prostoru* , nebo v *prostoru světa*. V obou případech jsou vždy zarovnané osy, což znamená, že rozsahy a svazky se mohou lišit mezi znázorněním místního a světového prostoru.

## <a name="querying-object-bounds"></a>Dotazování na hranice objektů

Z připojeného [rámečku na místní ose lze](meshes.md) dotazovat přímo z prostředku sítě. Tyto hranice je možné transformovat do místního prostoru nebo světového prostoru entity pomocí transformace entity.

Je možné vypočítat hranice celé hierarchie objektů tímto způsobem, ale to vyžaduje, aby procházela hierarchii, dotazoval hranice pro každou síť a byly kombinovány ručně. Tato operace je únavné a neefektivní.

Lepším způsobem je zavolat `QueryLocalBoundsAsync` nebo `QueryWorldBoundsAsync` na entitu. Výpočet se pak přesměruje na server a vrátí se s minimálním zpožděním.

```cs
public async void GetBounds(Entity entity)
{
    try
    {
        Task<Bounds> boundsQuery = entity.QueryWorldBoundsAsync();
        Bounds result = await boundsQuery;
    
        Double3 aabbMin = result.Min;
        Double3 aabbMax = result.Max;
        // ...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void GetBounds(ApiHandle<Entity> entity)
{
    entity->QueryWorldBoundsAsync(
        // completion callback:
        [](Status status, Bounds bounds)
        {
           if (status == Status::OK)
            {
                Double3 aabbMin = bounds.Min;
                Double3 aabbMax = bounds.Max;
                // ...
            }
        }
    );
}
```

## <a name="api-documentation"></a>Dokumentace k rozhraní API

* [Entita jazyka C#. QueryLocalBoundsAsync](/dotnet/api/microsoft.azure.remoterendering.entity.querylocalboundsasync)
* [Entita C++:: QueryLocalBoundsAsync](/cpp/api/remote-rendering/entity#querylocalboundsasync)

## <a name="next-steps"></a>Další kroky

* [Prostorové dotazy](../overview/features/spatial-queries.md)