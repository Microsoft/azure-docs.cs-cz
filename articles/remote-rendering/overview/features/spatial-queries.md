---
title: Prostorové dotazy
description: Jak provádět prostorové dotazy ve scéně
author: jakrams
ms.author: jakras
ms.date: 02/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: c664df586c260b3e16f64c071190055dbaeccd24
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594040"
---
# <a name="spatial-queries"></a>Prostorové dotazy

Prostorové dotazy jsou operace, se kterými můžete požádat o službu vzdáleného vykreslování, které objekty jsou umístěné v oblasti. Prostorové dotazy se často používají k implementaci interakcí, jako je například zjištění objektu, na kterém uživatel odkazuje.

Všechny prostorové dotazy jsou vyhodnocovány na serveru. V důsledku toho jsou asynchronní operace a výsledky se dostanou prodlevou, která závisí na latenci sítě. Vzhledem k tomu, že každý prostorový dotaz generuje síťový provoz, buďte opatrní, aby nedošlo k příliš velkému počtu současně.

## <a name="collision-meshes"></a>Kolize sítí

Prostorové dotazy používá modul [Havok fyzika](https://www.havok.com/products/havok-physics) a vyžadují, aby byla k dispozici vyhrazená síť kolizí. Ve výchozím nastavení [Převod modelu](../../how-tos/conversion/model-conversion.md) generuje sítě kolizí. Pokud nepotřebujete prostorové dotazy pro komplexní model, zvažte možnost zakázat generování sítě kolizí v [možnostech převodu](../../how-tos/conversion/configure-model-conversion.md), protože má dopad více způsobů:

* [Převod modelu](../../how-tos/conversion/model-conversion.md) bude trvat mnohem delší dobu.
* Převedené velikosti souborů modelu jsou výrazně větší, což má vliv na rychlost stahování.
* Doba načítání za běhu je delší.
* Spotřeba paměti procesoru za běhu je vyšší.
* Pro každou instanci modelu existuje mírné režie výkonu modulu runtime.

## <a name="ray-casts"></a>Přetypování Ray

*Jednosměrné přetypování* je prostorový dotaz, ve kterém modul runtime kontroluje, které objekty se protínají, od dané pozice a odkazuje na určitý směr. V rámci optimalizace je také k disdobu maximální vzdálenosti, aby nedošlo k hledání objektů, které jsou příliš daleko.

```cs
async void CastRay(RenderingSession session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast = new RayCast(new Double3(0, 0, 0), new Double3(0, 0, 1), 10);

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy.ClosestHit;

    RayCastQueryResult result = await session.Connection.RayCastQueryAsync(rayCast);
    RayCastHit[] hits = result.Hits;
    if (hits.Length > 0)
    {
        var hitObject = hits[0].HitObject;
        var hitPosition = hits[0].HitPosition;
        var hitNormal = hits[0].HitNormal;

        // do something with the hit information
    }
}
```

```cpp
void CastRay(ApiHandle<RenderingSession> session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast;
    rayCast.StartPos = {0, 0, 0};
    rayCast.EndPos = {0, 0, 1};
    rayCast.MaxHits = 10;

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy::ClosestHit;

    session->Connection()->RayCastQueryAsync(rayCast, [](Status status, ApiHandle<RayCastQueryResult> result)
    {
        if (status == Status::OK)
        {
            std::vector<RayCastHit> hits;
            result->GetHits(hits);

            if (hits.size() > 0)
            {
                auto hitObject = hits[0].HitObject;
                auto hitPosition = hits[0].HitPosition;
                auto hitNormal = hits[0].HitNormal;

                // do something with the hit information
            }
        }
    });
}
```


Existují tři režimy kolekce přístupů:

* **`Closest` :** V tomto režimu bude hlášeno pouze nejbližší volání.
* **`Any` :** Preferovat tento režim, pokud chcete zjistit, *jestli* v Ray nedošlo cokoli, ale nezáleží na tom, co bylo dosaženo přesně. Tento dotaz může být výrazně levnější pro vyhodnocení, ale má i několik aplikací.
* **`All` :** V tomto režimu jsou vykazovány všechny přístupy podél pole Ray, seřazené podle vzdálenosti. Tento režim nepoužívejte, pokud skutečně nepotřebujete více než první zásah. Omezte počet nahlášených přístupů s `MaxHits` možností.

Pokud chcete vyloučit objekty, které se selektivně považují za přetypování, je možné použít komponentu [HierarchicalStateOverrideComponent](override-hierarchical-state.md) .

<!--
The CollisionMask allows the query to consider or ignore some objects based on their collision layer. If an object has layer L, it will be hit only if the mask has bit L set.
It is useful in case you want to ignore objects, for instance when setting an object transparent, and trying to select another object behind it.
TODO : Add an API to make that possible.
-->

### <a name="hit-result"></a>Výsledek volání

Výsledkem dotazu přetypování do Ray je pole přístupů. Pole je prázdné, pokud nebyl nalezen žádný objekt.

K dispozice jsou následující vlastnosti:

* **`HitEntity` :** Která [entita](../../concepts/entities.md) byla vybrána.
* **`SubPartId` :** Která *podsítě* byla v [MeshComponent](../../concepts/meshes.md). Dá se použít k indexování `MeshComponent.UsedMaterials` a hledání [materiálu](../../concepts/materials.md) v tomto okamžiku.
* **`HitPosition` :** Pozice v celém světě, kde prostor protínají objekty.
* **`HitNormal` :** Střední plocha je normální plocha na pozici průsečíku.
* **`DistanceToHit` :** Vzdálenost od počáteční pozice od Ray po k dosažení.

## <a name="api-documentation"></a>Dokumentace k rozhraní API

* [C# RenderingConnection. RayCastQueryAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.raycastqueryasync)
* [C++ RenderingConnection:: RayCastQueryAsync ()](/cpp/api/remote-rendering/renderingconnection#raycastqueryasync)

## <a name="next-steps"></a>Další kroky

* [Hranice objektů](../../concepts/object-bounds.md)
* [Přepsání hierarchických stavů](override-hierarchical-state.md)