---
title: Zabránit obsahu překladu – Translator Text API
titlesuffix: Azure Cognitive Services
description: Zakázat posunutí obsahu s Translator Text API.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: v-jansko
ms.openlocfilehash: b012201bcb9e0aece3d7e1ddd4f771643088af76
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52683375"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Jak zabránit překlad obsahu s rozhraním Translator Text API

Translator Text API vám umožní obsah značky tak, aby není přeložen. Můžete například označit kód, název značky nebo slovo nebo fráze, který nemá smysl při lokalizována. 

## <a name="methods-for-preventing-translation"></a>Metody jak zabránit překladu
1. Řídicí klíčové slovo Twitteru @somethingtopassthrough nebo #somethingtopassthrough. Zrušit řídicí po převodu.

2. Obsah s `notranslate`.

   Příklad:

   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. Použití [dynamický slovník](dynamic-dictionary.md) stanovit konkrétní překladu.

4. Nepředávejte řetězec rozhraní Translator Text API pro překlad.

5. Vlastní Translator: Použít [slovníku v vlastní Translator](custom-translator/what-is-dictionary.md) stanovit překladu věty se 100 % pravděpodobností.


## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Vyhněte se překlad do volání rozhraní Translator API](reference/v3-0-translate.md)
