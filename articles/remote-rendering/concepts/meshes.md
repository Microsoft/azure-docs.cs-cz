---
title: Sítě
description: Definice sítí v oboru vzdáleného vykreslování Azure
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 33894e0d0b6f3ea50ffeac4f0c90c60f28e09f5e
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594514"
---
# <a name="meshes"></a>Sítě

## <a name="mesh-resource"></a>Prostředek sítě

Sítě jsou neměnný [sdílený prostředek](../concepts/lifetime.md), který lze vytvořit pouze prostřednictvím [konverze modelu](../how-tos/conversion/model-conversion.md). Sítě obsahují jednu nebo více *podsítí* spolu s reprezentací fyzika pro [dotazy raycast](../overview/features/spatial-queries.md). Každá podsítě odkazuje na [materiál](materials.md) , se kterým by se měla ve výchozím nastavení vykreslovat. Pokud chcete umístit síť do 3D prostoru, přidejte k [entitě](entities.md) [MeshComponent](#meshcomponent) .

### <a name="mesh-resource-properties"></a>Vlastnosti prostředku sítě

`Mesh`Vlastnosti třídy jsou:

* **Materiály:** Pole materiálů. Každý materiál se používá v různých podsítích. Více položek v poli může odkazovat na stejný [materiál](materials.md). Tato data nelze upravovat za běhu.

* **Meze:** Ohraničovací rámeček (AABB) pro mřížku na ose místní prostor () pro vrcholy.

## <a name="meshcomponent"></a>MeshComponent

`MeshComponent`Třída se používá k umístění instance objektu sítě. Každý MeshComponent odkazuje na jednu síť. Může potlačit, které materiály se použijí pro vykreslení každé podsítě.

### <a name="meshcomponent-properties"></a>Vlastnosti MeshComponent

* **Síť:** Prostředek sítě, který je používán touto komponentou.

* **Materiály:** Pole materiálů určené v samotné součásti sítě. Pole bude mít vždycky stejnou délku jako pole *materiálů* v prostředku sítě. Materiály, které se nepřepisují z výchozí mřížky, jsou v tomto poli nastavené na *hodnotu null* .

* **UsedMaterials:** Pole skutečně použitých materiálů pro každou podmřížku. Bude totožný s daty v poli *materiál* pro hodnoty, které nejsou null. V opačném případě obsahuje hodnotu z pole *materiálů* v instanci sítě.

### <a name="sharing-of-meshes"></a>Sdílení sítí

`Mesh`Prostředek se dá sdílet mezi několika instancemi součásti sítě. `Mesh`Prostředek, který je přiřazen k součásti sítě, se navíc dá kdykoli změnit programově. Následující kód ukazuje, jak klonovat síť:

```cs
Entity CloneEntityWithModel(RenderingConnection api, Entity sourceEntity)
{
    MeshComponent meshComp = sourceEntity.FindComponentOfType<MeshComponent>();
    if (meshComp != null)
    {
        Entity newEntity = api.CreateEntity();
        MeshComponent newMeshComp = api.CreateComponent(ObjectType.MeshComponent, newEntity) as MeshComponent;
        newMeshComp.Mesh = meshComp.Mesh; // share the mesh
        return newEntity;
    }
    return null;
}
```

```cpp
ApiHandle<Entity> CloneEntityWithModel(ApiHandle<RenderingConnection> api, ApiHandle<Entity> sourceEntity)
{
    if (ApiHandle<MeshComponent> meshComp = sourceEntity->FindComponentOfType<MeshComponent>())
    {
        ApiHandle<Entity> newEntity = *api->CreateEntity();
        ApiHandle<MeshComponent> newMeshComp = api->CreateComponent(ObjectType::MeshComponent, newEntity)->as<RemoteRendering::MeshComponent>();
        newMeshComp->SetMesh(meshComp->GetMesh()); // share the mesh
        return newEntity;
    }
    return nullptr;
}
```

## <a name="api-documentation"></a>Dokumentace k rozhraní API

* [Třída sítě v jazyce C#](/dotnet/api/microsoft.azure.remoterendering.mesh)
* [Třída C# MeshComponent](/dotnet/api/microsoft.azure.remoterendering.meshcomponent)
* [Třída mřížky C++](/cpp/api/remote-rendering/mesh)
* [Třída C++ MeshComponent](/cpp/api/remote-rendering/meshcomponent)


## <a name="next-steps"></a>Další kroky

* [Materiály](materials.md)