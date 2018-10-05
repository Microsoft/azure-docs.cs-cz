---
title: O převodu řeči na Text
description: Přehled možností Speech to Text API.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 456b60ee44f3e47bc04da341fd0e3df86662d16b
ms.sourcegitcommit: 4edf9354a00bb63082c3b844b979165b64f46286
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2018
ms.locfileid: "48785439"
---
# <a name="about-the-speech-to-text-api"></a>O Speech to Text API

**Převod řeči na Text** API *transcribes* příkaz audiostreamy do textu, který vaše aplikace může zobrazit uživateli nebo adekvátně jako vstup. Rozhraní API je možné buď pomocí klientské knihovny SDK (pro podporované platformy a jazyky) nebo rozhraní REST API.

**Převod řeči na Text** API nabízí následující funkce:

- Pokročilou technologii rozpoznávání řeči od Microsoftu, stejně používají Cortana, Office a další produkty společnosti Microsoft.

- V reálném čase průběžné rozpoznávání. **Převod řeči na Text** umožňuje uživatelům přepisy zvuku na text v reálném čase. Podporuje také přijímají mezilehlých výsledků slov, která zatím byly rozpoznány. Služba automaticky rozpozná konec řeči. Uživatelé mohou také další možnosti formátování, včetně malá a velká písmena a interpunkční znaménka, maskování vulgárních výrazů a normalizace inverzní textu.

- Výsledky vrácené v Lexical a zobrazení formulářů (lexikální výsledky, naleznete v tématu DetailedSpeechRecognitionResult příklady nebo rozhraní API).

- Podporu mnoha jazyků mluvené slovo a dialekty. Úplný seznam podporovaných jazyků v jednotlivých režimech rozpoznávání najdete v tématu [podporované jazyky](language-support.md#speech-to-text).

- Jazyk a akustických modelů, přizpůsobit, takže můžete přizpůsobit aplikaci do slovníku specializované domény vašich uživatelů, mluvený prostředí a způsob, jak mluvit.

- Rozpoznávání přirozeného jazyka. Díky integraci se sadou [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS), které lze odvodit záměry a entity z řeči. Uživatelé nemusí vědět slovník vaší aplikace, ale můžete popsat, co chtějí ve své vlastní slova.

- Skóre spolehlivosti se vrátí ze služby, pokud zadáte podrobný výstup na objekt konfigurace řeči (SpeechConfig.OutputFormat vlastnost). Pak můžete použít některé z metod Best() na výsledek nebo získat skóre přímo z JSON vrácený službou (něco jako výsledek. Properties.GetProperty(PropertyId.SpeechServiceResponse_JsonResult)).

## <a name="api-capabilities"></a>Funkce rozhraní API

Některé možnosti **převod řeči na Text** rozhraní API – zejména v oblasti Vlastní nastavení – jsou k dispozici prostřednictvím REST. V následující tabulce najdete souhrn možností jednotlivých metod přístupu k rozhraní API. 

| Případ použití | REST | Sady SDK |
|-----|-----|-----|----|
| Přepisy krátký utterance, například příkaz (s délkou < 15); žádná prozatímní výsledky | Ano | Ano |
| Přepisy delší utterance (> 15 s) | Ne | Ano |
| Přepisy streamování zvuku s volitelné dočasné výsledky | Ne | Ano |
| Vysvětlení mluvčího záměry prostřednictvím služby LUIS | Ne\* | Ano |
| Vytvořit testy přesnosti | Ano | Ne |
| Nahrání datových sad pro přizpůsobení modelu | Ano | Ne |
| Vytvoření a správa modely řeči | Ano | Ne |
| Vytvoření a správa model nasazení | Ano | Ne |
| Správa předplatných | Ano | Ne |
| Vytvoření a správa model nasazení | Ano | Ne |
| Vytvoření a správa model nasazení | Ano | Ne |

> [!NOTE]
> Rozhraní REST API implementuje tohoto omezení požadavků rozhraní API 25 na 5 sekund. Zpráva hearders informuje omezení

\* *Služba LUIS záměry a entity, může být odvozena pomocí samostatné předplatné služby LUIS. S tímto předplatným sady SDK můžete zavolat LUIS a poskytnout výsledky entit a účel, jakož i řeči přepisů. Pomocí rozhraní REST API, můžete volat LUIS sami sebe k odvození záměry a entity ve vašem předplatném služby LUIS.*

## <a name="next-steps"></a>Další postup

* [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Rychlý start: rozpoznávat řeč v jazyce C#](quickstart-csharp-dotnet-windows.md)
* [Zjistěte, jak rozpoznat záměry z řeči v jazyce C#](how-to-recognize-intents-from-speech-csharp.md)
