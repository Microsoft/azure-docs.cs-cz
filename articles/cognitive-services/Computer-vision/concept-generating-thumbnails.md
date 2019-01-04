---
title: Generování miniatur - pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty týkající se vytváření miniatur obrázků s využitím rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 371fa639b2edc300e44cc495393e89c9fce9c4bf
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53580854"
---
# <a name="generating-smart-cropped-thumbnails-with-computer-vision"></a>Generuje se oříznou kolem smart miniatury s pro počítačové zpracování obrazu

Miniatura je reprezentace snížit velikost obrázku. Miniatury se používá k reprezentování obrázky a další data úspornější a podporou rozložení způsobem. Rozhraní API pro počítačové zpracování obrazu používá inteligentní oříznutí, společně se změna velikosti obrázku, k vytvoření intuitivní miniatury pro danou image.

Generování miniatur algoritmus počítačového zpracování obrazu funguje takto:
1. Odeberte rušivé prvky z bitové kopie a identifikovat _oblast zájmu_&mdash;oblasti obrázku, ve kterém se zobrazí hlavní objekty.
1. Oříznout okraje obrázku podle identifikovanou _oblast zájmu_.
1. Změna poměru stran, aby odpovídala rozměrům cílového miniatur.

## <a name="area-of-interest"></a>Oblast zájmu

Když nahrajete image, rozhraní API pro počítačové zpracování obrazu analyzuje umožňuje určit, *oblast zájmu*. K určení způsobu Oříznout okraje obrázku, pak může použít této oblasti. Oříznutí operaci, ale bude vždy odpovídat požadovaný poměr stran Pokud je zadaná.

Můžete také získat nezpracovaný ohraničující souřadnice pole tohoto stejného *oblast zájmu* voláním **areaOfInterest** API místo. Tyto informace pak můžete upravit původní bitové kopie, ale chcete.

## <a name="examples"></a>Příklady

Vygenerované miniatury můžou výrazně lišit v závislosti na tom, zadejte pro výšku, šířku a inteligentní oříznutí, jak je znázorněno na následujícím obrázku.

![Miniatury](./Images/thumbnail-demo.png)

Následující tabulka ukazuje typické miniatury generovaných pro počítačové zpracování obrazu pro příklad Image. Miniatury byly vygenerovány pro zadanou cílovou výšku a šířku 50 pixelů a inteligentní oříznutí povolené.

| Image | Miniatura |
|-------|-----------|
|![Osoba na rock Horská oblast, abyste se při západu slunce](./Images/mountain_vista.png) | ![Horská oblast venku miniaturu](./Images/mountain_vista_thumbnail.png) |
|![Bílé květinu zeleným pozadím](./Images/flower.png) | ![Analýza květinu miniaturu pro zpracování obrazu](./Images/flower_thumbnail.png) |
|![Žena stropu budovy objektu apartment](./Images/woman_roof.png) | ![Žena stříška miniaturu](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>Další postup

Další informace o [označování imagí](concept-tagging-images.md) a [kategorizace obrázků](concept-categorizing-images.md).