---
title: Seznamy frází - služba řeči
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak službě Řeči dodat seznam `PhraseListGrammar` frází pomocí objektu ke zlepšení výsledků rozpoznávání řeči na text.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 5a21358edae4c61f35993770c22634da9ac83633
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401969"
---
# <a name="phrase-lists-for-speech-to-text"></a>Seznamy frází pro převod řeči na text

Poskytnutím funkce Řeč se seznamem frází můžete zlepšit přesnost rozpoznávání řeči. Seznamy frází se používají k identifikaci známých frází ve zvukových datech, jako je jméno osoby nebo konkrétní umístění.

Pokud máte například příkaz "Přesunout do" a možný cíl "Ward", který může být vysloven, můžete přidat položku "Přesunout do sboru". Přidání fráze zvýší pravděpodobnost, že když je zvuk rozpoznán, že "Přesunout do Ward" bude rozpoznán místo "Přesunout směrem".

Do seznamu frází lze přidat jednotlivá slova nebo úplné fráze. Během rozpoznávání se položka v seznamu frází používá, pokud je do zvuku zahrnuta přesná shoda pro celou frázi jako samostatná fráze. Pokud není nalezena přesná shoda s frází, rozpoznávání není podporováno.

>[!Note]
> V současné době funkce Seznamy frází podporuje pouze angličtinu pro převod řeči na text.

## <a name="how-to-use-phrase-lists"></a>Jak používat seznamy frází

Ukázky níže ilustrují, jak `PhraseListGrammar` vytvořit seznam frází pomocí objektu.

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

::: zone pivot="programming-language-more"

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

>[!Note]
> Maximální počet seznamů frází, které bude služba Speech používat k odpovídání řeči, je 1024 frází.

Můžete také vymazat fráze spojené `PhraseListGrammar` s voláním clear().

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

::: zone pivot="programming-language-more"

```JavaScript
phraseListGrammar.clear();
```

::: zone-end

> [!NOTE]
> Změny objektu `PhraseListGrammar` se projeví při dalším rozpoznávání nebo po opětovném připojení ke službě Speech.

## <a name="next-steps"></a>Další kroky

* [Referenční dokumentace sady Speech SDK](speech-sdk.md)
