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
ms.date: 5/02/2019
ms.author: rhurey
ms.openlocfilehash: 576d3c4a70c8870a31bc352b9f7723d2c2e69854
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025802"
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
> Maximální počet frázi seznamy, které bude služba Speech používat tak, aby odpovídaly řeči je 1024.

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
