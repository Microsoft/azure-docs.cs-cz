---
title: Zabránit překladu obsahu – Translator Text API
titleSuffix: Azure Cognitive Services
description: Zabraňte překladu obsahu pomocí Translator Text API. Translator Text API umožňuje označit obsah tak, aby nebyl přeložen.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: swmachan
ms.openlocfilehash: c7be4a0ea1a9d24a8b262132632a0bbb63ae1b96
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80052486"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Jak zabránit překladu obsahu pomocí Translator Text API

Translator Text API umožňuje označit obsah tak, aby nebyl přeložen. Například můžete chtít označit kód, název značky nebo slovo nebo frázi, které nedává smysl, pokud je lokalizováno.

## <a name="methods-for-preventing-translation"></a>Metody prevence překladu

1. Označte svůj obsah pomocí `notranslate`. Je navržený tak, že funguje jenom v případě, že je vstupní textType nastavený jako HTML.

   Příklad:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. Pomocí [dynamického slovníku](dynamic-dictionary.md) nařídíte konkrétní překlad.

3. Nepředávejte řetězec do Translator Text API pro překlad.

4. Vlastní Překladatel: pomocí [slovníku ve vlastním překladateli](custom-translator/what-is-dictionary.md) můžete naepsat převod fráze s pravděpodobností 100%.


## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Zamezení překladu ve volání rozhraní API pro překladatele](reference/v3-0-translate.md)
