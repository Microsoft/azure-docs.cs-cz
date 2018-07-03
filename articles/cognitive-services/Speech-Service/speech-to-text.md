---
title: O převodu řeči na Text | Dokumentace Microsoftu
description: Přehled možností Speech to Text API.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 42552fa09f0a433cf12b92e11531303a0d1bd6bb
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "37346507"
---
# <a name="about-the-speech-to-text-api"></a>O Speech to Text API

**Převod řeči na Text** API *transcribes* příkaz audiostreamy do textu, který vaše aplikace může zobrazit uživateli nebo adekvátně jako vstup. Rozhraní API je možné buď pomocí klientské knihovny SDK (pro podporované platformy a jazyky) nebo rozhraní REST API.

**Převod řeči na Text** API nabízí následující funkce:

- Pokročilou technologii rozpoznávání řeči od Microsoftu, stejně používají Cortana, Office a další produkty společnosti Microsoft.

- V reálném čase průběžné rozpoznávání. **Převod řeči na Text** umožňuje uživatelům přepisy zvuku na text v reálném čase. Podporuje také přijímají mezilehlých výsledků slov, která zatím byly rozpoznány. Služba automaticky rozpozná konec řeči. Uživatelé mohou také další možnosti formátování, včetně malá a velká písmena a interpunkční znaménka, maskování vulgárních výrazů a normalizace inverzní textu.

- Optimalizované **převod řeči na Text** výsledky pro interaktivní, konverzace a scénáře diktování. 

- Podporu mnoha jazyků mluvené slovo a dialekty. Úplný seznam podporovaných jazyků v jednotlivých režimech rozpoznávání najdete v tématu [podporované jazyky](supported-languages.md#speech-to-text).

- Jazyk a akustických modelů, přizpůsobit, takže můžete přizpůsobit aplikaci do slovníku specializované domény vašich uživatelů, mluvený prostředí a způsob, jak mluvit.

- Rozpoznávání přirozeného jazyka. Díky integraci se sadou [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS), které lze odvodit záměry a entity z řeči. Uživatelé nemusí vědět slovník vaší aplikace, ale můžete popsat, co chtějí ve své vlastní slova.

## <a name="api-capabilities"></a>Funkce rozhraní API

Některé funkce **převod řeči na Text** nejsou k dispozici prostřednictvím REST API. V následující tabulce najdete souhrn možností jednotlivých metod přístupu k rozhraní API.

| Případ použití | REST | Sady SDK |
|-----|-----|-----|----|
| Přepisy krátký utterance, například příkaz (s délkou < 15); žádná prozatímní výsledky | Ano | Ano |
| Přepisy delší utterance (> 15 s) | Ne | Ano |
| Přepisy streamování zvuku s volitelné dočasné výsledky | Ne | Ano |
| Vysvětlení mluvčího záměry prostřednictvím služby LUIS | Ne\* | Ano |

\* *Služba LUIS záměry a entity, může být odvozena pomocí samostatné předplatné služby LUIS. S tímto předplatným sady SDK můžete zavolat LUIS a poskytnout výsledky entit a účel, jakož i řeči přepisů. Pomocí rozhraní REST API, můžete volat LUIS sami sebe k odvození záměry a entity ve vašem předplatném služby LUIS.*

## <a name="next-steps"></a>Další postup

* [Získejte zkušební verzi předplatného řeči](https://azure.microsoft.com/try/cognitive-services/)
* [Zjistěte, jak rozpoznávat řeč v jazyce C#](quickstart-csharp-windows.md)
