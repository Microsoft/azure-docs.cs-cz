---
title: Přehled převodu textu na řeč – služba Speech
titleSuffix: Azure Cognitive Services
description: Funkce převodu textu na řeč ve službě rozpoznávání řeči umožňuje vašim aplikacím, nástrojům a zařízením převádět text na přirozeného mluveného slova přirozeného lidského typu. Tento článek představuje přehled výhod a možností služby převodu textu na řeč.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: trbye
ms.custom: cog-serv-seo-aug-2020
keywords: převod textu na řeč
ms.openlocfilehash: 7722c9387e8d6475c6da4c771e9a0e355a4ac0af
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104654845"
---
# <a name="what-is-text-to-speech"></a>Co je převod textu na řeč?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

V tomto přehledu se dozvíte o výhodách a funkcích služby převod textu na řeč, která umožňuje vašim aplikacím, nástrojům a zařízením převádět text na syntetizované rozpoznávání řeči typu člověk. Vyberte si ze standardních nebo neuronové hlasů nebo vytvořte vlastní hlas jedinečný pro svůj produkt nebo značku. 75 a standardní hlasy jsou k dispozici ve více než 45 jazycích a národních prostředích a 5 neuronové hlasy jsou k dispozici v rámci výběru počtu jazyků a národních prostředí. Úplný seznam podporovaných hlasů, jazyků a národních prostředí najdete v tématu [podporované jazyky](language-support.md#text-to-speech).

> [!NOTE]
> Zpracování řeči Bingu byl vyvyřazen z 15. října 2019. Pokud vaše aplikace, nástroje nebo produkty používají Zpracování řeči Bingu rozhraní API nebo Custom Speech, vytvořili jsme příručky, které vám pomůžou s migrací na službu Speech.
> - [Migrace z Zpracování řeči Bingu do služby pro rozpoznávání řeči](how-to-migrate-from-bing-speech.md)

## <a name="core-features"></a>Základní funkce

* Syntéza řeči – pomocí [sady Speech SDK](./get-started-text-to-speech.md) nebo [REST API](rest-text-to-speech.md) můžete převést převod textu na řeč pomocí standardních, neuronové nebo vlastních hlasů.

* Asynchronní syntéza dlouhého zvuku – používá [rozhraní API dlouhého zvukového rozhraní](long-audio-api.md) k asynchronnímu syntetizování souborů přemluveného textu na řeč delší než 10 minut (například zvukové knihy nebo přednášky). Na rozdíl od syntézy prováděné pomocí sady Speech SDK nebo řeči-text REST API nevrátí odpovědi v reálném čase. Očekává se, že se požadavky odesílají asynchronně, odpovědi se dotazují na a že se při zpřístupnění ze služby stáhne syntetizované zvuky. Jsou podporovány pouze vlastní hlasy neuronové.

* Standardní hlasy – vytvořené pomocí metod syntézy statistických ukazatelů nebo sloučení zřetězení. Tyto hlasy jsou vysoce srozumitelnější a zdravé přirozené. Můžete snadno povolit vašim aplikacím mluvit ve více než 45 jazycích, a to s využitím široké škály možností hlasu. Tyto hlasy poskytují přesnost na vysokou výslovnost, včetně podpory zkratek, rozšíření akronymů, výkladů data a času, telefonů a dalších. Úplný seznam standardních hlasů najdete v tématu [podporované jazyky](language-support.md#text-to-speech).

* Neuronové hlasy – špičkové sítě neuronové se používají k překonání limitů tradiční syntézy řeči s ohledem na zátěž a nevyužití v mluveném jazyce. Prosody předpověď a Hlasová syntéza se provádí současně, což vede k více kapalinovým a přirozeným výstupům. Hlasy neuronové se dají použít k zajištění většího přirozeného a poutavého působení chatovacích robotů o a hlasových asistentů, převodu digitálních textů, jako jsou například e-knihy na audiobooks, a k vylepšení navigačních systémů v klecích. V případě přirozeného Prosody jako přirozeného a jasného kloubování slov, neuronové hlasy významně omezují naslouchat únavu při interakci se systémy AI. Úplný seznam hlasů pro neuronové najdete v tématu [podporované jazyky](language-support.md#text-to-speech).

* Upravit styly speaking pomocí jazyka SSML-Speech syntézy (SSML) je značkovací jazyk založený na jazyce XML, který slouží k přizpůsobení výstupů řeči na text. Pomocí SSML můžete upravit rozteč, přidat pauzy, zlepšit výslovnost, zrychlit nebo zpomalit rychlost řeči, zvýšit nebo snížit objem a zadat u jednoho dokumentu více hlasů. Další informace najdete v tématu [postup](speech-synthesis-markup.md) pro úpravu stylu speaking.

* Visemes- [visemes](how-to-speech-synthesis-viseme.md) jsou klíčovým výsledkem pozorovaného hlasu, včetně pozice sad LIP, vidlice a jazyka při vytváření konkrétního foném. Visemes mají silnou korelaci s hlasy a fonémy. Pomocí událostí viseme v sadě Speech SDK můžete vygenerovat data animace obličeje, která se dají použít k animování plošek v oblasti komunikace, vzdělávání, zábavy a zákaznických služeb v sadě LIP. 

## <a name="get-started"></a>Začínáme

Pokud chcete začít používat převod textu na řeč, Projděte si [rychlý Start](get-started-text-to-speech.md) . Služba převod textu na řeč je k dispozici prostřednictvím [sady Speech SDK](speech-sdk.md), [REST API](rest-text-to-speech.md)a rozhraní příkazového [řádku pro rozpoznávání řeči](spx-overview.md)

## <a name="sample-code"></a>Ukázka kódu

Vzorový kód pro převod textu na řeč je k dispozici na GitHubu. Tyto ukázky překrývají převod textu na řeč v nejoblíbenějších programovacích jazycích.

- [Ukázky převodu textu na řeč (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Ukázky převodu textu na řeč (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customization"></a>Přizpůsobení

Kromě standardních a neuronové hlasů můžete vytvářet a doladit vlastní hlasy, které jsou pro váš produkt nebo značku jedinečné. Vše, co vše trvá, je několik zvukových souborů a přidružených přepisů. Další informace najdete v tématu [Začínáme s vlastním hlasem](how-to-custom-voice.md) .

## <a name="pricing-note"></a>Poznámka k ceně

Při použití služby převod textu na řeč se vám bude účtovat každý znak, který se převede na řeč, včetně interpunkce. I když samotný dokument SSML není fakturovatelný, volitelné prvky, které slouží k úpravě způsobu převodu textu na řeč, jako je fonémy a rozteč, se počítají jako Fakturovatelné znaky. Tady je seznam toho, co je Fakturovatelné:

- Text předaný do služby převodu textu na řeč v SSML textu žádosti
- Všechny značky v textovém poli textu žádosti ve formátu SSML, s výjimkou `<speak>` `<voice>` značek a
- Písmena, interpunkční znaménka, mezery, tabulátory, značky a všechny prázdné znaky
- Každý bod kódu definovaný v kódování Unicode

Podrobné informace najdete v tématu [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Každý čínský, japonský a korejský znak jazyka se počítá jako dva znaky pro účely fakturace.

## <a name="reference-docs"></a>Referenční dokumenty

- [Speech SDK](speech-sdk.md)
- [REST API: převod textu na řeč](rest-text-to-speech.md)

## <a name="next-steps"></a>Další kroky

- [Získat bezplatné předplatné služby Speech](overview.md#try-the-speech-service-for-free)
- [Získat sadu Speech SDK](speech-sdk.md)
