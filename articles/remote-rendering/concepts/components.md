---
title: Komponenty
description: Definice součástí v oboru vzdáleného vykreslování Azure
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: 42efc1395fee28a3854420f15c2ad08b301cc1f7
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758737"
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

```cpp
// create a point light component
ApiHandle<AzureSession> session = GetCurrentlyConnectedSession();

ApiHandle<PointLightComponent> lightComponent = session->Actions()->CreateComponent(ObjectType::PointLightComponent, ownerEntity)->as<PointLightComponent>();

// ...

// destroy the component
lightComponent->Destroy();
lightComponent = nullptr;
```


Komponenta je připojena k entitě při vytváření. Nedá se přesunout na jinou entitu později. Komponenty se explicitně odstraní pomocí `Component.Destroy()` nebo automaticky, když je zničena entita vlastníka součásti.

V jednom okamžiku může být do entity přidána pouze jedna instance každého typu součásti.

## <a name="unity-specific"></a>Specifické pro Unity

Integrace Unity má další funkce rozšíření pro interakci s komponentami. Viz [objekty a komponenty hry Unity](../how-tos/unity/objects-components.md).

## <a name="next-steps"></a>Další kroky

* [Hranice objektů](object-bounds.md)
* [Sítě](meshes.md)
