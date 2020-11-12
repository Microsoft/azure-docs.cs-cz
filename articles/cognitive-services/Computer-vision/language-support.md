---
title: Jazyková podpora – Počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje seznam přirozených jazyků podporovaných funkcemi Počítačové zpracování obrazu. Optické rozpoznávání znaků, analýza obrázků.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: b065b36103b69f0601daa1388b45865856543d2b
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540514"
---
# <a name="language-support-for-computer-vision"></a>Jazyková podpora pro Počítačové zpracování obrazu

Některé funkce Počítačové zpracování obrazu podporují více jazyků. všechny funkce, které tady nejsou uvedené, podporují jenom angličtinu.

## <a name="optical-character-recognition-ocr"></a>Optické rozpoznávání znaků (OCR)

Rozhraní API pro optické Počítačové zpracování obrazu podporují několik jazyků. Nevyžadují, abyste zadali kód jazyka. Další informace naleznete v tématu [optické rozpoznávání znaků (OCR)](concept-recognizing-text.md) .

|Jazyk| Kód jazyka | ROZHRANÍ API PRO OPTICKÉ ROZPOZNÁVÁNÍ ZNAKŮ | Číst 3,0 a 3,1 | Přečteno v 3.2 – Preview. 1 |
|:-----|:----:|:-----:|:---:|:---:|
|Arabština | `ar`|✔ | | |
|Čínština (zjednodušená) | `zh-Hans`|✔ | |✔ |
|Čínština (tradiční) | `zh-Hant`|✔ | | |
|Čeština | `cs` |✔ | | |
|Dánština | `da` |✔ | | |
|Nizozemština | `nl` |✔ |✔ |✔ |
|Angličtina | `en` |✔ |✔ |✔ |
|Finština | `fi` |✔ | | |
|Francouzština | `fr` |✔ |✔ |✔ |
|Němčina | `de` |✔ |✔ |✔ |
|Řečtina | `el` |✔ | | |
|Maďarština | `hu` |✔ | | |
|Italština | `it` |✔ |✔ |✔ |
|Japonština | `ja` |✔ | |✔ |
|Korejština | `ko` |✔ | | |
|Norština | `nb` |✔ | | |
|Polština | `pl` |✔ | | |
|Portugalština | `pt` |✔ |✔ |✔ |
|Rumunština | `ro` |✔ | | |
|Ruština | `ru` |✔ | | |
|Srbština (cyrilice) | `sr-Cyrl` |✔ | | |
|Srbština (latinka) | `sr-Latn` |✔ | | |
|Slovenština | `sk` |✔ | | |
|Španělština | `es` |✔ |✔ |✔ |
|Švédština | `sw` |✔ | | |
|Turečtina | `tr` |✔ | | |

## <a name="image-analysis"></a>Analýza obrázků

Některé akce rozhraní API [analyzovat-image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) mohou vracet výsledky v jiných jazycích, které jsou zadány pomocí `language` parametru dotazu. Jiné akce vrátí výsledky v angličtině bez ohledu na to, jaký jazyk je zadán, a jiné vyvolají výjimku pro nepodporované jazyky. Akce jsou zadány s `visualFeatures` `details` parametry dotazu a. Další informace najdete v [přehledu](overview.md) o všech akcích, které můžete provádět s analýzou obrázků.

|Jazyk | Kód jazyka | Kategorie | Značky | Popis | Pro dospělé | Značky | Color | Tváře | ImageType | Objekty | Celebrity | Orientační body tváře |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Čínština | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Angličtina | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japonština | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Portugalština | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Španělština | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Další kroky

Začněte využívat Počítačové zpracování obrazu funkce, které jsou uvedené v této příručce.

* [Analýza místní Image (REST)](./quickstarts/csharp-analyze.md)
* [Extrahovat vytištěný text (REST)](./quickstarts/csharp-print-text.md)
