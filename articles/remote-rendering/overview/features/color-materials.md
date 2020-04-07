---
title: Barevné materiály
description: Popisuje typ barevného materiálu.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 7cbcaefcc087c9f1c7c09668a27fbdef9a4802d3
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681074"
---
# <a name="color-materials"></a>Barevné materiály

*Barevné materiály* jsou jedním z podporovaných [typů materiálů](../../concepts/materials.md) v Azure Remote Rendering. Používají se pro ok, které by [neměly](../../concepts/meshes.md) přijímat žádný druh osvětlení, ale spíše být plný jas za všech okolností. To může být případ pro 'zářící' materiály, jako jsou palubní desky automobilů, žárovky, nebo pro data, která již obsahuje statické osvětlení, jako jsou modely získané prostřednictvím [fotogrammetrie](https://en.wikipedia.org/wiki/Photogrammetry).

Barevné materiály jsou účinnější než [PBR materiály](pbr-materials.md) díky jejich jednoduššístínování modelu. Podporují také různé režimy transparentnosti.

## <a name="common-material-properties"></a>Společné vlastnosti materiálu

Tyto vlastnosti jsou společné pro všechny materiály:

* **albedoColor:** Tato barva se násobí jinými barvami, například barvami *albedoMap* nebo *vertex*. Pokud je u materiálu povolena *průhlednost,* alfa kanál se `1` použije k `0` úpravě krytí, což znamená, že je zcela neprůhledné a znamená to zcela průhledné. Výchozí hodnota je bílá.

  > [!NOTE]
  > Vzhledem k tomu, že barevné materiály neodrážejí prostředí, stává se plně průhledným barevným materiálem neviditelný. U materiálů [PBR](pbr-materials.md)se to liší .

* **albedoMap:** [2D textura](../../concepts/textures.md) pro hodnoty albedo na pixel.

* **alphaClipEnabled** a **alphaClipThreshold:** Pokud je *alphaClipEnabled* true, nebudou vykresleny všechny obrazové body, kde je hodnota albedo alpha nižší než *alphaClipThreshold.* Alfa oříznutí lze použít i bez povolení průhlednosti a je mnohem rychlejší k vykreslení. Alfa oříznuté materiály jsou však stále pomalejší než plně neprůhledné materiály. Ve výchozím nastavení je alfa oříznutí zakázáno.

* **textureCoordinateScale** a **textureCoordinateOffset:** Měřítko se vynásobí do souřadnic textury UV, posun se k němu přidá. Lze použít k roztažení a posunu textur. Výchozí měřítko je (1, 1) a posun je (0, 0).

* **useVertexColor:** Pokud síť obsahuje barvy vrcholu a tato volba je povolena, barvy vrcholů sítí se vynásobí do *albedoColor* a *albedoMap*. Ve výchozím nastavení jsou barvy vrcholů zakázány.

* **isDoubleSided:** Pokud je oboustrannost nastavena na hodnotu true, trojúhelníky s tímto materiálem jsou vykresleny i v případě, že se kamera dívá na jejich zadní tváře. Ve výchozím nastavení je tato možnost zakázána. Viz také [Jednostranné vykreslování](single-sided-rendering.md).

## <a name="color-material-properties"></a>Vlastnosti barevného materiálu

Následující vlastnosti jsou specifické pro barevné materiály:

* **vertexMix:** Tato hodnota `0` `1` mezi a určuje, jak silně barva vrcholu v [síti](../../concepts/meshes.md) přispívá ke konečné barvě. Při výchozí hodnotě 1 se barva vrcholu plně vynásobí do barvy albedo. S hodnotou 0 jsou barvy vrcholu zcela ignorovány.

* **transparencyMode:** Na rozdíl od [materiálů PBR](pbr-materials.md)rozlišují barevné materiály mezi různými režimy průhlednosti:

  1. **Neprůhledné:** Výchozí režim zakáže průhlednost. Alfa výřez je stále možné, i když, a měly by být upřednostňovány, pokud je dostačující.
  
  1. **Alfablended:** Tento režim je podobný režimu průhlednosti pro materiály PBR. Měl by být použit pro průhledných materiálů, jako je sklo.

  1. **Doplňková látka:** Tento režim je nejjednodušší a nejúčinnější režim průhlednosti. Příspěvek materiálu je přidán k vykreslenému obrazu. Tento režim lze použít k simulaci zářících (ale stále průhledných) objektů, jako jsou značky používané pro zvýraznění důležitých objektů.

## <a name="next-steps"></a>Další kroky

* [PBR materiály](pbr-materials.md)
* [Textury](../../concepts/textures.md)
* [Ok](../../concepts/meshes.md)
