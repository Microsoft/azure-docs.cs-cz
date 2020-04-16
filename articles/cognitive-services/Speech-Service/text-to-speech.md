---
title: Převod textu na řeč - Řečová služba
titleSuffix: Azure Cognitive Services
description: Funkce převodu textu na řeč ve službě Řeč umožňuje aplikacím, nástrojům nebo zařízením převést text na přirozenou syntetizovanou řeč. Zvolte přednastavené hlasy nebo si vytvořte vlastní hlas.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.openlocfilehash: 1a8b458eb004b44d0045f36b18d88e11e019c4d2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399507"
---
# <a name="what-is-text-to-speech"></a>Co je převod textu na řeč?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Převod textu na řeč ze služby Řeč umožňuje aplikacím, nástrojům nebo zařízením převést text na syntetizovanou řeč podobné člověku. Vyberte si ze standardních a neurálních hlasů nebo vytvořte vlastní hlas, který je jedinečný pro váš produkt nebo značku. Více než 75 standardních hlasů je k dispozici ve více než 45 jazycích a národních prostředích a 5 neurálních hlasů je k dispozici ve vybraném počtu jazyků a národních prostředí. Úplný seznam podporovaných hlasů, jazyků a národních prostředí naleznete v [tématu podporované jazyky](language-support.md#text-to-speech).

> [!NOTE]
> 15. října 2019 byla vyřazena z provozu proslov Bing. Pokud vaše aplikace, nástroje nebo produkty používají řešení API pro rozpoznávání řeči Bingu nebo vlastní řeč, vytvořili jsme průvodce, které vám pomohou migrovat do služby Řeč.
> - [Migrace z řeči Bingu do služby Řeč](how-to-migrate-from-bing-speech.md)

## <a name="core-features"></a>Základní funkce

* Syntéza řeči – pomocí [rozhraní Speech SDK](quickstarts/text-to-speech-audio-file.md) nebo [ROZHRANÍ REST API](rest-text-to-speech.md) můžete převést text na řeč pomocí standardních, neurálních nebo vlastních hlasů.

* Asynchronní syntéza dlouhého zvuku – Pomocí [rozhraní Long Audio API](long-audio-api.md) můžete asynchronně syntetizovat soubory převodu textu na řeč delší než 10 minut (například zvukové knihy nebo přednášky). Na rozdíl od syntézy prováděné pomocí sady REST K sady Speech SDK nebo rozhraní REST API převodu řeči na text nejsou odpovědi vráceny v reálném čase. Očekává se, že požadavky jsou odesílány asynchronně, odpovědi jsou dotazovány a syntetizovaný zvuk je stažen, když je k dispozici ze služby. Podporovány jsou pouze vlastní neurální hlasy.

* Standardní hlasy - Vytvořeno pomocí statistických metod parametrické syntézy a/nebo syntézy zřetězení. Tyto hlasy jsou vysoce srozumitelné a zvuk přirozené. Můžete snadno povolit, aby vaše aplikace mohly mluvit ve více než 45 jazycích, s širokou škálou hlasových možností. Tyto hlasy poskytují vysokou přesnost výslovnosti, včetně podpory zkratek, rozšíření zkratek, interpretací data a času, polyfonů a dalších. Úplný seznam standardních hlasů naleznete v [části Podporované jazyky](language-support.md#text-to-speech).

* Nervové hlasy - Hluboké neuronové sítě se používají k překonání mezí tradiční syntézy řeči s ohledem na stres a intonaci v mluveném jazyce. Prosody predikce a syntéza hlasu jsou prováděny současně, což má za následek více tekutin a přirozeně znějící výstupy. Neurální hlasy lze použít k tomu, aby interakce s chatboty a hlasovými asistenty byly přirozenější a poutavější, převáděly digitální texty, jako jsou e-knihy, do audioknih a vylepšovaly navigační systémy v autě. S lidskou přirozenou prozodomií a jasnou artikulací slov nervové hlasy výrazně snižují únavu z poslechu při interakci se systémy AI. Úplný seznam neurálních hlasů naleznete v [části Podporované jazyky](language-support.md#text-to-speech).

* Jazyk značek syntézy řeči (SSML) – značkovací jazyk založený na jazyce XML, který slouží k přizpůsobení výstupů řeči na text. Pomocí SSML můžete upravit výšku, přidat pauzy, zlepšit výslovnost, zrychlit nebo zpomalit rychlost mluvení, zvýšit nebo snížit hlasitost a přiřadit více hlasů do jednoho dokumentu. Viz [SSML](speech-synthesis-markup.md).

## <a name="get-started"></a>Začínáme

Služba převodu textu na řeč je k dispozici prostřednictvím [sady Speech SDK](speech-sdk.md). Existuje několik běžných scénářů, které jsou k dispozici jako rychlé starty v různých jazycích a platformách:

* [Syntéza řeči do zvukového souboru](quickstarts/text-to-speech-audio-file.md)
* [Syntéza řeči do reproduktoru](quickstarts/text-to-speech.md)
* [Asynchronně syntetizovat zvuk dlouhého tvaru](quickstarts/text-to-speech/async-synthesis-long-form-audio.md)

Pokud dáváte přednost, služba převodu textu na řeč je přístupná prostřednictvím [REST](rest-text-to-speech.md).

## <a name="sample-code"></a>Ukázka kódu

Ukázkový kód pro převod textu na řeč je k dispozici na GitHubu. Tyto ukázky zahrnují převod převodu převodu převodu převodu převodu převodu převodu převodu převodu převodu převodu na řeč v nejpopulárnějších programovacích jazycích.

- [Ukázky převodu textu na řeč (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Ukázky převodu textu na řeč (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customization"></a>Přizpůsobení

Kromě standardních a neurálních hlasů můžete vytvářet a dolaďovat vlastní hlasy jedinečné pro váš produkt nebo značku. Stačí začít, je několik zvukových souborů a související přepisy. Další informace [najdete v tématu Začínáme s vlastním hlasem](how-to-custom-voice.md)

## <a name="pricing-note"></a>Cenová poznámka

Při použití služby převodu textu na řeč se vám bude účtovat každý znak, který je převeden na řeč, včetně interpunkce. Zatímco samotný dokument SSML není fakturovatelný, volitelné prvky, které se používají k úpravě způsobu převodu textu na řeč, jako jsou fonémy a rozteč, se počítají jako fakturovatelné znaky. Zde je seznam toho, co je fakturovatelné:

- Text předaný službě převodu textu na řeč v těle SSML požadavku
- Všechny značky v textovém poli těla požadavku ve formátu `<speak>` `<voice>` SSML, s výjimkou a značek
- Písmena, interpunkce, mezery, tabulátory, značky a všechny prázdné znaky
- Každý bod kódu definovaný v Unicode

Podrobné informace naleznete v [tématu Ceny](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Každý znak čínštiny, japonštiny a korejštiny se počítá jako dva znaky pro fakturaci.

## <a name="reference-docs"></a>Referenční dokumenty

- [Speech SDK](speech-sdk.md)
- [ROZHRANÍ REST API: Převod textu na řeč](rest-text-to-speech.md)

## <a name="next-steps"></a>Další kroky

- [Získejte předplatné služby free speech](get-started.md)
- [Získání sady SDK pro rozpoznávání řeči](speech-sdk.md)
