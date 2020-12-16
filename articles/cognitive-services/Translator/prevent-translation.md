---
title: Zabránit překladu obsahu – Translator
titleSuffix: Azure Cognitive Services
description: Zabraňte překladu obsahu pomocí překladatele. Překladatel umožňuje označit obsah tak, aby nebyl přeložen.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: bf8923c1090669caa46ef51a26418933b1cda023
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97563428"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>Jak zabránit překladu obsahu pomocí překladatele

Překladatel umožňuje označit obsah tak, aby nebyl přeložen. Můžete chtít například označit programovací kód, název značky nebo slova nebo fráze, jejichž překlad nedává smysl.

## <a name="methods-for-preventing-translation"></a>Metody prevence překladu

1. Označte svůj obsah pomocí `notranslate` . Je navržený tak, že funguje jenom v případě, že je vstupní textType nastavený jako HTML.

   Příklad:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. Označte svůj obsah pomocí `translate="no"` . To funguje jenom v případě, že je vstupní textType nastavený jako HTML.

   Příklad:

   ```html
   <span translate="no">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div translate="no">This will not be translated.</div>
   <div>This will be translated. </div>
   ```
   
3. Pomocí [dynamického slovníku](dynamic-dictionary.md) nařídíte konkrétní překlad.

4. Nepředávejte řetězec překladateli pro překlad.

5. Vlastní Překladatel: pomocí [slovníku ve vlastním překladateli](custom-translator/what-is-dictionary.md) můžete naepsat převod fráze s pravděpodobností 100%.


## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Použití operace překladu k překladu textu](reference/v3-0-translate.md)
