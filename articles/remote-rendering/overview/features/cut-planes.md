---
title: Řezané roviny
description: Vysvětluje, co jsou řezané roviny a jak je používat
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 8075d9cd4530bafb12a338830baf0fe22eb03bce
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681022"
---
# <a name="cut-planes"></a>Řezané roviny

*Rovina řezu* je vizuální funkce, která ořízne obrazové body na jedné straně virtuální [roviny](../../concepts/meshes.md)a odhalí vnitřek ok .
Následující obrázek ukazuje efekt. Vlevo zobrazuje původní síť, vpravo se může podívat dovnitř sítě:

![Rovina řezu](./media/cutplane-1.png)

## <a name="limitations"></a>Omezení

* Vzdálené vykreslování Azure prozatím podporuje **maximálně osm aktivních rovin řezu**. Můžete vytvořit více součástí roviny řezu, ale pokud se pokusíte povolit více současně, bude aktivace ignorovat. Chcete-li přepnout, která součást by měla mít vliv na scénu, nejprve zakažte nejprve další roviny.
* Každá rovina řezu ovlivňuje všechny vzdáleně vykreslené objekty. V současné době neexistuje žádný způsob, jak vyloučit určité objekty nebo součásti sítě.
* Roviny řezu jsou čistě vizuálním prvkem, nemají vliv na výsledek [prostorových dotazů](spatial-queries.md). Pokud chcete ray cast do sítě otevřené pro říznutí, můžete nastavit počáteční bod paprsku, který má být v rovině řezu. Tímto způsobem ray může zasáhnout pouze viditelné části.

## <a name="performance-considerations"></a>Otázky výkonu

Každá aktivní rovina řezu má při vykreslování malé náklady. Zakažte nebo odstraňte roviny řezu, když nejsou potřeba.

## <a name="cutplanecomponent"></a>Komponenta CutPlaneComponent

Rovinu řezu přidáte do scény vytvořením *komponenty CutPlaneComponent*. Umístění a orientace roviny je určena [entitou](../../concepts/entities.md)vlastníka komponenty .

```cs
void CreateCutPlane(AzureSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Actions.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

### <a name="cutplanecomponent-properties"></a>Vlastnosti CutPlaneComponent

Na součásti roviny řezu jsou vystaveny následující vlastnosti:

* **Povoleno:** Zakázané roviny řezu můžete dočasně vypnout zakázáním součásti. Zakázané roviny řezu nevznikají vykreslování nad hlavou a také se nezapočítávají do globálního limitu roviny řezu.

* **Normální:** Určuje, který směr (+X,-X,+Y,-Y,+Z,-Z) se použije jako normální rovina. Tento směr je relativní vzhledem k orientaci entity vlastníka. Přesunutí a otočení entity vlastníka pro přesné umístění

* **FadeColor** a **FadeLength:**

  Pokud je hodnota alfa *fadecoloru* nenulová, obrazové body v blízkosti roviny řezu zeslabe směrem k rgb části FadeColor. Síla alfa kanálu určuje, zda bude zcela slábnout směrem k barvě fade nebo jen částečně. *FadeLength* definuje, přes jakou vzdálenost bude toto zeslabení probíhat.

## <a name="next-steps"></a>Další kroky

* [Jednostranné vykreslování](single-sided-rendering.md)
* [Prostorové dotazy](spatial-queries.md)
