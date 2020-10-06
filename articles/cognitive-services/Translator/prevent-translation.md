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
ms.openlocfilehash: ec39b3692a90f22409e85b5502d3ea874e3282d6
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91742056"
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

3. Use the [dynamic dictionary](dynamic-dictionary.md) to prescribe a specific translation.

4. Don't pass the string to the Translator for translation.

5. Custom Translator: Use a [dictionary in Custom Translator](custom-translator/what-is-dictionary.md) to prescribe the translation of a phrase with 100% probability.


## Next steps
> [!div class="nextstepaction"]
> [Use the Translate operation to translate text](reference/v3-0-translate.md)
