---
title: Seznamy frází – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Naučte se poskytovat hlasové služby pomocí seznamu frází pomocí `PhraseListGrammar` objektu pro zlepšení výsledků rozpoznávání řeči na text.
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: rhurey
ms.openlocfilehash: 0e552d502184d1b537263c2c1f6b2a8562cdf791
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562775"
---
# <a name="phrase-lists-for-speech-to-text"></a>Seznamy frází pro převod řeči na text

Když poskytnete hlasové služby se seznamem frází, můžete zlepšit přesnost rozpoznávání řeči. Seznamy frází slouží k identifikaci známých frází ve zvukových datech, jako je jméno osoby nebo konkrétní umístění.

Příklad: Pokud máte příkaz "přesunout do" a možné místo cíle "", které je možné přehlasovat, můžete přidat položku "přesunout do" dál ". Přidáním fráze dojde k nárůstu pravděpodobnosti, že při rozpoznání zvuku bude místo možnosti přesunout směrem nahoru rozpoznáno "Přesun na".

Do seznamu frází lze přidat jednotlivá slova nebo kompletní fráze. Při rozpoznávání se používá záznam v seznamu frází, pokud je do zvuku vložena přesná shoda. V předchozím příkladu, pokud seznam frází obsahuje "přesunout na další", a zaznamenaná fráze je "přesunout směrem nahoru", výsledek rozpoznávání bude "Přesun do nejbližšího zpomalit".

>[!Note]
> V současné době seznamy frází podporují pouze angličtinu pro převod řeči na text.

## <a name="how-to-use-phrase-lists"></a>Používání seznamů frází

Následující ukázky ukazují, jak vytvořit seznam frází pomocí `PhraseListGrammar` objektu.

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

>[!Note]
> Maximální počet frází seznamů, které bude služba řeči používat pro porovnávání řeči, je 1024 frází.

Můžete také vymazat fráze spojené s `PhraseListGrammar` voláním Clear ().

```C++
phraselist->Clear();
```

```cs
phraseList.Clear();
```

```Python
phrase_list_grammar.clear()
```

```JavaScript
phraseListGrammar.clear();
```

```Java
phraseListGrammar.clear();
```

> [!NOTE]
> `PhraseListGrammar` Změny objektu se projeví při dalším rozpoznávání nebo po opětovném připojení ke službám řeči.

## <a name="next-steps"></a>Další kroky

* [Referenční dokumentace sady Speech SDK](speech-sdk.md)
