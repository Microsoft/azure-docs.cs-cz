---
title: Miniatury s inteligentním oříznutím – Počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty související s generováním miniatur pro obrázky pomocí rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4874910f37b49990a659b48af0cf27921c3fcd5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "68945235"
---
# <a name="generating-smart-cropped-thumbnails-with-computer-vision"></a>Generování miniatur s inteligentním oříznutím pomocí Počítačové zpracování obrazu

Miniatura je reprezentace obrázku zmenšené velikosti. Miniatury slouží k reprezentaci obrázků a dalších dat v lépe úsporném a uživatelsky přívětivém způsobu. Rozhraní API pro počítačové zpracování obrazu používá inteligentní oříznutí spolu se změnou velikosti obrázku, aby bylo možné vytvořit intuitivní miniatury pro daný obrázek.

Počítačové zpracování obrazu algoritmus pro generování miniatury funguje takto:

1. Odstraňte rušivé elementy z obrázku a Identifikujte _oblast zájmu_ &mdash; v oblasti obrázku, ve kterém se zobrazují hlavní objekty.
1. Ořízne obrázek na základě identifikované _oblasti zájmu_.
1. Změňte poměr stran tak, aby odpovídal rozměrům cílové miniatury.

## <a name="area-of-interest"></a>Oblast zájmu

Když nahrajete obrázek, rozhraní API pro počítačové zpracování obrazu ho analyzuje a určí *oblast zájmu*. Pak může pomocí této oblasti určit, jak se má obrázek oříznout. Operace oříznutí však bude vždy odpovídat požadovanému poměru stran, je-li zadána jedna.

Nezpracované Souřadnice ohraničovacího rámečku v této stejné *oblasti zájmu* můžete také načíst voláním rozhraní **areaOfInterest** API. Tyto informace pak můžete použít k úpravě původní image, kterou chcete.

## <a name="examples"></a>Příklady

Vygenerovaná Miniatura se může velmi lišit v závislosti na tom, co zadáte pro výšku, šířku a inteligentní oříznutí, jak je znázorněno na následujícím obrázku.

![Horský obrázek vedle různých konfigurací oříznutí](./Images/thumbnail-demo.png)

Následující tabulka ilustruje typické miniatury vygenerované Počítačové zpracování obrazu pro příklady imagí. Byly vygenerovány miniatury pro zadanou cílovou výšku a šířku 50 pixelů s povoleným inteligentním oříznutím.

| Image | Miniatuře |
|-------|-----------|
|![Venkovní horská oblast na západce s silhouetteou osoby](./Images/mountain_vista.png) | ![Miniatura venkovní horské hory na západce s silhouetteou osoby](./Images/mountain_vista_thumbnail.png) |
|![Bílá květina se zeleným pozadím](./Images/flower.png) | ![Miniatura analýzy květin](./Images/flower_thumbnail.png) |
|![Žena na střeše budovy typu Apartment](./Images/woman_roof.png) | ![Miniatura žena na střeše budovy typu Apartment](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>Další kroky

Přečtěte si o [označování obrázků](concept-tagging-images.md) a [kategorizaci imagí](concept-categorizing-images.md).
