---
title: Generování miniatur
titleSuffix: Computer Vision - Cognitive Services - Azure
description: Koncepty týkající se vytváření miniatur obrázků s využitím Azure Cognitive Services pro počítačové zpracování obrazu.
services: cognitive-services
author: deken
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: 92de0c0a428c9010188131a768074234241ed604
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44725331"
---
# <a name="generating-thumbnails"></a>Generování miniatur

Miniatura je znázornění obrázek v plné velikosti. Pohyblivá podle zařízení, jako jsou telefony, tablety a počítače vytvořit potřebu rozložení rozhraní (UX) jiný uživatel a miniatur. Pomocí inteligentního ořezávání, tato funkce pro počítačové zpracování obrazu vám pomůže vyřešit problém.

Po nahrání obrázku pro počítačové zpracování obrazu generuje vysoce kvalitní miniaturu a poté analyzuje objekty v rámci bitové kopie k identifikaci *oblasti zájmu* (ROI). To můžete volitelně Oříznout okraje obrázku, aby odpovídal požadavkům na návratnost investic. Vygenerované miniatury lze zobrazit pomocí poměr stran, které se liší od poměru stran u původní bitové kopie, tak, aby vyhovovaly vašim potřebám.

Miniatura algoritmus funguje takto:

1. Odebere rušivé prvky z bitové kopie a identifikuje objekt hlavní oblasti zájmu.
2. Obrázek ořízne tak, podle zjištěných oblasti zájmu.
3. Změní poměr stran, aby odpovídala rozměrům cílového miniatur.

Vygenerované miniatury můžou výrazně lišit v závislosti na tom, zadejte pro výšku, šířku a inteligentní oříznutí, jak je znázorněno na následujícím obrázku.

![Miniatury](./Images/thumbnail-demo.png)

## <a name="thumbnail-generation-examples"></a>Příklady generování miniatur

Následující tabulka ukazuje typické miniatury generovaných pro počítačové zpracování obrazu pro příklad Image. Miniatury byly vygenerovány pro zadanou cílovou výšku a šířku 50 pixelů a inteligentní oříznutí povolené.

| Image | Miniatura |
|-------|-----------|
|![Venkovní Horská oblast](./Images/mountain_vista.png) | ![Horská oblast venku miniaturu](./Images/mountain_vista_thumbnail.png) |
|![Analýza květinu pro zpracování obrazu](./Images/flower.png) | ![Analýza květinu miniaturu pro zpracování obrazu](./Images/flower_thumbnail.png) |
|![Žena stříška](./Images/woman_roof.png) | ![Žena stříška miniaturu](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>Další postup

Seznamte se s koncepty [označování imagí](concept-tagging-images.md) a [kategorizace obrázků](concept-categorizing-images.md).