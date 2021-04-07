---
title: Řezy roviny
description: Vysvětluje, jaké jsou vyjmuté roviny a jak je používat.
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: b3348e5a999b507aa0d286528970beb0e03f26cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594368"
---
# <a name="cut-planes"></a>Řezy roviny

*Vyjmutá rovina* je vizuální funkce, která ořízne pixely na jedné straně virtuální plochy a odhalí uvnitř [sítí](../../concepts/meshes.md).
Obrázek níže ukazuje efekt. Vlevo znázorňuje původní síť, napravo od nich může vypadat v rámci sítě:

![Vyjmutá rovina](./media/cutplane-1.png)

## <a name="cutplanecomponent"></a>CutPlaneComponent

Vyjmutou rovinu přidáte do scény vytvořením *CutPlaneComponent*. Umístění a orientace roviny je určena [entitou](../../concepts/entities.md)vlastníka součásti.

```cs
void CreateCutPlane(RenderingSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Connection.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

```cpp
void CreateCutPlane(ApiHandle<RenderingSession> session, ApiHandle<Entity> ownerEntity)
{
    ApiHandle<CutPlaneComponent> cutPlane = session->Connection()->CreateComponent(ObjectType::CutPlaneComponent, ownerEntity)->as<CutPlaneComponent>();;
    cutPlane->SetNormal(Axis::X); // normal points along the positive x-axis of the owner object's orientation
    Color4Ub fadeColor;
    fadeColor.channels = { 255, 0, 0, 128 }; // fade to 50% red
    cutPlane->SetFadeColor(fadeColor);
    cutPlane->SetFadeLength(0.05f); // gradient width: 5cm
}
```

### <a name="cutplanecomponent-properties"></a>Vlastnosti CutPlaneComponent

Následující vlastnosti jsou zpřístupněny na komponentě vyjmuté roviny:

* `Enabled`: Vyjmutím komponenty můžete dočasně vypnout vyjmuté plochy. Zakázané vyjmuté plochy neúčtují režii vykreslování a také se nepočítají s omezením globální vyjmuté plochy.

* `Normal`: Určuje směr (+ X,-X, + Y,-Y, + Z,-Z) se používá jako normální rovina. Tento směr je relativní vzhledem k orientaci entity vlastníka. Přesune a otočí entitu vlastníka pro přesné umístění.

* `FadeColor` a `FadeLength` :

  Pokud hodnota alfa *FadeColor* je nenulová, pixely blízko k vyjmuté rovině se rozstanou k části RGB v FadeColor. Síla alfa kanálu určuje, zda se bude zcela zvolna zobrazovat barvě slábnutí nebo pouze částečně. *FadeLength* definuje, přes jakou vzdálenost bude tato slábnutí probíhat.

* `ObjectFilterMask`: Maska bitového filtru, která určuje, který geometrie má vliv na vyjmutou rovinu. Podrobné informace najdete v dalším odstavci.

### <a name="selective-cut-planes"></a>Selektivní vyjmuté plochy

Jednotlivé vyjmuté roviny je možné nakonfigurovat tak, aby ovlivnily pouze konkrétní geometrii. Následující obrázek znázorňuje, jak může tato instalace vypadat v praxi:

![Selektivní vyjmuté plochy](./media/selective-cut-planes.png)

Filtrování funguje pomocí **porovnání logické bitové masky** mezi bitovou maskou na straně vyjmuté roviny a druhou bitovou maskou, která je nastavena v geometrii. Pokud výsledek logické `AND` operace mezi maskami není nula, bude tato vyjmutá rovina ovlivňovat geometrii.

* Bitová maska na komponentě vyjmuté roviny je nastavena prostřednictvím její `ObjectFilterMask` Vlastnosti.
* Bitová maska v dílčí hierarchii geometrie se nastavuje přes [HierarchicalStateOverrideComponent](override-hierarchical-state.md#features) .

Příklady:

| Vyjmout masku filtru rovin | Maska filtru geometrie  | Výsledek logického `AND` | Vyjmutá rovina má vliv na geometrii?  |
|--------------------|-------------------|-------------------|:----------------------------:|
| (0000 0001) = = 1   | (0000 0001) = = 1  | (0000 0001) = = 1  | Yes |
| (1111 0000) = = 240 | (0001 0001) = = 17 | (0001 0000) = = 16 | Yes |
| (0000 0001) = = 1   | (0000 0010) = = 2  | (0000 0000) = = 0  | No |
| (0000 0011) = = 3   | (0000 1000) = = 8  | (0000 0000) = = 0  | No |

>[!TIP]
> Nastavení vyjmuté plochy `ObjectFilterMask` na hodnotu 0 znamená, že nebude mít vliv na žádný geometrii, protože výsledek logické hodnoty `AND` nemůže být prázdný. Vykreslovací systém nepovažuje tyto roviny na první místo, takže jde o odlehčenou metodu zakázání jednotlivých vyjmutých ploch. Tyto vyjmuté roviny se také nepočítají na základě limitu osmi aktivních rovin.

## <a name="limitations"></a>Omezení

* Vzdálené vykreslování Azure podporuje **maximálně osm aktivních vyjmutých ploch**. Můžete vytvořit další komponenty vyjmuté roviny, ale pokud se pokusíte povolit více současně, bude aktivace ignorována. Chcete-li přepnout, které součásti mají mít vliv na scénu, zakažte nejprve jiné roviny.
* Vyjmuté roviny představují čistě vizuální funkci, která nemá vliv na výsledek [prostorových dotazů](spatial-queries.md). Pokud chcete přetypování do sítě s vyjmutými otevřeními, můžete nastavit počáteční bod prostorového převodu na vyjmutou plochu. Tímto způsobem může být Ray možné vysáhnout jenom viditelné části.

## <a name="performance-considerations"></a>Otázky výkonu

Každá aktivní vyjmutá rovina při vykreslování způsobí malé náklady. Pokud nepotřebujete, zakažte nebo odstraňte vyjmuté plochy.

## <a name="api-documentation"></a>Dokumentace k rozhraní API

* [Třída C# CutPlaneComponent](/dotnet/api/microsoft.azure.remoterendering.cutplanecomponent)
* [Třída C++ CutPlaneComponent](/cpp/api/remote-rendering/cutplanecomponent)

## <a name="next-steps"></a>Další kroky

* [Vykreslování s jednou stranou](single-sided-rendering.md)
* [Prostorové dotazy](spatial-queries.md)