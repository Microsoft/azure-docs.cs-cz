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
ms.date: 11/18/2019
ms.author: swmachan
ms.openlocfilehash: dd3684cbd7c03851bfcc75293a9690f77b4652b2
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184815"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Jak zabránit překladu obsahu pomocí Translator Text API

Translator Text API umožňuje označit obsah tak, aby nebyl přeložen. Například můžete chtít označit kód, název značky nebo slovo nebo frázi, které nedává smysl, pokud je lokalizováno.

## <a name="methods-for-preventing-translation"></a>Metody prevence překladu
1. Řídicí znak na Twitteru @somethingtopassthrough nebo #somethingtopassthrough. Zrušit řídicí znak po překladu. Toto je regulární výraz pro platné značky Twitteru: `\B@[A-Za-z]+[A-Za-z0-9_]+)`. Značka by měla začínat znakem "@", následovaným znakem a potom následováním jednoho nebo více znaků, číslic nebo podtržítka. Doporučuje se, aby značky byly krátké a před otevírací značkou musí být mezera.

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
