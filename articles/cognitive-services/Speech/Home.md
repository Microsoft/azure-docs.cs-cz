---
title: Služba Microsoft Zpracování řeči Bingu | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Pomocí rozhraní API pro rozpoznávání řeči můžete do svých aplikací přidat akce zaměřené na řeč, včetně interakce v reálném čase s uživateli.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ff9de2557583eecd5ddb2acd97b445a93abc5fb6
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966651"
---
# <a name="what-is-bing-speech"></a>Co je Zpracování řeči Bingu?

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Cloudová rozhraní API pro zpracování řeči Bingu Microsoftu poskytuje vývojářům snadný způsob, jak ve svých aplikacích vytvářet výkonné funkce s podporou rozpoznávání řeči, jako je ovládání hlasových příkazů, uživatelský dialog s využitím přirozené konverzace pomocí řeči a přepisu a diktování řeči. Rozhraní API pro rozpoznávání řeči od Microsoftu podporuje převod *řeči na text* i *Převod textu na řeč* .

- **Převod řeči na text** Rozhraní API převede lidské řeči na text, který se dá použít jako vstup nebo příkazy k řízení vaší aplikace.
- **Převod textu na řeč** Rozhraní API převede text na zvukové streamy, které se dají přehrát uživateli vaší aplikace.

## <a name="speech-to-text-speech-recognition"></a>Převod řeči na text (rozpoznávání řeči)

Rozhraní API pro rozpoznávání řeči od Microsoftu *transcribes* zvukové streamy do textu, který může vaše aplikace zobrazit uživateli nebo jednat jako vstup příkazu. Poskytuje vývojářům dva způsoby, jak do svých aplikací přidat řeč: Rozhraní REST API **nebo** klientské knihovny založené na protokolu WebSocket.

- [Rozhraní REST API](GetStarted/GetStartedREST.md): Vývojáři můžou použít volání HTTP ze svých aplikací do služby pro rozpoznávání řeči.
- [Klientské knihovny](GetStarted/GetStartedClientLibraries.md): V případě pokročilých funkcí můžou vývojáři stahovat klientské knihovny Microsoft Speech a propojit je s aplikacemi.  Klientské knihovny jsou k dispozici na různých platformách (Windows, Android, iOS) pomocí různýchC#jazyků (, Java, JavaScript, ObjectiveC). Na rozdíl od rozhraní REST API klientské knihovny využívají protokol založený na protokolu WebSocket.

| Případy použití | [Rozhraní REST API](GetStarted/GetStartedREST.md) | [Klientské knihovny](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Převeďte krátký mluvený zvuk, například příkazy (velikost zvuku < 15 s) bez dočasných výsledků. | Ano | Ano |
| Převod dlouhého zvuku (> 15 s) | Ne | Ano |
| Streamování zvuku s požadovanými průběžnými výsledky | Ne | Ano |
| Pochopení textu převedeného ze zvuku pomocí LUIS | Ne | Ano |

Bez ohledu na to, jaký přístup vývojářům zvolíte (rozhraní REST API nebo klientské knihovny), služba Microsoft Speech Service podporuje následující:

- Pokročilé technologie rozpoznávání řeči od Microsoftu používané v Cortana, diktování Office, překladateli Office a dalších produktech Microsoftu.
- V reálném čase průběžné rozpoznávání. Rozhraní API pro rozpoznávání řeči umožňuje uživatelům přepisovat zvuk do textu v reálném čase a podporuje příjem mezilehlých výsledků slov, která byly doposud uznány. Služba rozpoznávání řeči také podporuje rozpoznávání koncových řeči. Kromě toho mohou uživatelé zvolit další možnosti formátování, jako jsou velká a interpunkční znaménka, vulgární výrazy a normalizace textu.
- Podporuje optimalizované výsledky rozpoznávání řeči pro *interaktivní*scénáře, *konverzace*a *Diktování* . U uživatelských scénářů vyžadujících vlastní jazykové modely a akustické modely vám [Custom Speech Service](../custom-speech-service/cognitive-services-custom-speech-home.md) umožňuje vytvářet modely řeči, které se přizpůsobí vaší aplikaci a vašim uživatelům.
- Podpora mnoha mluvených jazyků ve více dialektech. Úplný seznam podporovaných jazyků v každém režimu rozpoznávání najdete v tématu [jazyky rozpoznávání](api-reference-rest/supportedlanguages.md).
- Integrace s jazykovým porozuměním Kromě převodu vstupního zvukového vstupu na text poskytuje převod *řeči na text* další funkce, abyste pochopili, co text znamená. K extrakci záměrů a entit z rozpoznaného textu používá [Language Understanding Intelligent Service (Luis)](../LUIS/what-is-luis.md) .

### <a name="next-steps"></a>Další postup

- Začněte používat službu rozpoznávání řeči od Microsoftu s [rozhraními REST API](GetStarted/GetStartedREST.md) nebo s [klientskými knihovnami](GetStarted/GetStartedClientLibraries.md).
- Podívejte se na [ukázkové aplikace](samples.md) v upřednostňovaném programovacím jazyce.
- V části Reference najdete podrobnosti o [protokolu Microsoft Speech Protocol](API-Reference-REST/websocketprotocol.md) a odkazy na rozhraní API.

## <a name="text-to-speech-speech-synthesis"></a>Převod textu na řeč (syntéza řeči)

*Převod textu na řeč* Rozhraní API používají REST pro převod strukturovaného textu do zvukového datového proudu. Rozhraní API poskytují rychlý převod textu na převod řeči v různých hlasů a jazycích. Kromě toho uživatelé mají možnost měnit zvukové charakteristiky, jako je třeba výslovnost, objem, rozteč atd. použití značek SSML.

### <a name="next-steps"></a>Další kroky

- Začínáme používat Microsoft text ke službě Speech Service: [Odkaz na rozhraní API pro převod textu na řeč](api-reference-rest/bingvoiceoutput.md). Úplný seznam jazyků a hlasů podporovaných nástrojem Převod textu na řeč najdete v tématu [podporované místní a hlasová písma](api-reference-rest/bingvoiceoutput.md#SupLocales).
