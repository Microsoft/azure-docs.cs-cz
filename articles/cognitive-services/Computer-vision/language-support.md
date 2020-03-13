---
title: Jazyková podpora – Počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje seznam přirozených jazyků podporovaných funkcemi Počítačové zpracování obrazu. Optické rozpoznávání znaků, Rozpoznávání textu a čtení.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: a834c68119340d796f87971912a07fc0524a6d21
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220135"
---
# <a name="language-support-for-computer-vision"></a>Jazyková podpora pro Počítačové zpracování obrazu

Některé funkce Počítačové zpracování obrazu podporují více jazyků. všechny funkce, které tady nejsou uvedené, podporují jenom angličtinu.

## <a name="text-recognition"></a>Rozpoznávání textu

Počítačové zpracování obrazu umí rozpoznávat text v mnoha jazycích. Konkrétně rozhraní API pro [rozpoznávání OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) podporuje různé jazyky, zatímco rozhraní API [pro čtení](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) a [rozpoznávání textu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) API podporují jenom angličtinu. Další informace o této funkci a výhodách jednotlivých rozhraní API najdete v tématu [rozpoznávání vytištěného a rukopisného textu](concept-recognizing-text.md) .

Optické rozpoznávání znaků automaticky detekuje jazyk vstupního materiálu, takže v volání rozhraní API není nutné zadávat kód jazyka. Kódy jazyků se ale vždycky vrátí jako hodnota uzlu `"language"` v odpovědi JSON.

|Jazyk| Kód jazyka | OCR API |
|:-----|:----:|:-----:|
|Arabština | `ar`|✔ |
|Čínština (zjednodušená) | `zh-Hans`|✔ |
|Čínština (tradiční) | `zh-Hant`|✔ |
|Čeština | `cs` |✔ |
|dánština | `da` |✔ |
|Nizozemština | `nl` |✔ |
|angličtina | `en` |✔ |
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
|Ruština | `ru` |✔ |
|Srbština (cyrilice) | `sr-Cyrl` |✔ |
|Srbština (Latina) | `sr-Latn` |✔ |
|Slovenština | `sk` |✔ |
|Španělština | `es` |✔ |
|švédština | `sw` |✔ |
|turečtina | `tr` |✔ |

## <a name="image-analysis"></a>Analýza obrázků

Některé akce rozhraní API [analyzovat-image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) mohou vracet výsledky v jiných jazycích, které jsou zadány pomocí parametru dotazu `language`. Jiné akce vrátí výsledky v angličtině bez ohledu na to, jaký jazyk je zadán, a jiné vyvolají výjimku pro nepodporované jazyky. Akce jsou zadány s parametry dotazu `visualFeatures` a `details`; seznam všech akcí, které můžete provádět s analýzou obrázků, najdete v [přehledu](home.md) .

|Jazyk | Kód jazyka | Kategorie | Značky | Popis | Pro dospělé | Značky | Barva | Tváře | ImageType | Objekty | Celebrity | Památek |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Čínština | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|angličtina | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japonština | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Portugalština | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Španělština | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Další kroky

Začněte využívat Počítačové zpracování obrazu funkce, které jsou uvedené v této příručce.

* [Analýza místní Image (REST)](./quickstarts/csharp-analyze.md)
* [Extrahovat vytištěný text (REST)](./quickstarts/csharp-print-text.md)