---
title: Barevné materiály
description: Popisuje typ barevného materiálu.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: cda4aa9a811bac0ccf20caec32ee38da9b46b6c7
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2020
ms.locfileid: "89613770"
---
# <a name="color-materials"></a>Barevné materiály

*Barevné materiály* jsou jedním z podporovaných [typů materiálu](../../concepts/materials.md) ve vzdáleném vykreslování Azure. Používají se pro [sítě](../../concepts/meshes.md) , které by neměly obdržet žádný druh osvětlení, ale místo toho se musí celý jas zobrazit. Může to být případ pro "záři", jako jsou například řídicí panely aut, žárovky nebo data, která již pocházejí ze statického osvětlení, jako jsou například modely získané prostřednictvím [Photogrammetry](https://en.wikipedia.org/wiki/Photogrammetry).

Barevné materiály jsou efektivnější pro vykreslování než [PBR materiálů](pbr-materials.md) z důvodu jejich jednoduššího modelu stínování. Podporují také různé režimy průhlednosti.

## <a name="common-material-properties"></a>Společné vlastnosti materiálu

Tyto vlastnosti jsou společné pro všechny materiály:

* **albedoColor:** Tato barva se vynásobí jinými barvami, jako je například *albedoMap* nebo * :::no-loc text="vertex"::: Colors*. Pokud je pro materiál povolená *průhlednost* , alfa kanál se použije k úpravě krytí, což znamená, že je `1` plně neprůhledný a má velmi `0` transparentní význam. Výchozí hodnota je bílá.

  > [!NOTE]
  > Vzhledem k tomu, že barevné materiály neodrážejí prostředí, je plně transparentní barevný materiál neviditelný. To se u [materiálů PBR](pbr-materials.md)liší.

* **albedoMap:** [2D textura](../../concepts/textures.md) pro hodnoty albedo na pixel.

* **alphaClipEnabled** a **alphaClipThreshold:** Pokud má *alphaClipEnabled* hodnotu true, všechny pixely, kde je hodnota albedo alfa nižší než *alphaClipThreshold* , se nevykreslí. Výstřižek alfa lze použít i bez nutnosti průhlednosti a je mnohem rychlejší pro vykreslování. Oříznuté materiály alfa jsou stále pomalejší pro vykreslování než plně neprůhledné materiály, ale. Ve výchozím nastavení je oříznutí alfa zakázané.

* **textureCoordinateScale** a **textureCoordinateOffset:** stupnice se vynásobí souřadnicemi textury UV, do které se přidá posun. Dá se použít k roztažení a posunutí textur. Výchozí měřítko je (1, 1) a posun je (0, 0).

* **useVertexColor:** Pokud mřížka obsahuje :::no-loc text="vertex"::: barvy a tato možnost je povolená, :::no-loc text="vertex"::: vynásobí se barva ok v *albedoColor* a *albedoMap*. Ve výchozím nastavení je *useVertexColor* zakázaný.

* **isDoubleSided:** Pokud je vlastnost sidedness nastavená na hodnotu true, budou se tyto trojúhelníky s tímto materiálem vykreslovat i v případě, že fotoaparát hledá své zadní plošky. Ve výchozím nastavení je tato možnost zakázána. Viz také [ :::no-loc text="Single-sided"::: vykreslování](single-sided-rendering.md).

## <a name="color-material-properties"></a>Vlastnosti barevného materiálu

Následující vlastnosti jsou specifické pro barevné materiály:

* **vertexMix:** Tato hodnota mezi `0` a `1` Určuje, jak silné je :::no-loc text="vertex"::: Barva v [mřížce](../../concepts/meshes.md) přispěje k konečné barvě. U výchozí hodnoty 1 :::no-loc text="vertex"::: je barva vynásobena plnou barvou albedo. S hodnotou 0 :::no-loc text="vertex"::: jsou barvy zcela ignorovány.

* **transparencyMode:** V rozporu s [materiály PBR](pbr-materials.md)se barevné materiály liší v různých režimech průhlednosti:

  1. **Neprůhledný:** Výchozí režim zakáže průhlednost. Oříznutí alfa je stále možné, i když, a pokud je to dostatečné, měla by být upřednostňovaná.
  
  1. **AlphaBlended:** Tento režim je podobný režimu průhlednosti pro materiály PBR. Měl by se použít pro zobrazení materiálů jako skla.

  1. **Doplňková:** Tento režim je nejjednodušším a nejefektivnějším režimem transparentnosti. Příspěvek materiálu se přidá do vykreslené image. Tento režim lze použít k simulaci záře (ale stále transparentních) objektů, jako jsou například značky používané pro zvýraznění důležitých objektů.

## <a name="api-documentation"></a>Dokumentace k rozhraní API

* [Třída C# ColorMaterial](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.colormaterial)
* [C# RemoteManager. CreateMaterial ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.creatematerial)
* [Třída C++ ColorMaterial](https://docs.microsoft.com/cpp/api/remote-rendering/colormaterial)
* [C++ RemoteManager:: CreateMaterial ()](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#creatematerial)

## <a name="next-steps"></a>Další kroky

* [Materiály PBR](pbr-materials.md)
* [Textury](../../concepts/textures.md)
* [Sítě](../../concepts/meshes.md)
