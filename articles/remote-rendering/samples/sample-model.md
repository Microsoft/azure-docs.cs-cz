---
title: Vzorové modely
description: Uvádí zdroje pro ukázkové modely.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 0c28d36c7934fbbac0ddd11562c8dc237b0360ee
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679482"
---
# <a name="sample-models"></a>Vzorové modely

Tento článek uvádí některé prostředky pro ukázková data, která lze použít k testování služby Vzdálené vykreslování Azure.

## <a name="built-in-sample-model"></a>Vestavěný ukázkový model

Poskytujeme vestavěný ukázkový model, který lze vždy načíst pomocí adresy URL **builtin://Engine**

![Vzorový model](./media/sample-model.png "Vzorový model")

Statistika modelu:

| Name (Název) | Hodnota |
|-----------|:-----------|
| [Požadovaná velikost virtuálního počítače](../how-tos/session-rest-api.md#create-a-session) | Standardní |
| Počet trojúhelníků | 18,7 milionu |
| Počet pohyblivých částí | 2073 |
| Počet materiálů | 94 |

## <a name="third-party-data"></a>Údaje třetích stran

Skupina Khronos udržuje sadu vzorových modelů GLTF pro testování. ARR podporuje formát glTF jak v textu (*.gltf*) tak v binárním (*.glb*) formě. Doporučujeme používat modely PBR pro dosažení nejlepších vizuálních výsledků:

* [glTF Ukázkové modely](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>Další kroky

* [Úvodní příručka: Vykreslení modelu pomocí Unity](../quickstarts/render-model.md)
* [Úvodní příručka: Převod modelu pro vykreslování](../quickstarts/convert-model.md)
