---
title: Hranice objektů
description: Vysvětluje, jak lze dotazovat na hranice prostorového objektu.
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 1d2dfdb203b05f2f6b7de740718d7407bd88066c
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681711"
---
# <a name="object-bounds"></a>Hranice objektů

Hranice objektu představují svazek, který [entita](entities.md) a její podřízené objekty zabírají. V Azure Remote Rendering, objekt hranice jsou vždy uvedeny jako *osa zarovnávací ohraničovací rámečky* (AABB). Hranice objektů mohou být buď v *místním prostoru,* nebo ve *světovém prostoru*. Ať tak či onak, jsou vždy zarovnány osou, což znamená, že rozsahy a objem se mohou lišit mezi místní a světovou reprezentací prostoru.

## <a name="querying-object-bounds"></a>Dotazování na hranice objektů

Místní AABB [sítě](meshes.md) lze dotazovat přímo z prostředku sítě. Tyto hranice lze transformovat do místního prostoru nebo světového prostoru entity pomocí transformace entity.

Tímto způsobem je možné vypočítat hranice celé hierarchie objektů, ale to vyžaduje procházení hierarchie, dotazování na hranice pro každou síť a jejich ruční kombinování. Tato operace je zdlouhavá i neefektivní.

Lepší způsob je `QueryLocalBoundsAsync` zavolat nebo `QueryWorldBoundsAsync` na entitu. Výpočtu je pak převedena na server a vrácena s minimálním zpožděním.

``` cs
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

## <a name="next-steps"></a>Další kroky

* [Prostorové dotazy](../overview/features/spatial-queries.md)
