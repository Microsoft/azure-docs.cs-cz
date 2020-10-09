---
title: Ukázkové modely
description: Zobrazuje seznam zdrojů pro ukázkové modely.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 8e5dcb6c9dfa08efc0889fcab779d6cb333d2330
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88507531"
---
# <a name="sample-models"></a>Ukázkové modely

V tomto článku jsou uvedené některé prostředky pro ukázková data, která se dají použít k testování služby Azure Remote rendering.

## <a name="built-in-sample-model"></a>Vestavěný Vzorový model

Poskytujeme vestavěný Vzorový model, který se dá vždycky načíst pomocí adresy URL **Builtin://Engine** .

![Ukázkový model](./media/sample-model.png "Ukázkový model")

Statistika modelu:

| Název | Hodnota |
|-----------|:-----------|
| [Požadovaná velikost serveru](../how-tos/session-rest-api.md#create-a-session) | standardní |
| Počet trojúhelníků | 18 700 000 |
| Počet pohyblivých částí | 2073 |
| Počet materiálů | 94 |

## <a name="third-party-data"></a>Data třetích stran

Skupina Khronos udržuje sadu vzorových modelů glTF pro testování. ARR podporuje formát glTF jak v textu (*. glTF*), tak ve formě Binary (*. glb*). Pro nejlepší vizuální výsledky doporučujeme použít modely PBR:

* [Ukázkové modely glTF](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>Další kroky

* [Rychlý Start: vykreslení modelu pomocí Unity](../quickstarts/render-model.md)
* [Rychlý Start: převod modelu pro vykreslování](../quickstarts/convert-model.md)
