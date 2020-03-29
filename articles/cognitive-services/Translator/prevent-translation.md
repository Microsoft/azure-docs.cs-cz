---
title: Zabránit překladu obsahu - Překladač Text API
titleSuffix: Azure Cognitive Services
description: Zabraňte překladu obsahu pomocí rozhraní Translator Text API. Rozhraní Translator Text API umožňuje označit obsah tak, aby nebyl přeložen.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: swmachan
ms.openlocfilehash: c7be4a0ea1a9d24a8b262132632a0bbb63ae1b96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80052486"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Jak zabránit překladu obsahu pomocí rozhraní Translator Text API

Rozhraní Translator Text API umožňuje označit obsah tak, aby nebyl přeložen. Můžete například označit kód, název značky nebo slovo nebo frázi, která při lokalizování nedává smysl.

## <a name="methods-for-preventing-translation"></a>Metody prevence překladu

1. Označte `notranslate`obsah pomocí aplikace . Je to záměrné, že to funguje pouze v případě, že vstupní textType je nastaven jako HTML

   Příklad:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. Pomocí [dynamického slovníku](dynamic-dictionary.md) předepište konkrétní překlad.

3. Nepředávat řetězec translator text api pro překlad.

4. Vlastní překladač: Použijte [slovník v vlastní překladač](custom-translator/what-is-dictionary.md) předepsat překlad fráze se 100% pravděpodobností.


## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Vyhněte se překladu v volání rozhraní API překladu](reference/v3-0-translate.md)
