---
title: Zabránit překladu obsahu – Translator Text API
titleSuffix: Azure Cognitive Services
description: Zabraňte překladu obsahu pomocí Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: f9b48b094713f6ee141c5c15d5636ca965ad61b9
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595227"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Jak zabránit překladu obsahu pomocí Translator Text API

Translator Text API umožňuje označit obsah tak, aby nebyl přeložen. Například můžete chtít označit kód, název značky nebo slovo nebo frázi, které nedává smysl, pokud je lokalizováno.

## <a name="methods-for-preventing-translation"></a>Metody prevence překladu
1. Řídicí znak @somethingtopassthrough na Twitteru nebo #somethingtopassthrough. Zrušit řídicí znak po překladu.

2. Označte svůj obsah pomocí `notranslate`.

   Příklad:

   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. Pomocí [dynamického slovníku](dynamic-dictionary.md) nařídíte konkrétní překlad.

4. Nepředávejte řetězec do Translator Text API pro překlad.

5. Vlastní Překladatel: Použijte [slovník ve vlastním překladateli](custom-translator/what-is-dictionary.md) k navýšení překladu fráze s pravděpodobností 100%.


## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Zamezení překladu ve volání rozhraní API pro překladatele](reference/v3-0-translate.md)
