---
title: Fráze seznamy – hlasové služby
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak zadat hlasové služby pomocí seznamu frází `PhraseListGrammar` objekt zlepšit výsledky rozpoznávání řeči na text.
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: rhurey
ms.openlocfilehash: 158819d481c0ce245539d783f194b159b2b9edb6
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606525"
---
# <a name="phrase-lists-for-speech-to-text"></a>Fráze seznamy pro převod řeči na text

Poskytování hlasové služby se seznamem frázi, můžete zlepšit přesnost rozpoznávání řeči. Fráze seznamy se používají k identifikaci známé fráze v zvukových dat, jako je jméno uživatele nebo na konkrétní místo.

Například pokud máte příkaz "Přejít na" a je to možné cíl "Pře", který může být používán, můžete přidat položku "Přesunout na dál". Přidání frázi zvýší pravděpodobnost, když zvuk rozpozná, že bude rozpoznán "Přesunout na dál" místo "Přesun směrem k".

Jednotlivá slova nebo fráze kompletní lze přidat do seznamu frázi. Při rozpoznávání položky v seznamu frázi slouží přesná shoda je zahrnuta ve zvukovém souboru. Stavíme na předchozí příklad, pokud seznam frázi obsahuje "Přesunout na dál" a frází zachycené je "Přesunout pomalu směrem k", pak bude výsledek rozpoznání "Přejděte dál pomalu".

## <a name="how-to-use-phrase-lists"></a>Jak používat seznamy fráze

Následující ukázky ukazují, jak sestavit seznam frází pomocí `PhraseListGrammar` objektu.

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
> Maximální počet frázi seznamy, které bude služba Speech používat tak, aby odpovídaly řeči je 1024 frází.

Můžete také zrušit fráze související s `PhraseListGrammar` ve volání funkce clear().

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
> Změny `PhraseListGrammar` objektu vzít vliv na další rozpoznávání nebo po opětovné připojení ke službám řeči.

## <a name="next-steps"></a>Další postup

* [Referenční dokumentace sady SDK řeči](speech-sdk.md)
