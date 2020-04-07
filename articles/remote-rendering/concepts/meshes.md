---
title: Ok
description: Definice míchaných misek v rozsahu vzdáleného vykreslování Azure
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: b800272a538243d39a89e4eed64bc5cbc2d53ad8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681763"
---
# <a name="meshes"></a>Ok

## <a name="mesh-resource"></a>Prostředek sítě

Vytváření sek je neměnný [sdílený prostředek](../concepts/lifetime.md), který lze vytvořit pouze prostřednictvím [převodu modelu](../how-tos/conversion/model-conversion.md). Sítě obsahují jednu nebo více *podmořských sítí*. Každá podsíť odkazuje na [materiál,](materials.md) se kterým by měla být ve výchozím nastavení vykreslena. Chcete-li umístit síť do 3D prostoru, přidejte součást [sítě](#meshcomponent) k [entitě](entities.md).

### <a name="mesh-resource-properties"></a>Vlastnosti prostředků sítě

Vlastnosti `Mesh` třídy jsou:

* **Materiály:** Řada materiálů. Každý materiál je používán jinou podsítí. Více položek v poli může odkazovat na stejný [materiál](materials.md). Tato data nelze za běhu změnit.

* **Hranice:** Ohraničovací rámeček (AABB) osy (AABB) osy v místním prostoru (AABB).

## <a name="meshcomponent"></a>Součást sítě

Třída `MeshComponent` se používá k umístění instance síťového prostředku. Každá součást sítě odkazuje na jednu síť. Může přepsat, které materiály se používají k vykreslení každé podsítě.

### <a name="meshcomponent-properties"></a>Vlastnosti komponenty MeshComponent

* **Síť:** Prostředek sítě, který používá tato součást.

* **Materiály:** Pole materiálů určené na samotné součásti sítě. Pole bude mít vždy stejnou délku jako pole *Materials* na prostředku sítě. Materiály, které nesmí být přepsány z výchozí sítě, jsou nastaveny na *hodnotu null* v tomto poli.

* **Použité Materiály:** Pole skutečně použitých materiálů pro každou podsíť. Bude shodné s daty v poli *Materials* pro hodnoty bez nuly. V opačném případě obsahuje hodnotu z pole *Materials* v instanci sítě.

## <a name="next-steps"></a>Další kroky

* [Materials](materials.md)
