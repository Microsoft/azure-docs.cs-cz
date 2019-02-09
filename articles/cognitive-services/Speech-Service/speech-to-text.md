---
title: O převodu řeči na Text – hlasové služby
titleSuffix: Azure Cognitive Services
description: Rozhraní Speech to Text API transcribes audiostreamy na text, který vaše aplikace může zobrazit nebo s nimi pracovat jako vstup. Tato služba je k dispozici prostřednictvím sady SDK a koncový bod RESTful.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 5012245a79295f1e05079f6c0a368ac832b8974a
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55978574"
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

Některé z možností **převod řeči na Text** rozhraní API, zejména v oblasti Vlastní nastavení, jsou k dispozici prostřednictvím REST. V následující tabulce najdete souhrn možností jednotlivých metod přístupu k rozhraní API. Úplný seznam možností a podrobnosti o rozhraní API najdete v tématu [Swagger odkaz](https://westus.cris.ai/swagger/ui/index).

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
> Rozhraní REST API implementuje tohoto omezení požadavků rozhraní API 25 na 5 sekund. Záhlaví zpráv bude informovat omezení

\* *Služba LUIS záměry a entity, může být odvozena pomocí samostatné předplatné služby LUIS. S tímto předplatným sady SDK můžete zavolat LUIS a poskytnout výsledky entit a účel, jakož i řeči přepisů. Pomocí rozhraní REST API, můžete volat LUIS sami sebe k odvození záměry a entity ve vašem předplatném služby LUIS.*

## <a name="next-steps"></a>Další postup

* [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Rychlý start: rozpoznávat řeč v jazyce C#](quickstart-csharp-dotnet-windows.md)
* [Zjistěte, jak rozpoznat záměry z řeči v jazyce C#](how-to-recognize-intents-from-speech-csharp.md)
