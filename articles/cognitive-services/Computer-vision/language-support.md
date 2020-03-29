---
title: Jazyková podpora - Počítačové vidění
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje seznam přirozených jazyků podporovaných funkcemi počítačového vidění; OCR, rozpoznat text a číst.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: a834c68119340d796f87971912a07fc0524a6d21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220135"
---
# <a name="language-support-for-computer-vision"></a>Jazyková podpora pro počítačové vidění

Některé funkce počítačového vidění podporují více jazyků; všechny funkce, které zde nejsou uvedeny, podporují pouze angličtinu.

## <a name="text-recognition"></a>Rozpoznávání textu

Počítačové vidění dokáže rozpoznat text v mnoha jazycích. Konkrétně [rozhraní API OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) podporuje různé jazyky, zatímco [rozhraní READ](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) API a rozpoznat [textové](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) rozhraní API podporují pouze angličtinu. Další informace o této funkci a výhodách jednotlivých rozhraní API naleznete v tématu [Rozpoznávání tištěného a ručně psaného textu.](concept-recognizing-text.md)

Rozpoznávání OCR automaticky detekuje jazyk vstupního materiálu, takže není nutné při volání rozhraní API zadávat kód jazyka. Kódy jazyků jsou však vždy `"language"` vráceny jako hodnota uzlu v odpovědi JSON.

|Jazyk| Kód jazyka | OCR API |
|:-----|:----:|:-----:|
|Arabština | `ar`|✔ |
|Čínština (zjednodušená) | `zh-Hans`|✔ |
|Čínština (tradiční) | `zh-Hant`|✔ |
|Čeština | `cs` |✔ |
|Dánština | `da` |✔ |
|Nizozemština | `nl` |✔ |
|Angličtina | `en` |✔ |
|Finština | `fi` |✔ |
|Francouzština | `fr` |✔ |
|Němčina | `de` |✔ |
|Řečtina | `el` |✔ |
|Maďarština | `hu` |✔ |
|Italština | `it` |✔ |
|Japonština | `ja` |✔ |
|Korejština | `ko` |✔ |
|Norština | `nb` |✔ |
|Polština | `pl` |✔ |
|Portugalština | `pt` |✔ |
|Rumunština | `ro` |✔ |
|Ruština | `ru` |✔ |
|Srbština (cyrilice) | `sr-Cyrl` |✔ |
|Srbština (latinka) | `sr-Latn` |✔ |
|Slovenština | `sk` |✔ |
|Španělština | `es` |✔ |
|Švédština | `sw` |✔ |
|Turečtina | `tr` |✔ |

## <a name="image-analysis"></a>Analýza obrazu

Některé akce [analyzovat - image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API můžete vrátit výsledky `language` v jiných jazycích, zadaný parametrdotaz. Jiné akce vrátí výsledky v angličtině bez ohledu na to, jaký jazyk je určen a jiné vyvolat výjimku pro nepodporované jazyky. Akce jsou určeny s parametry dotazu `visualFeatures` a; `details` V [přehledu](home.md) najdete seznam všech akcí, které můžete s analýzou obrázků provést.

|Jazyk | Kód jazyka | Kategorie | Značky | Popis | Pro dospělé | Značky | Barvy | Tváře | ImageType | Objekty | Celebrity | Význačná místa |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Chinese | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Angličtina | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japonština | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Portugalština | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Španělština | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Další kroky

Můžete začít používat funkce počítačového vidění uvedené v této příručce.

* [Analýza místního obrázku (REST)](./quickstarts/csharp-analyze.md)
* [Extrahování tištěného textu (REST)](./quickstarts/csharp-print-text.md)