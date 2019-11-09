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
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: f3bf784898f7f51beea890d8d2a8401af1403fbc
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888123"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Jak zabránit překladu obsahu pomocí Translator Text API

Translator Text API umožňuje označit obsah tak, aby nebyl přeložen. Například můžete chtít označit kód, název značky nebo slovo nebo frázi, které nedává smysl, pokud je lokalizováno.

## <a name="methods-for-preventing-translation"></a>Metody prevence překladu
1. Řídicí znak na Twitteru @somethingtopassthrough nebo #somethingtopassthrough. Zrušit řídicí znak po překladu.

2. Označení obsahu pomocí `notranslate`.

   Příklad:

   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. Pomocí [dynamického slovníku](dynamic-dictionary.md) nařídíte konkrétní překlad.

4. Nepředávejte řetězec do Translator Text API pro překlad.

5. Vlastní Překladatel: pomocí [slovníku ve vlastním překladateli](custom-translator/what-is-dictionary.md) můžete naepsat převod fráze s pravděpodobností 100%.


## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Zamezení překladu ve volání rozhraní API pro překladatele](reference/v3-0-translate.md)
