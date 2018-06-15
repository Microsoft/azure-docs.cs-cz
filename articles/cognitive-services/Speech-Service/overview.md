---
title: Co je služba rozpoznávání řeči (preview)? | Dokumenty Microsoft
description: 'Služba rozpoznávání řeči, součástí kognitivní služby společnosti Microsoft, jednotek několik služeb Azure rozpoznávání řeči, které byly dřív dostupné samostatně: Bing řeči (které zahrnuje rozpoznávání řeči a převod textu na řeč), vlastní řeči a řeči překlad.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: e7c09eee1634c52e78a523a7cc65641ea99f23e6
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "35343940"
---
# <a name="what-is-the-speech-service-preview"></a>Co je služba rozpoznávání řeči (preview)?

Služba rozpoznávání řeči, součástí kognitivní služby společnosti Microsoft, jednotek několik služeb Azure rozpoznávání řeči, které byly dřív dostupné samostatně: Bing řeči (které zahrnuje rozpoznávání řeči a převod textu na řeč), vlastní řeči a řeči překlad. Jako jeho prekurzorů řeči služba používá technologii technologií používaných v další produkty společnosti Microsoft, včetně Cortana a Microsoft Office.

> [!NOTE]
> Rozpoznávání řeči služba je aktuálně ve verzi public preview. Vraťte se sem pravidelně aktualizací k dokumentaci, ukázky další kódu a další.

Jednotná řeči služby s jedno předplatné, poskytuje vývojářům snadný způsob, jak poskytnout funkce výkonné rozpoznávání řeči své aplikace. Aplikace můžete nyní funkce hlasových příkazů, přepis, diktování, řeči souhrnnou a překlad.

|Funkce|Popis|
|-|-|
|Převod řeči na text|Průběžné lidského hlasu převede na text, který lze použít jako vstup do vaší aplikace. Umožňuje integraci s [znalosti jazyka služby](https://docs.microsoft.com/azure/cognitive-services/luis/) (LEOŠ) k odvozování z utterances záměru uživatele.|
|Převod textu na řeč|Převede text na zvukové soubory přirozený zní řečového.|
|Rozpoznávání řeči&nbsp;překlad|Poskytněte překlady převod řeči na jiných jazyků, text nebo řečovém výstupu.|

## <a name="using-the-speech-service"></a>Pomocí služby řeči

Služba rozpoznávání řeči je k dispozici dvěma způsoby. [Sada SDK](speech-sdk.md) abstrahuje rychle podrobnosti síťových protokolů. [REST API](rest-apis.md) pracuje s žádný programovací jazyk, ale neposkytuje všechny funkce, které nabízí sadu SDK.

|<br>Metoda|Řeč<br>na Text|Text, který se<br>Řeč|Řeč<br>Překlad|<br>Popis|
|-|-|-|-|-|
|[Sady SDK](speech-sdk.md)|Ano|Ne|Ano|Knihovny pro konkrétní programovací jazyky, které zjednodušují vývoj.|
|[REST](rest-apis.md)|Ano|Ano|Ne|Jednoduché založené na protokolu HTTP rozhraní API umožňující snadné přidání řeči do vaší aplikace.|

## <a name="speech-to-text"></a>Převod řeči na text

[Řeči na Text](speech-to-text.md) (STT), nebo rozpoznávání řeči, rozhraní API transcribes zvukových datových proudů do textu, který vaše aplikace může přijmout jako vstup. Aplikace můžete pak, například zadejte text do dokumentu nebo pracovat jako příkaz.

Převod řeči na Text samostatně optimalizovaná pro interaktivní, konverzace a diktování scénáře. Tady uvádíme běžné případy použití pro převod řeči na Text rozhraní API. 

* Rozpoznat stručné utterance, třeba příkaz, bez dočasné výsledky
* Transcribe dlouhý, dříve zaznamenaných utterance, jako je například zprávu hlasová pošta
* Transcribe streamování řeči v reálném čase s částečné výsledky pro diktování
* Zjistit, co uživatelé chtějí provést podle žádost mluvené přirozeného jazyka

Převod řeči na Text rozhraní API podporuje interaktivní řeči přepis s v reálném čase průběžné rozpoznávání a průběžných výsledky. Podporuje také end řeči detekce, volitelné automaticky velká písmena a interpunkční znaménka, vulgárnost maskování a normalizaci text.

Můžete přizpůsobit rozpoznávání řeči na Text akustickými a jazyk modely, aby specializované terminologie, aktivní prostředí a různé způsoby hovořícího.

## <a name="text-to-speech"></a>Převod textu na řeč

[Převod textu na řeč](text-to-speech.md) (převod textu na ŘEČ), nebo řeči slučování jsou rozhraní API převede prostého textu na řeč přirozený zní, doručit do vaší aplikace v zvukový soubor. Více hlasy, různých pohlaví nebo zvýraznění, jsou k dispozici mnoho podporovaných jazyků.

Rozhraní API podporuje značky řeči souhrnnou Markup Language (SSML), takže můžete zadat přesný výslovnosti výslovnosti pro problémových slova. SSML může také indikovat charakteristiky rozpoznávání řeči (včetně zvýraznění, rychlost, svazek, pohlaví a výšce) přímo do textu.

Tady uvádíme běžné případy použití pro převod textu na řeč rozhraní API.

* Řečovém výstupu jako alternativní obrazovky výstupu uživatelům se zrakovým postižením
* Hlas na výzvy pro aplikace v automobilu, jako například navigace
* Konverzačního uživatelského rozhraní v souladu s převod řeči na Text rozhraní API

Pokud potřebujete nepodporované dialekt nebo jenom chcete jedinečný hlasu pro vaši aplikaci, převod textu na řeč rozhraní API podporuje vlastní hlasové modely.

## <a name="speech-translation"></a>Překlad řeči

[Řeči překlad](speech-translation.md) rozhraní API je možné přeložit datový proud zvuku v téměř v reálném čase nebo ke zpracování zaznamenaná řeči. Při překladu streamování, vrátí službu dočasné výsledky, které lze zobrazit pro uživatele k určení průběhu překlad. Výsledky mohou být vráceny jako text, nebo jako hlasu.

Případy použití pro překlad řeči patří.

* Pro zaměstnanci služebních implementovat "konverzačního" Překlad mobilní aplikace nebo zařízení 
* Zadejte automatické překladů pro titulkování záznamů audio a video

## <a name="speech-devices-sdk"></a>Speech Devices SDK

Se zavedením jednotné řeči služby společnosti Microsoft a příslušní partneři nabízí platformu integrované hardware a software pro optimalizovaná pro vývoj zařízení s podporou rozpoznávání řeči: [řeči zařízení SDK](speech-devices-sdk.md). Tato sada SDK je vhodný pro vývoj inteligentní řeči zařízení pro všechny typy aplikací.

Sady SDK zařízení řeči umožňuje vytvářet vlastní vedlejším zařízení slovem přizpůsobené probuzení, tak, aby upozornění, která aktivuje zvuku jsou jedinečné pro vaší značkou. Nabízí taky nadřízená zvuk zpracování z několika kanál zdrojů pro přesnější rozpoznávání řeči, včetně potlačení šumu, hlasové úplně pole a beamforming.

## <a name="next-steps"></a>Další postup

Získáte klíč bezplatné předplatné zkušební verze pro rozpoznávání řeči služby.

> [!div class="nextstepaction"]
> [Vyzkoušení služby řeči zdarma](get-started.md)
