---
title: Komponenty
description: Definice součástí v oboru vzdáleného vykreslování Azure
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: a17bfe4dac2007d3ad136598c3c4e335e2397293
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92203717"
---
# <a name="components"></a>Komponenty

Vzdálené vykreslování Azure používá [systémový vzor komponenty entity](https://en.wikipedia.org/wiki/Entity_component_system) . Zatímco [entity](entities.md) představují pozici a hierarchické složení objektů, jsou zodpovědné za implementaci chování.

Nejčastěji používané typy komponent jsou [:::no-loc text="mesh components":::](meshes.md) , které přidávají sítě do kanálu vykreslování. Podobně se pro vyjmutí otevřených sítí používají [lehké komponenty](../overview/features/lights.md) , které slouží k přidání osvětlení a [řezu vyjmuté plochy](../overview/features/cut-planes.md) .

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

## <a name="api-documentation"></a>Dokumentace k rozhraní API

* [C# ComponentBase](/dotnet/api/microsoft.azure.remoterendering.componentbase)
* [C# RemoteManager. CreateComponent ()](/dotnet/api/microsoft.azure.remoterendering.remotemanager.createcomponent)
* [Entita jazyka C#. FindComponentOfType ()](/dotnet/api/microsoft.azure.remoterendering.entity.findcomponentoftype)
* [ComponentBase C++](/cpp/api/remote-rendering/componentbase)
* [C++ RemoteManager:: CreateComponent ()](/cpp/api/remote-rendering/remotemanager#createcomponent)
* [Entita C++:: FindComponentOfType ()](/cpp/api/remote-rendering/entity#findcomponentoftype)

## <a name="next-steps"></a>Další kroky

* [Hranice objektů](object-bounds.md)
* [Sítě](meshes.md)