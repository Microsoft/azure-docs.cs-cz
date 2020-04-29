---
title: Komponenty
description: Definice součástí v oboru vzdáleného vykreslování Azure
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: cb8b38addef736914a8627971e57ea2b173293d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681893"
---
# <a name="components"></a>Komponenty

Vzdálené vykreslování Azure používá [systémový vzor komponenty entity](https://en.wikipedia.org/wiki/Entity_component_system) . Zatímco [entity](entities.md) představují pozici a hierarchické složení objektů, jsou zodpovědné za implementaci chování.

Nejčastěji používané typy komponent jsou [součásti sítě](meshes.md), které do kanálu vykreslování přidávají sítě. Podobně se pro vyjmutí otevřených sítí používají [lehké komponenty](../overview/features/lights.md) , které slouží k přidání osvětlení a [řezu vyjmuté plochy](../overview/features/cut-planes.md) .

Všechny tyto komponenty používají transformaci (pozice, otočení, škálování) entity, ke které jsou připojeni, jako jejich referenční bod.

## <a name="working-with-components"></a>Práce s komponentami

Komponenty můžete snadno přidat, odebrat a manipulovat programově:

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

Komponenta je připojena k entitě při vytváření. Nedá se přesunout na jinou entitu později. Komponenty se explicitně odstraní pomocí `Component.Destroy()` nebo automaticky, když je zničena entita vlastníka součásti.

V jednom okamžiku může být do entity přidána pouze jedna instance každého typu součásti.

## <a name="unity-specific"></a>Specifické pro Unity

Integrace Unity má další funkce rozšíření pro interakci s komponentami. Viz [objekty a komponenty hry Unity](../how-tos/unity/objects-components.md).

## <a name="next-steps"></a>Další kroky

* [Hranice objektů](object-bounds.md)
* [Sítě](meshes.md)
