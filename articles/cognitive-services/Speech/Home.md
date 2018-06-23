---
title: Služba Microsoft řeči | Microsoft Docs
description: Pomocí rozhraní API služby Microsoft řeči přidejte řízené řeči akce do vaší aplikace, včetně v reálném čase interakce s uživateli.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: c041132e992f07e94e4b6669ec7ce174f7c2d0dd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342639"
---
# <a name="microsoft-speech-api-overview"></a>Přehled rozhraní API pro rozpoznávání řeči Microsoft

Rozhraní API založené na cloudu řeči Microsoft poskytuje vývojářům snadný způsob, jak vytvořit výkonné funkce rozpoznávání řeči ve svých aplikacích, jako je ovládací prvek příkaz hlasové, pomocí přirozené řeči konverzace a přepis řeči a diktování uživatelském dialogu. Rozhraní API pro rozpoznávání řeči Microsoft podporuje obě *řeči na Text* a *převod textu na řeč* převod.

- **Převod řeči na Text** rozhraní API lidského hlasu převede na text, který můžete použít jako vstup nebo příkazy k řízení vaší aplikace.
- **Převod textu na řeč** rozhraní API převede text na zvukové datových proudů, které je možné přehrát uživateli vaší aplikace.

## <a name="speech-to-text-speech-recognition"></a>Převod řeči na text (rozpoznávání řeči)

Rozpoznávání řeči Microsoft rozhraní API *transcribes* příkaz zvukových datových proudů do text, který vaše aplikace můžete zobrazit uživateli nebo provedení akce jako vstup. Nabízí dva způsoby pro vývojáře pro přidání do své aplikace řeči: rozhraní REST API **nebo** klientské knihovny založené na protokolu Websocket.

- [REST API](GetStarted/GetStartedREST.md): vývojáři mohou použít HTTP volání ze svých aplikací se službou pro rozpoznávání řeči.
- [Knihovny klienta](GetStarted/GetStartedClientLibraries.md): pokročilé funkce, vývojáři můžou stáhnout Microsoft Speech klientské knihovny a propojit do své aplikace.  Knihovny klienta jsou dostupné na různých platformách (Windows, Android, iOS) pomocí různých jazyků (C#, Java, JavaScript, ObjectiveC). Na rozdíl od rozhraní REST API knihovny klienta využívat získáváním na základě protokolu Websocket.

| Případy použití | [Rozhraní REST API](GetStarted/GetStartedREST.md) | [Knihovny klienta](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Převést prostě mluvené zvuk, například příkazy (audio délka < 15 s) bez dočasné výsledky | Ano | Ano |
| Převést dlouho zvuk (s > 15) | Ne | Ano |
| Zvuk datového proudu s dočasné výsledky potřeby | Ne | Ano |
| Pochopení textu převést z zvuk pomocí LEOŠ | Ne | Ano |

Podle toho, která vývojářům přístup zvolte (rozhraní REST API nebo knihovny klienta), služba Microsoft řeči podporuje následující:

- Pokročilé technologie rozpoznávání řeči od společnosti Microsoft, které používá Cortana, diktování Office, Office překladač a další produkty společnosti Microsoft.
- Průběžné rozpoznávání v reálném čase. Rozpoznávání řeči rozhraní API umožňuje uživatelům transcribe zvuk do textu v reálném čase a podporuje přijímat mezilehlé výsledky slova, která, pokud byly rozpoznány. Služba rozpoznávání řeči také podporuje end řeči detekce. Kromě toho mohli uživatelé vybrat další možnosti formátování, jako je malá a velká písmena a interpunkce, maskování vulgárnost a normalizaci text.
- Podporuje optimalizované výsledky rozpoznávání řeči pro *interaktivní*, *konverzace*, a *diktování* scénáře. Pro scénáře uživatele, které vyžadují vlastní jazyk modely a akustickými modely [vlastní řeči služby](../custom-speech-service/cognitive-services-custom-speech-home.md) vám umožní vytvořit řeči modely, které jsou pro aplikaci a uživatele.
- V několika dialekty podporují mnoho mluvené jazyky. Úplný seznam podporovaných jazyků v jednotlivých režimech rozpoznávání najdete v tématu [jazyky rozpoznávání](api-reference-rest/supportedlanguages.md).
- Integrace s znalosti jazyka. Kromě vstupní zvuk převod na text, *řeči na Text* poskytuje aplikacím další schopností pochopit, co znamená text. Použije [jazyk Principy inteligentního Service(LUIS)](../LUIS/Home.md) extrahovat tříd Intent a entity z rozpoznaný text.

### <a name="next-steps"></a>Další postup

- Začínáme pomocí služby rozpoznávání řeči společnosti Microsoft s [rozhraní REST API](GetStarted/GetStartedREST.md) nebo [klientské knihovny](GetStarted/GetStartedClientLibraries.md).
- Podívejte se na [ukázkové aplikace](samples.md) v upřednostňovanou programovací jazyk.
- Přejděte do části odkaz Najít [Microsoft řeči Protocol](API-Reference-REST/websocketprotocol.md) podrobnosti a odkazy na rozhraní API.

## <a name="text-to-speech-speech-synthesis"></a>Převod textu na řeč (řeči souhrnnou)

*Převod textu na řeč* rozhraní API REST použijte k převedení strukturovaných textových zvuk datového proudu. Rozhraní API zadejte převod rychlé převod textu na řeč v různých jazycích a hlasy. Kromě toho uživatelé také mít možnost měnit zvuk charakteristiky, jako jsou například výslovnosti, svazek, výška atd. pomocí SSML značky.

### <a name="next-steps"></a>Další postup

- Začínáme používat službu Microsoft převod textu na řeč: [Text, který se referenční dokumentace rozhraní API pro rozpoznávání řeči](api-reference-rest/bingvoiceoutput.md). Úplný seznam jazyků a podporuje převod textu na řeč, najdete v části [podporované národní prostředí a písem hlasové](api-reference-rest/bingvoiceoutput.md#SupLocales).
