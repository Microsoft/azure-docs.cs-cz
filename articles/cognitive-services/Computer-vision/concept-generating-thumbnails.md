---
title: Miniatury s inteligentním oříznutím – Počítačové vidění
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68945235"
---
# <a name="generating-smart-cropped-thumbnails-with-computer-vision"></a>Generování inteligentních oříznutých miniatur pomocí počítačového vidění

Miniatura je zmenšená velikost obrazu. Miniatury se používají k reprezentaci obrázků a dalších dat hospodárnějším způsobem, který je příznivější pro rozložení. Rozhraní Api pro počítačové zpracování obrazu využívá inteligentní oříznutí spolu se změnami velikosti obrázku k vytvoření intuitivních miniatur pro daný obrázek.

Algoritmus generování miniatur počítačového vidění funguje následovně:

1. Odstraňte rušivé prvky z obrazu a identifikujte _oblast zájmu_&mdash;o oblast obrazu, ve které se hlavní objekt (hlavní objekty) zobrazuje.
1. Ořízněte obrázek na základě identifikované _oblasti zájmu_.
1. Změňte poměr stran tak, aby odpovídal cílovým rozměrům miniatur.

## <a name="area-of-interest"></a>Oblast zájmu

Když obrázek nahrajete, rozhraní API pro počítačové zpracování obrazu jej analyzuje, aby určilo *oblast zájmu*. Tuto oblast pak můžete použít k určení způsobu oříznutí obrázku. Operace oříznutí však bude vždy odpovídat požadovanému poměru stran, pokud je zadán.

Můžete také získat nezpracované ohraničovací souřadnice této stejné *oblasti zájmu* voláním **areaOfInterest** API místo. Tyto informace pak můžete použít k úpravě původního obrázku, jak chcete.

## <a name="examples"></a>Příklady

Vygenerovaná miniatura se může značně lišit v závislosti na tom, co zadáte pro výšku, šířku a inteligentní oříznutí, jak je znázorněno na následujícím obrázku.

![Obrázek hory vedle různých konfigurací oříznutí](./Images/thumbnail-demo.png)

Následující tabulka znázorňuje typické miniatury generované programem Počítačové vidění pro ukázkové obrázky. Miniatury byly generovány pro zadanou cílovou výšku a šířku 50 pixelů s povoleným inteligentním oříznutím.

| Image | Miniaturu |
|-------|-----------|
|![Venkovní hora při západu slunce, s siluetou osoby](./Images/mountain_vista.png) | ![Miniatura Venkovní hora při západu slunce, s siluetou osoby](./Images/mountain_vista_thumbnail.png) |
|![Bílý květ se zeleným pozadím](./Images/flower.png) | ![Miniatura květiny vision Analyze](./Images/flower_thumbnail.png) |
|![Žena na střeše bytového domu](./Images/woman_roof.png) | ![miniatura ženy na střeše bytového domu](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>Další kroky

Přečtěte si o [označování obrázků](concept-tagging-images.md) a [kategorizaci obrázků](concept-categorizing-images.md).
