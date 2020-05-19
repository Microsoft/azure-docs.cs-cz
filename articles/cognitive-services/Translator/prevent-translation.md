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
ms.date: 03/20/2020
ms.author: swmachan
ms.openlocfilehash: 33939976a0824ce8afeb2e6f6fb19e7033098683
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592691"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>Jak zabránit překladu obsahu pomocí překladatele

Překladatel umožňuje označit obsah tak, aby nebyl přeložen. Například můžete chtít označit kód, název značky nebo slovo nebo frázi, které nedává smysl, pokud je lokalizováno.

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

2. Pomocí [dynamického slovníku](dynamic-dictionary.md) nařídíte konkrétní překlad.

3. Nepředávejte řetězec překladateli pro překlad.

4. Vlastní Překladatel: pomocí [slovníku ve vlastním překladateli](custom-translator/what-is-dictionary.md) můžete naepsat převod fráze s pravděpodobností 100%.


## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Zamezení překladu ve volání překladatele](reference/v3-0-translate.md)
