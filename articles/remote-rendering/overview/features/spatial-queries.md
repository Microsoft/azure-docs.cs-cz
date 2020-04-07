---
title: Prostorové dotazy
description: Jak provést prostorové dotazy ve scéně
author: jakrams
ms.author: jakras
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 9a981aeb08ec46900994fd599b592b9f16034f34
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680528"
---
# <a name="spatial-queries"></a>Prostorové dotazy

Prostorové dotazy jsou operace, pomocí kterých se můžete zeptat služby vzdáleného vykreslování, které objekty jsou umístěny v oblasti. Prostorové dotazy se často používají k implementaci interakcí, jako je například zjištění, na který objekt uživatel ukazuje.

Všechny prostorové dotazy jsou vyhodnocovány na serveru. V důsledku toho se jedná o asynchronní operace a výsledky dorazí se zpožděním, které závisí na latenci sítě. Vzhledem k tomu, že každý prostorový dotaz generuje síťový provoz, dávejte pozor, abyste nedělali příliš mnoho najednou.

## <a name="collision-meshes"></a>Kolizní křovina

Prostorové dotazy jsou poháněny motorem [Havok Physics](https://www.havok.com/products/havok-physics) a vyžadují, aby byla přítomna vyhrazená kolize. Ve výchozím nastavení [generuje převod modelu](../../how-tos/conversion/model-conversion.md) kolize míchací kódy. Pokud nepožadujete prostorové dotazy na složitý model, zvažte zakázání generování mřížky kolizí v [možnostech převodu](../../how-tos/conversion/configure-model-conversion.md), protože to má dopad několika způsoby:

* [Převod modelu](../../how-tos/conversion/model-conversion.md) bude trvat podstatně déle.
* Převedené velikosti souborů modelu jsou znatelně větší, což má vliv na rychlost stahování.
* Doba načítání za běhu je delší.
* Spotřeba paměti procesoru runtime je vyšší.
* Je tu mírné řízení za běhu výkon režie pro každou instanci modelu.

## <a name="ray-casts"></a>Ray odlitky

Ray *cast* je prostorový dotaz, kde modul runtime kontroluje, které objekty jsou protnuty paprskem, počínaje danou polohou a směřující do určitého směru. Jako optimalizace je také uvedena maximální vzdálenost paprsku, aby se nehledaly objekty, které jsou příliš daleko.

````c#
async void CastRay(AzureSession session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast = new RayCast(new Double3(0, 0, 0), new Double3(0, 0, 1), 10);

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy.ClosestHit;

    RayCastHit[] hits = await session.Actions.RayCastQueryAsync(rayCast).AsTask();

    if (hits.Length > 0)
    {
        var hitObject = hits[0].HitEntity;
        var hitPosition = hits[0].HitPosition;
        var hitNormal = hits[0].HitNormal;

        // do something with the hit information
    }
}
````

Existují tři režimy shromažďování přístupů:

* **Nejbližší:** V tomto režimu bude hlášen pouze nejbližší zásah.
* **Jakékoliv:** Preferujte tento režim, když vše, co chcete vědět, je, *zda* ray by hit něco, ale je jedno, co bylo přesně zasaženo. Tento dotaz může být podstatně levnější vyhodnotit, ale má také jen málo aplikací.
* **Vše:** V tomto režimu jsou hlášeny všechny zásahy podél paprsku seřazené podle vzdálenosti. Nepoužívejte tento režim, pokud opravdu nepotřebujete více než první zásah. Omezte počet nahlášených přístupů s `MaxHits` možností.

Chcete-li vyloučit objekty selektivně z považovány za přetypování paprsku, [hierarchicalStateOverrideComponent](override-hierarchical-state.md) komponenty lze použít.

<!--
The CollisionMask allows the quey to consider or ignore some objects based on their collision layer. If an object has layer L, it will be hit only if the mask has  bit L set.
It is useful in case you want to ignore objects, for instance when setting an object transparent, and trying to select another object behind it.
TODO : Add an API to make that possible.
-->

### <a name="hit-result"></a>Výsledek zásahu

Výsledkem dotazu ray cast je pole přístupů. Pole je prázdné, pokud nebyl nalezen žádný objekt.

Zásah má následující vlastnosti:

* **HitEntity:** Která [entita](../../concepts/entities.md) byla zasažena.
* **SubPartId:** Která *podsíť* byla přístupů v [MeshComponent](../../concepts/meshes.md). Lze použít k `MeshComponent.UsedMaterials` indexování a vyhlédnout [materiál](../../concepts/materials.md) v tomto bodě.
* **HitPosition:** Světová vesmírná pozice, kde paprsek protínal objekt.
* **HitNormal:** Plocha světového prostoru normála sítě v poloze průsečíku.
* **Vzdálenosttohit:** Vzdálenost od počáteční polohy paprsku k zásahu.

## <a name="next-steps"></a>Další kroky

* [Hranice objektů](../../concepts/object-bounds.md)
* [Přepsání hierarchických stavů](override-hierarchical-state.md)
