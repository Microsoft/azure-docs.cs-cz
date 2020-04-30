---
title: Jazyková podpora – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Služba rozpoznávání řeči podporuje mnoho jazyků pro převod řeči na text a převod textu na řeč spolu s překladem řeči. Tento článek poskytuje úplný seznam funkcí jazykové podpory podle funkcí služby.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: trbye
ms.custom: seodec18
ms.openlocfilehash: 0b6fea381bd6b4aa8ad3e7061e6f632176c41033
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82113829"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Podpora jazyka a hlasu pro službu rozpoznávání řeči

Jazyková podpora se liší podle funkcí služby Speech. Následující tabulka shrnuje jazykovou podporu pro nabídky převodu [řeči na text](#speech-to-text), převod [textu na řeč](#text-to-speech)a [rozpoznávání řeči](#speech-translation) .

## <a name="speech-to-text"></a>Převod řeči na text

Sada Microsoft Speech SDK i REST API podporují následující jazyky (národní prostředí). Pro zvýšení přesnosti se přizpůsobení nabídne pro podmnožinu jazyků prostřednictvím nahrávání zvuku a přepisu s popiskem nebo souvisejícího textu: věty. Přizpůsobení výslovnosti je aktuálně k dispozici pouze pro `en-US` a `de-DE`. Další informace o přizpůsobení [najdete tady](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Národní prostředí  | Jazyk                          | Podporuje se | Vlastní nastavení                                    |
|---------|-----------------------------------|-----------|---------------------------------------------------|
| `ar-AE` | Arabština (Spojené arabské emiráty)                      | Ano       | Ne                                                |
| `ar-BH` | Arabština (Bahrajn), moderní Standard | Ano       | Jazykový model                                    |
| `ar-EG` | Arabština (Egypt)                    | Ano       | Jazykový model                                    |
| `ar-KW` | Arabština (Kuvajt)                   | Ano       | Ne                                                |
| `ar-QA` | Arabština (Katar)                    | Ano       | Ne                                                |
| `ar-SA` | Arabština (Saúdská Arábie)             | Ano       | Ne                                                |
| `ar-SY` | Arabština (Sýrie)                    | Ano       | Jazykový model                                    |
| `ca-ES` | Katalánština                           | Ano       | Jazykový model                                    |
| `da-DK` | dánština (Dánsko)                  | Ano       | Jazykový model                                    |
| `de-DE` | němčina (Německo)                  | Ano       | Akustický model<br>Jazykový model<br>Výslovnost |
| `en-AU` | Angličtina (Austrálie)               | Ano       | Akustický model<br>Jazykový model                  |
| `en-CA` | Angličtina (Kanada)                  | Ano       | Akustický model<br>Jazykový model                  |
| `en-GB` | Angličtina (Spojené království)          | Ano       | Akustický model<br>Jazykový model<br>Výslovnost |
| `en-IN` | Angličtina (Indie)                   | Ano       | Akustický model<br>Jazykový model                  |
| `en-NZ` | Angličtina (Nový Zéland)             | Ano       | Akustický model<br>Jazykový model                  |
| `en-US` | Angličtina (Spojené státy)           | Ano       | Akustický model<br>Jazykový model<br>Výslovnost |
| `es-ES` | Španělština (Španělsko)                   | Ano       | Akustický model<br>Jazykový model                  |
| `es-MX` | Španělština (Mexiko)                  | Ano       | Akustický model<br>Jazykový model                  |
| `fi-FI` | finština (Finsko)                 | Ano       | Jazykový model                                    |
| `fr-CA` | Francouzština (Kanada)                   | Ano       | Akustický model<br>Jazykový model                  |
| `fr-FR` | francouzština (Francie)                   | Ano       | Akustický model<br>Jazykový model<br>Výslovnost |
| `gu-IN` | Gudžarátština (Indie)                 | Ano       | Jazykový model                                    |
| `hi-IN` | hindština (Indie)                     | Ano       | Akustický model<br>Jazykový model                  |
| `it-IT` | italština (Itálie)                   | Ano       | Akustický model<br>Jazykový model<br>Výslovnost |
| `ja-JP` | japonština (Japonsko)                  | Ano       | Jazykový model                                    |
| `ko-KR` | korejština (Jižní Korea)                    | Ano       | Jazykový model                                    |
| `mr-IN` | Maráthština (Indie)                   | Ano       | Jazykový model                                    |
| `nb-NO` | Norština (Bokmål) (Norsko)       | Ano       | Jazykový model                                    |
| `nl-NL` | nizozemština (Nizozemsko)               | Ano       | Jazykový model                                    |
| `pl-PL` | polština (Polsko)                   | Ano       | Jazykový model                                    |
| `pt-BR` | Portugalština (Brazílie)               | Ano       | Akustický model<br>Jazykový model<br>Výslovnost |
| `pt-PT` | portugalština (Portugalsko)             | Ano       | Jazykový model                                    |
| `ru-RU` | ruština (Rusko)                  | Ano       | Akustický model<br>Jazykový model                  |
| `sv-SE` | švédština (Švédsko)                  | Ano       | Jazykový model                                    |
| `ta-IN` | Tamilština (Indie)                     | Ano       | Jazykový model                                    |
| `te-IN` | Telugština (Indie)                    | Ano       | Ne                                                |
| `th-TH` | thajština (Thajsko)                   | Ano       | Ne                                                |
| `tr-TR` | turečtina (Turecko)                  | Ano       | Ne                                                |
| `zh-CN` | Čínština (Mandarin, zjednodušená)    | Ano       | Akustický model<br>Jazykový model                  |
| `zh-HK` | Čínština (kantonština, tradiční)  | Ano       | Jazykový model                                    |
| `zh-TW` | Čínština (tchajwanský mandarinka)      | Ano       | Jazykový model                                    |

## <a name="text-to-speech"></a>Převod textu na řeč

Sada Microsoft Speech SDK a rozhraní REST API podporují tyto hlasy, z nichž každý podporuje konkrétní jazyk a dialekt identifikovaný národním prostředím.

> [!IMPORTANT]
> Ceny se liší pro standardní, vlastní a neuronové hlasy. Další informace najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) .

### <a name="neural-voices"></a>Hlasy neuronové

Neuronové převod textu na řeč je nový typ syntézy řeči, který využívá rozsáhlé sítě neuronové. Při použití hlasu neuronové je syntetizované rozpoznávání řeči skoro neodlišitelné od lidských nahrávek.

Neuronové hlasy se dají použít k zajištění většího přirozeného a poutavého působení s chatovacích robotů o a hlasovými asistenty, konverzí digitálních textů, jako jsou například e-knihy, do audiobooks a vylepšení navigačních systémů v klecích. V případě přirozeného Prosody jako přirozeného a jasného kloubování slov, neuronové hlasy významně omezují naslouchat únavu při interakci uživatelů se systémy AI.

Další informace o regionální dostupnosti najdete v tématu [oblasti](regions.md#standard-and-neural-voices).

| Národní prostředí  | Jazyk            | Pohlaví | Úplné mapování názvu služby                                               | Krátký název hlasu        |
|---------|---------------------|--------|-------------------------------------------------------------------------|-------------------------|
| `de-DE` | němčina (Německo)    | Female | Hlasový Převod textu na řeč pro Microsoft Server Speech (de-DE, KatjaNeural)     | de-DE-KatjaNeural "     |
| `en-US` | Angličtina (USA)        | Female | Hlasový Převod textu na řeč pro Microsoft Server Speech (EN-US, AriaNeural)      | "en-US-AriaNeural"      |
| `en-US` | Angličtina (USA)        | Male   | Hlasový Převod textu na řeč pro Microsoft Server Speech (EN-US, GuyNeural)       | "en-US-GuyNeural"       |
| `it-IT` | italština (Itálie)     | Female | Microsoft Server Speech Převod textu na řeč hlas (IT – IT, ElsaNeural)      | "IT-IT-ElsaNeural"      |
| `pt-BR` | Portugalština (Brazílie) | Female | Microsoft Server Speech Převod textu na řeč Voice (pt-BR, FranciscaNeural) | pt-BR-FranciscaNeural |
| `zh-CN` | Čínština (Mandarin, zjednodušená)  | Female | "Microsoft Server Speech Převod textu na řeč Voice (zh-CN, XiaoxiaoNeural)"  | zh-CN-XiaoxiaoNeural "  |

> [!IMPORTANT]
> `en-US-JessaNeural` Hlas se změnil na `en-US-AriaNeural`. Pokud jste předtím používali "Jessa", převeďte ho na standard ARIA.

Pokud se chcete dozvědět, jak můžete nakonfigurovat a upravit hlasy neuronové, přečtěte si téma věnované [jazykům pro označování řeči](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> V požadavcích na Shrnutí řeči můžete použít buď úplné mapování názvu služby, nebo krátký název hlasu.

### <a name="standard-voices"></a>Standardní hlasy

Více než 75 standardních hlasů je k dispozici ve více než 45 jazycích a národních prostředích, což vám umožní převést text na syntetizované řeč. Další informace o regionální dostupnosti najdete v tématu [oblasti](regions.md#standard-and-neural-voices).

| Národní prostředí | Jazyk | Pohlaví | Úplné mapování názvu služby | Krátký název |
|--|--|--|--|--|
| <sup>první</sup>`ar-EG` | Arabština (Egypt) | Female | Microsoft Server Speech Převod textu na řeč Voice (ar-EG, Hoda) | ar-EG-Hoda |
| `ar-SA` | Arabština (Saúdská Arábie) | Male | Microsoft Server Speech Převod textu na řeč Voice (ar-SA, Naayf) | ar-SA-Naayf " |
| `bg-BG` | Bulharština | Male | "Microsoft Server Speech Převod textu na řeč Voice (BG-BG, Ivan)" | BG-BG-Ivan |
| `ca-ES` | katalánština (Španělsko) | Female | Microsoft Server Speech Převod textu na řeč hlas (CA-ES, HerenaRUS) | "Ca-ES-HerenaRUS" |
| `cs-CZ` | Čeština | Male | Hlasový Převod textu na řeč pro Microsoft Server Speech (cs-CZ, Jakub) | cs-CZ-Jakub " |
| `da-DK` | Dánština | Female | Microsoft Server Speech Převod textu na řeč Voice (da-DK, HelleRUS) | "da-DK-HelleRUS" |
| `de-AT` | Němčina (Rakousko) | Male | Microsoft Server Speech Převod textu na řeč hlas (de-AT, Michael) | de-AT-Michael |
| `de-CH` | Němčina (Švýcarsko) | Male | Microsoft Server Speech Převod textu na řeč Voice (de-CH; Karsten) | de-CH-Karsten |
| `de-DE` | němčina (Německo) | Female | Hlasový Převod textu na řeč pro Microsoft Server Speech (de-DE, Hedda) | de-DE-Hedda " |
|  |  | Female | Hlasový Převod textu na řeč pro Microsoft Server Speech (de-DE, HeddaRUS) | de-DE-HeddaRUS " |
|  |  | Male | Hlasový Převod textu na řeč pro Microsoft Server Speech (de-DE, Stefan, Apollo) | de-DE-Stefan-Apollo " |
| `el-GR` | Řečtina | Male | Hlasový Převod textu na řeč pro Microsoft Server Speech (El-GR, Stefanos) | "El-GR-Stefanos" |
| `en-AU` | Angličtina (Austrálie) | Female | Microsoft Server Speech Převod textu na řeč Voice (EN-AU, Catherine) | "en-AU-Catherine" |
|  |  | Female | Microsoft Server Speech Převod textu na řeč Voice (EN-AU, HayleyRUS) | "en-AU-HayleyRUS" |
| `en-CA` | Angličtina (Kanada) | Female | Microsoft Server Speech Převod textu na řeč hlas (en-CA, Linda) | "en-CA-Linda" |
|  |  | Female | Microsoft Server Speech Převod textu na řeč hlas (en-CA, HeatherRUS) | "en-CA-HeatherRUS" |
| `en-GB` | Angličtina (Spojené království) | Female | Hlasový Převod textu na řeč pro Microsoft Server Speech (en-GB, Zuzana, Apollo) | "en-GB-Zuzana-Apollo" |
|  |  | Female | Hlasový Převod textu na řeč pro Microsoft Server Speech (en-GB, HazelRUS) | "en-GB-HazelRUS" |
|  |  | Male | Hlasový Převod textu na řeč pro Microsoft Server Speech (en-GB, Jiří, Apollo) | "en-GB-Jiří-Apollo" |
| `en-IE` | Angličtina (Irsko) | Male | Microsoft Server Speech Převod textu na řeč Voice (EN-IE, Novák) " | "en-IE-Novák" |
| `en-IN` | Angličtina (Indie) | Female | Microsoft Server Speech Převod textu na řeč Voice (en-IN, Heera, Apollo) | "en-IN-Heera-Apollo" |
|  |  | Female | Microsoft Server Speech Převod textu na řeč Voice (en-IN, PriyaRUS) | "en-IN-PriyaRUS" |
|  |  | Male | Microsoft Server Speech Převod textu na řeč Voice (en-IN, Ravi, Apollo) | "en-IN-Ravi-Apollo" |
| `en-US` | Angličtina (USA) | Female | Hlasový Převod textu na řeč pro Microsoft Server Speech (EN-US, ZiraRUS) | "en-US-ZiraRUS" |
|  |  | Female | Hlasový Převod textu na řeč pro Microsoft Server Speech (EN-US, AriaRUS) | "en-US-AriaRUS" |
|  |  | Male | Hlasový Převod textu na řeč pro Microsoft Server Speech (EN-US, BenjaminRUS) | "en-US-BenjaminRUS" |
|  |  | Male | Hlasový Převod textu na řeč pro Microsoft Server Speech (EN-US, Guy24kRUS) | "en-US-Guy24kRUS" |
| `es-ES` | Španělština (Španělsko) | Female | Microsoft Server Speech Převod textu na řeč Voice (ES-ES, Laura, Apollo) | ES-ES-Laura-Apollo " |
|  |  | Female | Microsoft Server Speech Převod textu na řeč Voice (ES-ES, HelenaRUS) | ES-ES-HelenaRUS " |
|  |  | Male | Microsoft Server Speech Převod textu na řeč Voice (ES-ES, Pablo, Apollo) | ES-ES-Pablo-Apollo |
| `es-MX` | Španělština (Mexiko) | Female | "Microsoft Server Speech Převod textu na řeč Voice (ES-MX, HildaRUS)" | ES-MX-HildaRUS |
|  |  | Male | "Microsoft Server Speech Převod textu na řeč Voice (ES-MX, Raul, Apollo)" | ES-MX-Raul-Apollo |
| `fi-FI` | Finština | Female | Microsoft Server Speech Převod textu na řeč Voice (Fi-FI, HeidiRUS) | "Fi-FI-HeidiRUS" |
| `fr-CA` | Francouzština (Kanada) | Female | Microsoft Server Speech Převod textu na řeč hlas (fr-CA, Caroline) | "fr-CA-Caroline" |
|  |  | Female | Microsoft Server Speech Převod textu na řeč hlas (fr-CA, HarmonieRUS) | "fr-CA-HarmonieRUS" |
| `fr-CH` | Francouzština (Švýcarsko) | Male | Microsoft Server Speech Převod textu na řeč Voice (fr-CH, Guillaume) | "fr-CH-Guillaume" |
| `fr-FR` | francouzština (Francie) | Female | Hlasový Převod textu na řeč pro Microsoft Server Speech (fr-FR, Julie, Apollo) | "fr-FR-Julie-Apollo" |
|  |  | Female | Hlasový Převod textu na řeč pro Microsoft Server Speech (fr-FR, HortenseRUS) | "fr-FR-HortenseRUS" |
|  |  | Male | Hlasový Převod textu na řeč pro Microsoft Server Speech (fr-FR, Paul, Apollo) | "fr-FR-Paul-Apollo" |
| `he-IL` | Hebrejština (Izrael) | Male | Microsoft Server Speech Převod textu na řeč hlas (HE-IL, Asaf) | "he-IL-Asaf" |
| `hi-IN` | hindština (Indie) | Female | Microsoft Server Speech Převod textu na řeč Voice (dobrý den, Kalpana, Apollo) | "Hi-IN-Kalpana-Apollo" |
|  |  | Female | Microsoft Server Speech Převod textu na řeč Voice (dobrý den, Kalpana) | "Dobrý den-Kalpana" |
|  |  | Male | Microsoft Server Speech Převod textu na řeč Voice (dobrý den, Hemant) | "Dobrý den-Hemant" |
| `hr-HR` | Chorvatština | Male | Microsoft Server Speech Převod textu na řeč Voice (HR-HR, Matej) | "HR-HR-Matej" |
| `hu-HU` | Maďarština | Male | Microsoft Server Speech Převod textu na řeč Voice (hu-HU, Szabolcs) | "hu-HU-Szabolcs" |
| `id-ID` | Indonéština | Male | Hlasový Převod textu na řeč pro Microsoft Server Speech (ID-ID, Andika) | ID-ID-Andika |
| `it-IT` | Italština | Male | Microsoft Server Speech Převod textu na řeč hlas (IT – IT, Cosimo, Apollo) | "IT-IT-Cosimo-Apollo" |
|  |  | Female | Microsoft Server Speech Převod textu na řeč hlas (IT – IT, LuciaRUS) | "IT-IT-LuciaRUS" |
| `ja-JP` | Japonština | Female | Microsoft Server Speech Převod textu na řeč Voice (ja-JP, Ayumi, Apollo) | "ja-JP-Ayumi-Apollo" |
|  |  | Male | Microsoft Server Speech Převod textu na řeč Voice (ja-JP, Ichiro, Apollo) | "ja-JP-Ichiro-Apollo" |
|  |  | Female | Hlasový Převod textu na řeč pro Microsoft Server Speech (ja-JP, HarukaRUS) | "ja-JP-HarukaRUS" |
| `ko-KR` | Korejština | Female | Microsoft Server Speech Převod textu na řeč Voice (ko-KR, HeamiRUS) | "ko-KR-HeamiRUS" |
| `ms-MY` | Malajština | Male | Microsoft Server Speech Převod textu na řeč hlas (MS-MY, Rizwan) | "MS-MY-Rizwan" |
| `nb-NO` | Norština | Female | Hlasový Převod textu na řeč pro Microsoft Server Speech (NB-NO, HuldaRUS) | "NB-NO-HuldaRUS" |
| `nl-NL` | Nizozemština | Female | Microsoft Server Speech Převod textu na řeč Voice (nl-NL, HannaRUS) | nl-NL-HannaRUS " |
| `pl-PL` | Polština | Female | Microsoft Server Speech Převod textu na řeč Voice (pl-PL, PaulinaRUS) | pl-PL-PaulinaRUS " |
| `pt-BR` | Portugalština (Brazílie) | Female | Microsoft Server Speech Převod textu na řeč Voice (pt-BR, HeloisaRUS) | pt-BR-HeloisaRUS |
|  |  | Male | Microsoft Server Speech Převod textu na řeč Voice (pt-BR, Daniel, Apollo) | pt-BR-Daniel-Apollo |
| `pt-PT` | portugalština (Portugalsko) | Female | "Microsoft Server Speech Převod textu na řeč Voice (pt-PT, HeliaRUS)" | pt-PT-HeliaRUS |
| `ro-RO` | Rumunština | Male | Microsoft Server Speech Převod textu na řeč Voice (RO-RO, Andrei) | "RO-RO-Andrei" |
| `ru-RU` | Ruština | Female | Microsoft Server Speech Převod textu na řeč Voice (ru-RU, Irina, Apollo) | ru-RU-Irina-Apollo " |
|  |  | Male | Microsoft Server Speech Převod textu na řeč Voice (ru-RU, Pavel, Apollo) | ru-RU-Pavel-Apollo " |
|  |  | Female | Microsoft Server Speech Převod textu na řeč Voice (ru-RU, EkaterinaRUS) | ru-RU – EkaterinaRUS |
| `sk-SK` | Slovenština | Male | Hlasový Převod textu na řeč pro Microsoft Server Speech (SK-SK; Filip) | "SK-SK-Filip" |
| `sl-SI` | Slovinština | Male | Microsoft Server Speech Převod textu na řeč Voice (SL-SI, lado) | "SL-SI-lado" |
| `sv-SE` | Švédština | Female | Microsoft Server Speech Převod textu na řeč Voice (sv-SE, HedvigRUS) | "sv-SE-HedvigRUS" |
| `ta-IN` | Tamilština (Indie) | Male | Microsoft Server Speech Převod textu na řeč Voice (ta-IN, Valluvar) | "ta-IN-Valluvar" |
| `te-IN` | Telugština (Indie) | Female | Hlasový Převod textu na řeč pro Microsoft Server Speech (te-IN, Chitra) | "te-IN-Chitra" |
| `th-TH` | Thajština | Male | Microsoft Server Speech Převod textu na řeč Voice (Th-tou, Pattara) | "th-TH-Pattara" |
| `tr-TR` | turečtina (Turecko) | Female | "Hlasový Převod textu na řeč pro Microsoft Server Speech (tr-TR, SedaRUS)" | "tr-TR-SedaRUS" |
| `vi-VN` | Vietnamština | Male | Microsoft Server Speech Převod textu na řeč Voice (VI-VN, a) " | "VI-VN-a" |
| `zh-CN` | Čínština (Mandarin, zjednodušená) | Female | "Microsoft Server Speech Převod textu na řeč Voice (zh-CN, HuihuiRUS)" | zh-CN-HuihuiRUS " |
|  |  | Female | Microsoft Server Speech Převod textu na řeč Voice (zh-CN, Yaoyao, Apollo) | zh-CN-Yaoyao-Apollo " |
|  |  | Male | Microsoft Server Speech Převod textu na řeč Voice (zh-CN, Kangkang, Apollo) | zh-CN-Kangkang-Apollo " |
| `zh-HK` | Čínština (kantonština, tradiční) | Female | Microsoft Server Speech Převod textu na řeč Voice (zh-HK, Tracy, Apollo) | "zh-HK-Tracy-Apollo" |
|  |  | Female | Microsoft Server Speech Převod textu na řeč Voice (zh-HK, TracyRUS) | "zh-HK-TracyRUS" |
|  |  | Male | Microsoft Server Speech Převod textu na řeč Voice (zh-HK, Danny, Apollo) | "zh-HK-Danny-Apollo" |
| `zh-TW` | Čínština (tchajwanský mandarinka) | Female | Microsoft Server Speech Převod textu na řeč Voice (zh-TW, Yating, Apollo) | "zh-TW-Yating-Apollo" |
|  |  | Female | Microsoft Server Speech Převod textu na řeč Voice (zh-TW, HanHanRUS) | "zh-TW-HanHanRUS" |
|  |  | Male | Microsoft Server Speech Převod textu na řeč Voice (zh-TW, Zhiwei, Apollo) | "zh-TW-Zhiwei-Apollo" |

**1** *ar-eg podporuje moderní arabštinu Standard (MSA).*

> [!IMPORTANT]
> `en-US-Jessa` Hlas se změnil na `en-US-Aria`. Pokud jste předtím používali "Jessa", převeďte ho na standard ARIA.

> [!TIP]
> V požadavcích na Shrnutí řeči můžete použít buď úplné mapování názvu služby, nebo krátký název hlasu.

### <a name="customization"></a>Přizpůsobení

Vlastní nastavení hlasu je `de-DE`k `en-GB`dispozici pro `es-MX`, `fr-FR`, `it-IT` `en-IN`, `en-US`,, `zh-CN`,, `pt-BR`a. Vyberte správné národní prostředí, které odpovídá školicím datům, které máte k dispozici pro výuku vlastního hlasového modelu. Například pokud jsou data nahrávání mluveného mluveného zvýraznění v angličtině, vyberte `en-GB`.

> [!NOTE]
> Ve vlastním hlasu nepodporujeme školicí modely v jazyce BI, s výjimkou české jazykové verze pro čínské angličtiny. Pokud chcete naučit čínský hlas, který může mluvit i v angličtině, vyberte možnost dvojjazyčně v angličtině. Hlasové školení ve všech národních prostředích začíná sadou dat 2000 + projevy, s výjimkou `en-US` a `zh-CN` kde můžete začít s jakoukoli velikostí školicích dat.

## <a name="speech-translation"></a>Překlad řeči

Rozhraní API pro **Překlad** řeči podporuje různé jazyky pro překlad hlasu na řeč a převod řeči na text. Zdrojový jazyk musí být vždy z tabulky jazyka Speech-to-text. Dostupné cílové jazyky závisí na tom, zda je cíl překladu řeč nebo text. Příchozí řeč můžete přeložit do více než [60 jazyků](https://www.microsoft.com/translator/business/languages/). K dispozici je podmnožina jazyků pro [syntézu řeči](language-support.md#text-languages).

### <a name="text-languages"></a>Jazyky textu

| Jazyk textu           | Kód jazyka |
|:------------------------|:-------------:|
| Afrikánština               | `af`          |
| Arabština                  | `ar`          |
| Bengálština                  | `bn`          |
| Bosenština (latinka)         | `bs`          |
| Bulharština               | `bg`          |
| Kantonština (tradiční) | `yue`         |
| Katalánština                 | `ca`          |
| Čínština (zjednodušená)      | `zh-Hans`     |
| Čínština (tradiční)     | `zh-Hant`     |
| Chorvatština                | `hr`          |
| Čeština                   | `cs`          |
| Dánština                  | `da`          |
| Nizozemština                   | `nl`          |
| Angličtina                 | `en`          |
| Estonština                | `et`          |
| Fijian                  | `fj`          |
| Filipino                | `fil`         |
| Finština                 | `fi`          |
| Francouzština                  | `fr`          |
| Němčina                  | `de`          |
| Řečtina                   | `el`          |
| Gudžarátština                | `gu`          |
| Haitská kreolština          | `ht`          |
| Hebrejština                  | `he`          |
| Hindština                   | `hi`          |
| Hmong Macek               | `mww`         |
| Maďarština               | `hu`          |
| Indonéština              | `id`          |
| Irština                   | `ga`          |
| Italština                 | `it`          |
| Japonština                | `ja`          |
| Kannadština                 | `kn`          |
| Svahilština               | `sw`          |
| Klingon                 | `tlh`         |
| Klingon (plqaD)         | `tlh-Qaak`    |
| Korejština                  | `ko`          |
| Lotyština                 | `lv`          |
| Litevština              | `lt`          |
| Malgašština                | `mg`          |
| Malajština                   | `ms`          |
| Malajalámština               | `ml`          |
| Maltština                 | `mt`          |
| Maorština                   | `mi`          |
| Maráthština                 | `mr`          |
| Norština               | `nb`          |
| Perština                 | `fa`          |
| Polština                  | `pl`          |
| Portugalština (Brazílie)     | `pt-br`       |
| portugalština (Portugalsko)   | `pt-pt`       |
| Paňdžábština                 | `pa`          |
| Queretaro Otomi         | `otq`         |
| Rumunština                | `ro`          |
| Ruština                 | `ru`          |
| Samoan                  | `sm`          |
| Srbština (cyrilice)      | `sr-Cyrl`     |
| Srbština (latinka)         | `sr-Latn`     |
| Slovenština                  | `sk`          |
| Slovinština               | `sl`          |
| Španělština                 | `es`          |
| Švédština                 | `sv`          |
| Tahitian                | `ty`          |
| Tamilština                   | `ta`          |
| Telugština                  | `te`          |
| Thajština                    | `th`          |
| Tongánština                  | `to`          |
| Turečtina                 | `tr`          |
| Ukrajinština               | `uk`          |
| Urdština                    | `ur`          |
| Vietnamština              | `vi`          |
| Velština                   | `cy`          |
| Yucatec Maya            | `yua`         |

## <a name="next-steps"></a>Další kroky

* [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Přečtěte si téma rozpoznávání řeči v jazyce C. #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
