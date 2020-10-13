---
title: Seznamy frází – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Naučte se poskytovat hlasovou službu pomocí seznamu frází pomocí `PhraseListGrammar` objektu pro zlepšení výsledků rozpoznávání řeči na text.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: trbye
zone_pivot_groups: programming-languages-speech-services-one-nomore-no-go
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 1e0b345eb4f1d6b3ab8ba917794b6878180ac558
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91320944"
---
# <a name="phrase-lists-for-speech-to-text"></a>Seznamy frází pro převod řeči na text

Zadáním služby Speech Service se seznamem frází můžete zlepšit přesnost rozpoznávání řeči. Seznamy frází slouží k identifikaci známých frází ve zvukových datech, jako je jméno osoby nebo konkrétní umístění.

Příklad: Pokud máte příkaz "přesunout do" a možné místo cíle "", které je možné přehlasovat, můžete přidat položku "přesunout do" dál ". Přidáním fráze dojde k nárůstu pravděpodobnosti, že při rozpoznání zvuku bude místo možnosti přesunout směrem nahoru rozpoznáno "Přesun na".

Do seznamu frází lze přidat jednotlivá slova nebo kompletní fráze. Při rozpoznávání se používá záznam v seznamu frází, pokud je přesná shoda pro celou frázi zahrnutá jako samostatná fráze. Pokud se nenalezne přesná shoda se slovem, rozpoznávání vám nepomáhá.

>[!Note]
> V současné době seznamy frází podporují pouze angličtinu pro převod řeči na text.

## <a name="how-to-use-phrase-lists"></a>Používání seznamů frází

Následující ukázky ukazují, jak vytvořit seznam frází pomocí `PhraseListGrammar` objektu.

::: zone pivot="programming-language-csharp"

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

::: zone-end

::: zone pivot="programming-language-javascript"

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

>[!Note]
> Maximální počet frází seznamů, které bude služba řeči používat pro porovnávání řeči, je 1024 frází.

Můžete také vymazat fráze spojené s `PhraseListGrammar` voláním Clear ().

::: zone pivot="programming-language-csharp"

```cs
phraseList.Clear();
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
phraselist->Clear();
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
phraseListGrammar.clear();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar.clear()
```

::: zone-end

::: zone pivot="programming-language-javascript"

```JavaScript
phraseListGrammar.clear();
```

::: zone-end

> [!NOTE]
> Změny objektu se projeví `PhraseListGrammar` při příštím rozpoznávání nebo po opětovném připojení ke službě Speech.

## <a name="next-steps"></a>Další kroky

* [Referenční dokumentace sady Speech SDK](speech-sdk.md)
