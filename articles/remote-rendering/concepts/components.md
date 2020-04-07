---
title: Komponenty
description: Definice součástí v rozsahu vzdáleného vykreslování Azure
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: cb8b38addef736914a8627971e57ea2b173293d6
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681893"
---
# <a name="components"></a>Komponenty

Vzdálené vykreslování Azure používá vzor [systému komponent entity.](https://en.wikipedia.org/wiki/Entity_component_system) Zatímco [entity](entities.md) představují pozici a hierarchické složení objektů, komponenty jsou zodpovědné za implementaci chování.

Nejčastěji používanými typy součástí jsou [součásti sítě](meshes.md), které přidávají sítě do kanálu vykreslování. Podobně se [světelné součásti](../overview/features/lights.md) používají k přidání osvětlení a [součásti roviny řezu](../overview/features/cut-planes.md) se používají k rozříznutí ok.

Všechny tyto součásti používají jako referenční bod transformaci (pozici, natočení, měřítko) entity, ke které jsou připojeny.

## <a name="working-with-components"></a>Práce s komponentami

Součásti můžete snadno přidávat, odebírat a manipulovat s nimi programově:

```cs
// create a point light component
AzureSession session = GetCurrentlyConnectedSession();
PointLightComponent lightComponent = session.Actions.CreateComponent(ObjectType.PointLightComponent, ownerEntity) as PointLightComponent;

lightComponent.Color = new Color4Ub(255, 150, 20, 255);
lightComponent.Intensity = 11;

// ...

// destroy the component
lightComponent.Destroy();
lightComponent = null;
```

Komponenta je připojena k entitě v době vytvoření. Poté jej nelze přesunout do jiné entity. Součásti jsou explicitně odstraněny s `Component.Destroy()` nebo automaticky při zničení entity vlastníka komponenty.

Současně může být do entity přidána pouze jedna instance každého typu součásti.

## <a name="unity-specific"></a>Jednota specifická

Unity integrace má další rozšiřující funkce pro interakci s komponentami. Viz [Unity herní objekty a komponenty](../how-tos/unity/objects-components.md).

## <a name="next-steps"></a>Další kroky

* [Hranice objektů](object-bounds.md)
* [Ok](meshes.md)
