---
title: Ukázkové modely
description: Zobrazuje seznam zdrojů pro ukázkové modely.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 6817601659c841ca98031f4e3e1590743bbed171
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530531"
---
# <a name="sample-models"></a>Ukázkové modely

V tomto článku jsou uvedené některé prostředky pro ukázková data, která se dají použít k testování služby Azure Remote rendering.

## <a name="built-in-sample-model"></a>Vestavěný Vzorový model

Poskytujeme vestavěný Vzorový model, který se dá vždycky načíst pomocí adresy URL **Builtin://Engine** .

![Ukázkový model](./media/sample-model.png "Ukázkový model")

Statistika modelu:

| Název | Hodnota |
|-----------|:-----------|
| [Požadovaná velikost serveru](../reference/vm-sizes.md) | standardní |
| Počet trojúhelníků | 18 700 000 |
| Počet pohyblivých částí | 2073 |
| Počet materiálů | 94 |

## <a name="third-party-data"></a>Data třetích stran

Skupina Khronos udržuje sadu vzorových modelů glTF pro testování. ARR podporuje formát glTF jak v textu (*. glTF*), tak ve formě Binary (*. glb*). Pro nejlepší vizuální výsledky doporučujeme použít modely PBR:

* [Ukázkové modely glTF](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>Další kroky

* [Rychlý Start: vykreslení modelu pomocí Unity](../quickstarts/render-model.md)
* [Rychlý start: Převod modelu pro vykreslování](../quickstarts/convert-model.md)
