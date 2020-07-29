---
title: Sítě
description: Definice sítí v oboru vzdáleného vykreslování Azure
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: b800272a538243d39a89e4eed64bc5cbc2d53ad8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "80681763"
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

## <a name="next-steps"></a>Další kroky

* [Materiály](materials.md)
