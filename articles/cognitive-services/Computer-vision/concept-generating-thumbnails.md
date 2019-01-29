---
title: Generování miniatur - pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty týkající se vytváření miniatur obrázků s využitím rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 638a54a4cf902693dcdccecf4236e75569a677a9
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55157597"
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
