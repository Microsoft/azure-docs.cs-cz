---
title: Převod textu na řeč se službami Azure řeči
titleSuffix: Azure Cognitive Services
description: Převod textu na řeč z hlasových služeb Azure je služba, která umožňuje aplikacím, nástroje nebo zařízení pro převod textu na fyzické řečového podobnou té lidské. Zvolte standardní a neuronových sítí hlasů nebo vytvořte vlastní vlastní hlasové jedinečné produkty nebo značky. 75 standardní hlasy jsou k dispozici ve více než 45 jazyků a národních prostředí a 5 neuronových sítí hlasy jsou k dispozici v 4 jazyky a národní prostředí.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 2c5c2bc2b8dd1930efef9833bd442fcad5566e2a
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578777"
---
# <a name="what-is-text-to-speech"></a>Co je převod textu na řeč?

Převod textu na řeč z hlasových služeb Azure je služba, která umožňuje aplikacím, nástroje nebo zařízení pro převod textu na fyzické řečového podobnou té lidské. Zvolte standardní a neuronových sítí hlasů nebo vytvořte vlastní vlastní hlasové jedinečné produkty nebo značky. 75 standardní hlasy jsou k dispozici ve více než 45 jazyků a národních prostředí a 5 neuronových sítí hlasy jsou k dispozici v 4 jazyky a národní prostředí. Úplný seznam najdete v tématu [podporované jazyky](language-support.md#text-to-speech).

Převod textu na řeč technologie umožňuje tvůrce obsahu pro interakci se svým uživatelům různými způsoby. Převod textu na řeč lze vylepšit dostupnost zároveň uživatelům poskytují možnost pracovat s obsahem zvukově. Zda uživatel má zrakově learning postižení, nebo vyžaduje informace o navigaci a zajistit, převod textu na řeč vylepšit stávající zkušenosti. Převod textu na řeč je také užitečné doplněk pro hlasové roboty a virtuálních asistentů.

### <a name="standard-voices"></a>Standardní hlasů

Standardní hlasy jsou vytvořeny pomocí statistické ukazatelů syntézu a/nebo zřetězení syntézu technik. Tyto hlasy jsou vysoce srozumitelné a zvukové poměrně fyzické. Můžete snadno povolit aplikacím řeči ve více než 45 jazyků s širokou škálu možností hlasu. Tyto hlasy poskytují vysokou výslovnost přesnost, včetně podpory pro zkratky, rozšíření zkratka, interpretace datum a čas, polyphones a další. Standardní hlas používejte ke zlepšení dostupnosti aplikací a služeb tím, že uživatelé interagují s vaším obsahem zvukově.

### <a name="neural-voices"></a>Neurální hlasů

Neurální hlasy překonání omezení tradičních systémů převod textu na řeč v odpovídající vzory zátěže a intonací mluvený jazyk a syntetizační jednotky řeči do počítače hlasový vstup pomocí hluboké neuronové sítě. Standardní převod textu na řeč boří prosody do samostatných pro jazykovou analýzu a akustický předpovědi kroky, které se řídí nezávislé modely. Výsledkem může být syntézu muffled, buzzy hlasu. Naše neuronových sítí funkce nemá prosody předpovědi a způsobu vyjadřování syntézu současně, povede k více plynulá práce a přirozeného to nezní hlasový vstup.

Neurální hlasy slouží k interakce s chatovacích a virtuálních asistentů přirozenější a poutavější převést digitální texty např. e knihy audiobooks a vylepšit systémy navigaci. Díky přirozené prosody podobnou té lidské a jasné rozdělení slov Neurální hlasy výrazně snížit naslouchání reorganizovaly při interakci s AI systémy. Další informace o neuronových sítí hlasů, naleznete v tématu [podporované jazyky](language-support.md#text-to-speech).

Další informace o výhodách neuronových sítí hlasů, najdete v článku [společnosti Microsoft nový převod textu na řeč neuronových sítí service pomáhá počítače řeči, jako jsou lidé](https://azure.microsoft.com/blog/microsoft-s-new-neural-text-to-speech-service-helps-machines-speak-like-people/).

### <a name="custom-voices"></a>Vlastní hlasů

Přizpůsobení hlasové umožňuje vytvářet rozpoznat, jeden z druhu hlas pro hodnotu značky. Pokud chcete vytvořit vlastní hlasové písmo, provedete studio nahrávání a nahrát přidružené skripty jako trénovací data. Služba potom vytvoří jedinečný hlas modelu, která je vyladěná pro nahrávání. Toto vlastní hlasové písmo můžete použít tak, aby odpovídaly řeči. Další informace najdete v tématu [vlastní hlasy](how-to-customize-voice-font.md).

## <a name="core-features"></a>Základní funkce

Tato tabulka uvádí základní funkce pro převod textu na řeč:

| Případ použití | Sada SDK | REST |
|----------|-----|------|
| Převod textu na řeč. | Ano | Ano |
| Nahrání datových sad pro přizpůsobení hlasu. | Ne | Ano\* |
| Vytvoření a správa modelů hlasového písma. | Ne | Ano\* |
| Vytvářejte a spravujte nasazení hlasového písma. | Ne | Ano\* |
| Vytvoření a správa testů hlasového písma. | Ne | Ano\* |
| Spravujte předplatná. | Ne | Ano\* |

\* *Tyto služby jsou k dispozici prostřednictvím cris.ai koncového bodu. Zobrazit [Swagger odkaz](https://westus.cris.ai/swagger/ui/index). Tyto vlastní hlasové školení a rozhraní API pro správu implementovat omezení šířky pásma, omezení požadavků na 25 na 5 sekund, zatímco syntézu řeči samotné rozhraní API implementuje omezení, která umožňuje 200 požadavků za sekundu jako nejvyšší. Pokud dojde k omezení využití sítě, zobrazí se oznámení prostřednictvím záhlaví zpráv.*

## <a name="get-started-with-text-to-speech"></a>Začínáme s převod textu na řeč

Nabízíme rychlí průvodci navržená tak, aby se spouštěním kódu za méně než 10 minut. Tato tabulka obsahuje seznam převod textu na řeč rychlých startů uspořádané podle jazyka.

### <a name="sdk-quickstarts"></a>Rychlé starty SDK

| Rychlý start (SDK) | Platforma | API – referenční informace |
|------------|----------|---------------|
| [C#, .NET Framework](quickstart-text-to-speech-dotnet-windows.md) | Windows | [Procházet](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-text-to-speech-cpp-windows.md) | Windows | [Procházet](https://aka.ms/csspeech/cppref) |
| [C++](quickstart-text-to-speech-cpp-linux.md) | Linux | [Procházet](https://aka.ms/csspeech/cppref) |

### <a name="rest-quickstarts"></a>Rychlé starty REST

| Rychlý start (REST) | Platforma | API – referenční informace |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-dotnet-text-to-speech.md) | Windows, macOS, Linux | [Procházet](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Node.js](quickstart-nodejs-text-to-speech.md) | Okno, macOS, Linux | [Procházet](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Python](quickstart-python-text-to-speech.md) | Okno, macOS, Linux | [Procházet](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |

## <a name="sample-code"></a>Ukázka kódu

Ukázkový kód pro převod textu na řeč je k dispozici na Githubu. Tyto ukázky zahrnují převod textu na řeč převod v nejoblíbenějších programovacích jazyků.

* [Převod textu na řeč ukázky (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Převod textu na řeč ukázky (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Referenční dokumenty

* [Speech SDK](speech-sdk-reference.md)
* [Rozpoznávání řeči zařízení SDK](speech-devices-sdk.md)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: Text-to-speech](rest-text-to-speech.md)
* [REST API: Přepis služby batch a přizpůsobení](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Další postup

* [Pořiďte si předplatné služby zdarma řeči](get-started.md)
* [Vytvoření vlastního hlasového písma](how-to-customize-voice-font.md)
