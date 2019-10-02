---
title: Služba pro převod textu na řeč
titleSuffix: Azure Cognitive Services
description: Funkce převodu textu na řeč ve službě Speech je funkce, která umožňuje vašim aplikacím, nástrojům a zařízením převádět text na syntetizované rozpoznávání řeči na přirozeném člověku. Vyberte si ze standardních nebo neuronové hlasů nebo si vytvořte vlastní hlas jedinečný pro svůj produkt nebo značku. 75 a standardní hlasy jsou k dispozici ve více než 45 jazycích a národních prostředích a 5 neuronové hlasy jsou k dispozici ve 4 jazycích a národních prostředích.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: erhopf
ms.openlocfilehash: d12b952d298b41c8d06f0fcac141a45749de9051
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71799946"
---
# <a name="what-is-text-to-speech"></a>Co je převod textu na řeč?

Převod textu na řeč z Azure Speech Services je služba, která umožňuje vašim aplikacím, nástrojům a zařízením převádět text na přirozený řeč, který se na něj člověk líbí. Vyberte si ze standardních nebo neuronové hlasů nebo si vytvořte vlastní hlas jedinečný pro svůj produkt nebo značku. 75 a standardní hlasy jsou k dispozici ve více než 45 jazycích a národních prostředích a 5 neuronové hlasy jsou k dispozici ve 4 jazycích a národních prostředích. Úplný seznam najdete v tématu [podporované jazyky](language-support.md#text-to-speech).

Technologie převodu textu na řeč umožňuje autorům obsahu pracovat různými způsoby s uživateli. Převod textu na řeč může zlepšit přístupnost tím, že poskytuje uživatelům možnost pracovat s obsahem audibly. Bez ohledu na to, jestli má uživatel vizuální poškození, zdravotní postižení, nebo vyžaduje navigační informace, ale při jízdě, může převod textu na řeč zlepšit stávající prostředí. Převod textu na řeč je také cenným doplňkem pro hlasové roboty a virtuální asistenty.


Když využijete SSML (Speech syntézy Language), jazyk značek založený na jazyce XML, můžou vývojáři, kteří používají službu Převod textu na řeč, určit, jak se vstupní text převede na syntetizované řeč. Pomocí SSML můžete upravovat rozteč, výslovnost, míru řeči, objem a další. Další informace najdete v tématu [SSML](#speech-synthesis-markup-language-ssml).

### <a name="standard-voices"></a>Standardní hlasy

Standardní hlasy jsou vytvářeny pomocí metod syntézy statistických ukazatelů a/nebo zřetězení. Tyto hlasy jsou vysoce srozumitelnější a zdravé přirozené. Můžete snadno povolit vašim aplikacím mluvit ve více než 45 jazycích, a to s využitím široké škály možností hlasu. Tyto hlasy poskytují přesnost na vysokou výslovnost, včetně podpory zkratek, rozšíření akronymů, výkladů data a času, telefonů a dalších. Pomocí standardního hlasu Vylepšete přístupnost vašim aplikacím a službám tím, že uživatelům umožníte pracovat s audibly obsahu.

### <a name="neural-voices"></a>Hlasy neuronové

Hlasy neuronové používají neuronové sítě k překonání omezení tradičních systémů pro převod textu na mluvené slovo v porovnání se vzory zátěže a nevýbušného jazyka v mluveném jazyce a při syntetizování jednotek řeči do hlasu v počítači. Standardní převod textu na řeč rozprosody na samostatné jazykové analýzy a kroky pro plynulé předpovědi, které se řídí nezávislými modely, což může vést k porušení hlasového Shrnutí. Naše schopnost neuronové provádí současné Prosody předpověď a hlasovou syntézu. Výsledkem je více kapalin a přirozený hlas.

Neuronové hlasy se dají použít k zajištění většího přirozeného a poutavého působení s chatovacích robotů o a virtuálními asistenty, konverzí digitálních textů, jako jsou například e-knihy, do audiobooks a vylepšení navigačních systémů v klecích. V případě přirozeného Prosody jako přirozeného a jasného kloubování slov, neuronové hlasy významně omezují naslouchat únavu při interakci se systémy AI.

Hlasy neuronové podporují různé styly, jako je neutrální a Cheerful. Například hlas Jessa (EN-US) může mluvit cheerfully, který je optimalizovaný pro teplé a příjemné konverzace. Hlasový výstup můžete upravit, jako je Tónová volba, rozteč a rychlost pomocí [jazyka značek syntézy řeči](speech-synthesis-markup.md). Úplný seznam dostupných hlasů najdete v tématu [podporované jazyky](language-support.md#text-to-speech).

Další informace o výhodách neuronové hlasů najdete v článku [Nová služba neuronové (text na řeč) Microsoftu, která pomáhá počítačům mluvit jako lidé](https://azure.microsoft.com/blog/microsoft-s-new-neural-text-to-speech-service-helps-machines-speak-like-people/).

### <a name="custom-voices"></a>Vlastní hlasy

Přizpůsobení hlasu vám umožní vytvořit si rozpoznatelný hlas pro vaši značku. Chcete-li vytvořit vlastní písmo hlasu, provedete záznam Studio a nahrajete přidružené skripty jako školicí data. Služba potom vytvoří jedinečný hlasový model vyladěný na vaše nahrávání. Pomocí tohoto vlastního hlasového písma můžete syntetizovat řeč. Další informace najdete v tématu [vlastní hlasy](how-to-customize-voice-font.md).

## <a name="speech-synthesis-markup-language-ssml"></a>Jazyk SSML (Speech Synthesis Markup Language)

SSML (Speech syntézy Language) je značkovací jazyk založený na jazyce XML, který umožňuje vývojářům určit, jakým způsobem se vstupní text převede na syntetizované rozpoznávání řeči pomocí služby převodu textu na řeč. V porovnání s prostým textem umožňuje SSML vývojářům doladit rozteč, výslovnost, míru řeči, objem a další výstup textu na řeč. Normální interpunkční znaménka, jako je například pozastavení po určité době, nebo použití správné nečinnosti, pokud je věta zakončena znakem otazníku, automaticky zpracována.

Všechny textové vstupy odesílané službě převod textu na řeč musí být strukturované jako SSML. Další informace najdete v tématu [jazyk značek pro rozpoznávání řeči](speech-synthesis-markup.md).

### <a name="pricing-note"></a>Poznámka k ceně

Při použití služby převod textu na řeč se vám bude účtovat každý znak, který se převede na řeč, včetně interpunkce. I když samotný dokument SSML není fakturovatelný, volitelné prvky, které slouží k úpravě způsobu převodu textu na řeč, jako je fonémy a rozteč, se počítají jako Fakturovatelné znaky. Tady je seznam toho, co je Fakturovatelné:

* Text předaný do služby převodu textu na řeč v SSML textu žádosti
* Všechny značky v textovém poli textu žádosti ve formátu SSML s výjimkou značek `<speak>` a `<voice>`
* Písmena, interpunkční znaménka, mezery, tabulátory, značky a všechny prázdné znaky
* Každý bod kódu definovaný v kódování Unicode

Podrobné informace najdete v tématu [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Každý čínský, japonský a korejský znak jazyka se počítá jako dva znaky pro účely fakturace.

## <a name="core-features"></a>Základní funkce

Tato tabulka uvádí základní funkce pro převod textu na řeč:

| Případ použití | SDK | REST |
|----------|-----|------|
| Převod textu na řeč | Ano | Ano |
| Nahrajte datové sady pro přizpůsobení hlasu. | Ne | Ano @ no__t-0 |
| Vytvářejte a spravujte modely hlasových písem. | Ne | Ano @ no__t-0 |
| Vytváření a Správa nasazení hlasových písem | Ne | Ano @ no__t-0 |
| Vytvářejte a spravujte testy hlasových písem. | Ne | Ano @ no__t-0 |
| Správa předplatných. | Ne | Ano @ no__t-0 |

@no__t – 0 *tyto služby jsou k dispozici pomocí koncového bodu CRIS.AI. Viz [odkaz Swagger](https://westus.cris.ai/swagger/ui/index). Tato vlastní rozhraní API pro školení a správu hlasu implementují omezení, které omezuje požadavky na 25 za 5 sekund, zatímco rozhraní API pro syntézu řeči sám implementuje omezení, které umožňuje 200 požadavků za sekundu jako nejvyšší. Když dojde k omezení, budete upozorněni prostřednictvím záhlaví zpráv.*

## <a name="get-started-with-text-to-speech"></a>Začínáme s převodem textu na řeč

Nabízíme rychlé starty, které jsou navržené tak, aby běžely kód za méně než 10 minut. Tato tabulka obsahuje seznam rychlých startů pro převod textu na řeč uspořádaných podle jazyka.

### <a name="sdk-quickstarts"></a>Rychlé starty sady SDK

| Rychlý Start (SDK) | Platforma | Referenční materiály k rozhraním API |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-text-to-speech-dotnetcore.md) | Windows | [Hlíží](https://aka.ms/csspeech/csharpref) |
| [C#.NET Framework](quickstart-text-to-speech-dotnet-windows.md) | Windows | [Hlíží](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](quickstart-text-to-speech-csharp-uwp.md) | Windows | [Hlíží](https://aka.ms/csspeech/csharpref) |
| [C#, Unity](quickstart-text-to-speech-csharp-unity.md) | Windows, Android | [Hlíží](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-text-to-speech-cpp-windows.md) | Windows | [Hlíží](https://aka.ms/csspeech/cppref) |
| [C++](quickstart-text-to-speech-cpp-linux.md) | Linux | [Hlíží](https://aka.ms/csspeech/cppref) |
| [Java](quickstart-text-to-speech-java-jre.md) | Windows, Linux, macOS | [Hlíží](https://aka.ms/csspeech/javaref) |
| [Java](quickstart-text-to-speech-java-android.md) | Android | [Hlíží](https://aka.ms/csspeech/javaref) |
| [Objective-C](quickstart-text-to-speech-objectivec-macos.md) | macOS | [Hlíží](https://aka.ms/csspeech/objectivecref) |
| [Objective-C](quickstart-text-to-speech-objectivec-ios.md) | iOS | [Hlíží](https://aka.ms/csspeech/objectivecref) |
| [Swift](quickstart-text-to-speech-swift-macos.md) | macOS | [Hlíží](https://aka.ms/csspeech/objectivecref) |
| [Swift](quickstart-text-to-speech-swift-ios.md) | iOS | [Hlíží](https://aka.ms/csspeech/objectivecref) |
| [Python](quickstart-text-to-speech-python.md) | Window, Linux, macOS | [Hlíží](https://aka.ms/csspeech/pythonref) |

### <a name="rest-quickstarts"></a>Rychlé starty REST

| Rychlý Start (REST) | Platforma | Referenční materiály k rozhraním API |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-dotnet-text-to-speech.md) | Windows, macOS, Linux | [Hlíží](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Node.js](quickstart-nodejs-text-to-speech.md) | Okno, macOS, Linux | [Hlíží](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Python](quickstart-python-text-to-speech.md) | Okno, macOS, Linux | [Hlíží](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |

## <a name="sample-code"></a>Ukázka kódu

Vzorový kód pro převod textu na řeč je k dispozici na GitHubu. Tyto ukázky překrývají převod textu na řeč v nejoblíbenějších programovacích jazycích.

* [Ukázky převodu textu na řeč (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Ukázky převodu textu na řeč (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Referenční dokumenty

* [Speech SDK](speech-sdk-reference.md)
* [Sada Speech Devices SDK](speech-devices-sdk.md)
* [REST API: Převod řeči na text](rest-speech-to-text.md)
* [REST API: převod textu na řeč](rest-text-to-speech.md)
* [REST API: přepis a přizpůsobení Batch](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Další kroky

* [Získání bezplatného předplatného služby Speech Services](get-started.md)
* [Vytváření vlastních hlasových písem](how-to-customize-voice-font.md)
