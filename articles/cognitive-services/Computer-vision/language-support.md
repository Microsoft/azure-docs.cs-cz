---
title: Podpora jazyků – pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Seznam podporovaných funkcí pro počítačové zpracování obrazu přirozeného jazyka.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 1a70d1b2ea504d0ccfba925810a2d19d0c7583cc
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "60012642"
---
# <a name="language-support-for-computer-vision"></a>Podpora jazyků pro počítačové zpracování obrazu

Některé funkce pro počítačové zpracování obrazu podporu více jazyků; všechny funkce, které zde nejsou uvedeny podporují pouze angličtinu.

## <a name="text-recognition"></a>Rozpoznávání textu

Počítačové zpracování obrazu může rozpoznání textu v řadě jazyků. Konkrétně [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) rozhraní API podporuje různé jazyky, že [čtení](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) rozhraní API a [rozpoznat Text](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) rozhraní API podporují pouze angličtinu. Zobrazit [rozpoznat tištěné a rukou psaný text](concept-recognizing-text.md) pro další informace o této funkce a výhody každé rozhraní API.

OCR jazyk se detekuje automaticky vstupní materiálu, takže není nutné určit jazyk kódu v volání rozhraní API. Však se kódy vždy vrátí hodnotu `"language"` uzlu v odpovědi JSON.

|Jazyk| Kód jazyka | OCR API |
|:-----|:----:|:-----:|
|arabština | `ar`|✔ |
|Čínština (zjednodušená) | `zh-Hans`|✔ |
|Čínština (tradiční) | `zh-Hant`|✔ |
|Čeština | `cs` |✔ |
|dánština | `da` |✔ |
|Holandština | `nl` |✔ |
|Angličtina | `en` |✔ |
|Finština | `fi` |✔ |
|Francouzština | `fr` |✔ |
|Němčina | `de` |✔ |
|Řečtina | `el` |✔ |
|Maďarština | `hu` |✔ |
|italština | `it` |✔ |
|Japonština | `ja` |✔ |
|Korejština | `ko` |✔ |
|norština | `nb` |✔ |
|polština | `pl` |✔ |
|Portugalština | `pt` |✔ |
|Rumunština | `ro` |✔ |
|ruština | `ru` |✔ |
|Srbština (cyrilice) | `sr-Cyrl` |✔ |
|Srbština (latinka) | `sr-Latn` |✔ |
|Slovenština | `sk` |✔ |
|Španělština | `es` |✔ |
|švédština | `sw` |✔ |
|turečtina | `tr` |✔ |

## <a name="image-analysis"></a>Analýza obrázků

Některé akce [analyzovat – obrázek](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) rozhraní API může vrátit výsledky v jiných jazycích, zadaný `language` parametr dotazu. Další akce vracejí výsledky v angličtině bez ohledu na to, jaký jazyk je zadán a ostatní vyvolat výjimku u nepodporovaných jazyků. Akce jsou určeny pomocí `visualFeatures` a `details` parametrů dotazu; viz [přehled](home.md) seznam všechny akce, které vám pomůžou s analýzu obrázku.

|Jazyk | Kód jazyka | Categories | Značky | Popis | Adult | Značky | Barva | Tváře | Typ obrázku | Objekty | Celebrity | Zajímavá |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Čínština | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Angličtina | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japonština | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Portugalština | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Španělština | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Další postup

Začněte používat pro počítačové zpracování obrazu funkcích uvedených v této příručce.

* [Analýza místní image (REST)](./quickstarts/csharp-analyze.md)
* [Extrahovat tištěný text (REST)](./quickstarts/csharp-print-text.md)