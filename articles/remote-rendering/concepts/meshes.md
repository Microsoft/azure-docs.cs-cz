---
title: Sítě
description: Definice sítí v oboru vzdáleného vykreslování Azure
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: eea43f48abef5e2b258251d46eca1061a2263519
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2020
ms.locfileid: "89613830"
---
# <a name="meshes"></a>Sítě

## <a name="mesh-resource"></a>Prostředek sítě

Sítě jsou neměnný [sdílený prostředek](../concepts/lifetime.md), který lze vytvořit pouze prostřednictvím [konverze modelu](../how-tos/conversion/model-conversion.md). Sítě obsahují jednu nebo více *podsítí*. Každá podsítě odkazuje na [materiál](materials.md) , se kterým by se měla ve výchozím nastavení vykreslovat. Pokud chcete umístit síť do 3D prostoru, přidejte k [entitě](entities.md) [MeshComponent](#meshcomponent) .

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

## <a name="api-documentation"></a>Dokumentace k rozhraní API

* [Třída sítě v jazyce C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.mesh)
* [Třída C# MeshComponent](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.meshcomponent)
* [Třída mřížky C++](https://docs.microsoft.com/cpp/api/remote-rendering/mesh)
* [Třída C++ MeshComponent](https://docs.microsoft.com/cpp/api/remote-rendering/meshcomponent)

## <a name="next-steps"></a>Další kroky

* [Materiály](materials.md)
