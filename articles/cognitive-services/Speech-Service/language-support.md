---
title: Jazyková podpora - Řečová služba
titleSuffix: Azure Cognitive Services
description: Služba Řeč podporuje mnoho jazyků pro převod řeči na text a převod textu na řeč spolu s překladem řeči. Tento článek obsahuje úplný seznam funkce jazykové podpory podle služeb.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: d01ab60790311649e424a98d5a08c6af0bca90f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336039"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Jazyková a hlasová podpora služby Speech

Jazyková podpora se liší podle funkce služby Speech. Následující tabulky shrnují jazykovou podporu pro nabídky [služby Převod řeči na text](#speech-to-text), Převod textu na [řeč](#text-to-speech)a [Překlad řeči.](#speech-translation)

## <a name="speech-to-text"></a>Převod řeči na text

Sada Microsoft Speech SDK a rozhraní REST API podporují následující jazyky (národní prostředí). Pro zvýšení přesnosti je pro podmnožinu jazyků nabízeno přizpůsobení prostřednictvím nahrání přepisů audio + člověkem nebo souvisejícího textu: věty. Přizpůsobení výslovnosti je v současné `en-US` `de-DE`době k dispozici pouze pro a . Další informace o přizpůsobení [naleznete zde](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Národní prostředí  | Jazyk                          | Podporuje se | Přizpůsobení                                    |
|---------|-----------------------------------|-----------|---------------------------------------------------|
| `ar-AE` | Arabština (SAE)                      | Ano       | Ne                                                |
| `ar-BH` | Arabština (Bahrajn), moderní standard | Ano       | Jazykový model                                    |
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
| `gu-IN` | Gudžarátáj (Ind.)                 | Ano       | Jazykový model                                    |
| `hi-IN` | hindština (Indie)                     | Ano       | Akustický model<br>Jazykový model                  |
| `it-IT` | italština (Itálie)                   | Ano       | Akustický model<br>Jazykový model<br>Výslovnost |
| `ja-JP` | japonština (Japonsko)                  | Ano       | Jazykový model                                    |
| `ko-KR` | korejština (Jižní Korea)                    | Ano       | Jazykový model                                    |
| `mr-IN` | Marathi (Indie)                   | Ano       | Jazykový model                                    |
| `nb-NO` | Norština (Bokmål) (Norsko)       | Ano       | Jazykový model                                    |
| `nl-NL` | nizozemština (Nizozemsko)               | Ano       | Jazykový model                                    |
| `pl-PL` | polština (Polsko)                   | Ano       | Jazykový model                                    |
| `pt-BR` | Portugalština (Brazílie)               | Ano       | Akustický model<br>Jazykový model<br>Výslovnost |
| `pt-PT` | portugalština (Portugalsko)             | Ano       | Jazykový model                                    |
| `ru-RU` | ruština (Rusko)                  | Ano       | Akustický model<br>Jazykový model                  |
| `sv-SE` | švédština (Švédsko)                  | Ano       | Jazykový model                                    |
| `ta-IN` | Tamil (Indie)                     | Ano       | Jazykový model                                    |
| `te-IN` | Telugu (Indie)                    | Ano       | Ne                                                |
| `th-TH` | thajština (Thajsko)                   | Ano       | Ne                                                |
| `tr-TR` | turečtina (Turecko)                  | Ano       | Ne                                                |
| `zh-CN` | Čínština (mandarínština, zjednodušená)    | Ano       | Akustický model<br>Jazykový model                  |
| `zh-HK` | Čínština (kantonská, tradiční)  | Ano       | Jazykový model                                    |
| `zh-TW` | Čínština (tchajwanská mandarínština)      | Ano       | Jazykový model                                    |

## <a name="text-to-speech"></a>Převod textu na řeč

Tato hlasy podporují rozhraní API sady Microsoft Speech SDK i REST, z nichž každý podporuje určitý jazyk a dialekt identifikovaný národním prostředím.

> [!IMPORTANT]
> Ceny se liší pro standardní, vlastní a nervové hlasy. Další informace naleznete na stránce [Ceny.](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)

### <a name="neural-voices"></a>Nervové hlasy

Neurální převod textu na řeč je nový typ syntézy řeči poháněný hlubokými neuronovými sítěmi. Při použití nervového hlasu je syntetizovaná řeč téměř k nerozeznání od lidských nahrávek.

Neurální hlasy mohou být použity k tomu, aby interakce s chatboty a hlasovými asistenty byly přirozenější a poutavější, převádějí digitální texty, jako jsou e-knihy, do audioknih a vylepšují navigační systémy v autě. S lidskou přirozenou prozodomií a jasnou artikulací slov nervové hlasy výrazně snižují únavu z poslechu, když uživatelé interagují se systémy AI.

Další informace o místní dostupnosti naleznete v [tématu Oblasti](regions.md#standard-and-neural-voices).

| Národní prostředí  | Jazyk            | Pohlaví | Mapování názvů celé služby                                               | Krátké hlasové jméno        |
|---------|---------------------|--------|-------------------------------------------------------------------------|-------------------------|
| `de-DE` | němčina (Německo)    | Female | "Hlas převodu textu na řeč serveru Microsoft Server (de-DE, KatjaNeural)"     | "de-DE-KatjaNeural"     |
| `en-US` | Angličtina (USA)        | Female | "Převod textu na řeč na řeč serveru Microsoft Server (en US, AriaNeural)"      | "en-US-AriaNeural"      |
| `en-US` | Angličtina (USA)        | Male   | "Převod textu na řeč na řeč serveru Microsoft Server (en US, GuyNeural)"       | "en-US-GuyNeural"       |
| `it-IT` | italština (Itálie)     | Female | "Hlas převodu textu na řeč serveru Microsoft Server (it-IT, ElsaNeural)"      | "it-IT-ElsaNeural"      |
| `pt-BR` | Portugalština (Brazílie) | Female | "Převod textu na řeč serveru Microsoft Server (pt-BR, FranciscaNeural)" | "pt-BR-FranciscaNeural" |
| `zh-CN` | Čínština (mandarínština, zjednodušená)  | Female | "Převod textu na řeč serveru Microsoft Server (zh-CN, XiaoxiaoNeural)"  | "zh-CN-XiaoxiaoNeural"  |

> [!IMPORTANT]
> Hlas `en-US-JessaNeural` se změnil `en-US-AriaNeural`na . Pokud jste používali "Jessa" dříve, převést na "Aria".

Informace o konfiguraci a úpravě neurálních hlasů naleznete v [tématu Jazyk značek syntézy řeči](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> V požadavcích na syntézu řeči můžete použít mapování názvů celé služby nebo krátký název hlasu.

### <a name="standard-voices"></a>Standardní hlasy

Více než 75 standardních hlasů je k dispozici ve více než 45 jazycích a národních prostředích, které umožňují převést text na syntetizovanou řeč. Další informace o místní dostupnosti naleznete v [tématu Oblasti](regions.md#standard-and-neural-voices).

| Národní prostředí | Jazyk | Pohlaví | Mapování názvů celé služby | Krátký název |
|--|--|--|--|--|
| <sup>1</sup>`ar-EG` | Arabština (Egypt) | Female | "Převod textu na řeč na řeč serveru Microsoft Server (ar-EG, Hoda)" | "ar-EG-Hoda" |
| `ar-SA` | Arabština (Saúdská Arábie) | Male | "Převod textu na řeč na řeč serveru Microsoft Server (ar-SA, Naayf)" | "ar-SA-Naayf" |
| `bg-BG` | Bulharština | Male | "Převod textu na řeč na řeč serveru Microsoft Server (bg-BG, Ivan)" | "bg-bg-ivan" |
| `ca-ES` | katalánština (Španělsko) | Female | "Převod textu na řeč na řeč serveru Microsoft Server (ca-ES, HerenaRUS)" | "ca-ES-HerenaRUS" |
| `cs-CZ` | Čeština | Male | "Microsoft Server Speech Text to Speech Voice (cs-CZ, Jakub)" | "cs-CZ-Jakub" |
| `da-DK` | Dánština | Female | "Převod textu na řeč na řeč serveru Microsoft Server (da-DK, HelleRUS)" | "da-DK-HelleRUS" |
| `de-AT` | Němčina (Rakousko) | Male | "Hlas převodu řeči na řeč serveru Microsoft Server (de-AT, Michael)" | "de-AT-Michael" |
| `de-CH` | Němčina (Švýcarsko) | Male | "Hlas převodu řeči na řeč serveru Microsoft Server (de-CH, Karsten)" | "de-CH-Karsten" |
| `de-DE` | němčina (Německo) | Female | "Hlas převodu řeči na řeč serveru Microsoft Server (de-DE, Hedda)" | "de-DE-Hedda" |
|  |  | Female | "Hlas převodu řeči na řeč serveru Microsoft Server (de-DE, HeddaRUS)" | "de-DE-HeddaRUS" |
|  |  | Male | "Hlas převodu řeči na řeč serveru Microsoft Server (de-DE, Stefan, Apollo)" | "de-DE-Stefan-Apollo" |
| `el-GR` | Řečtina | Male | "Hlas převodu řeči na řeč serveru Microsoft Server (el-GR, Stefanos)" | "el-GR-Stefanos" |
| `en-AU` | Angličtina (Austrálie) | Female | "Hlas převodu textu na řeč serveru Microsoft Server (en-AU, Catherine)" | "en-AU-Catherine" |
|  |  | Female | "Hlas převodu textu na řeč serveru Microsoft Server (en-AU, HayleyRUS)" | "en-AU-HayleyRUS" |
| `en-CA` | Angličtina (Kanada) | Female | "Hlas převodu textu na řeč serveru Microsoft Server (cs-CA, Linda)" | "en-CA-Linda" |
|  |  | Female | "Hlas převodu textu na řeč serveru Microsoft Server (en-CA, HeatherRUS)" | "en-CA-HeatherRUS" |
| `en-GB` | Angličtina (Velká Británie) | Female | "Hlas převodu textu na řeč serveru Microsoft Server (en-GB, Zuzana, Apollo)" | "en-GB-Susan-Apollo" |
|  |  | Female | "Hlas převodu textu na řeč serveru Microsoft Server (en-GB, HazelRUS)" | "en-GB-HazelRUS" |
|  |  | Male | "Hlas převodu textu na řeč serveru Microsoft Server (en-GB, George, Apollo)" | "en-GB-George-Apollo" |
| `en-IE` | Angličtina (Irsko) | Male | "Hlas převodu textu na řeč serveru Microsoft Server (en-IE, Sean)" | "en-IE-Sean" |
| `en-IN` | Angličtina (Indie) | Female | "Převod textu na řeč na řeč serveru Microsoft Server (en-IN, Heera, Apollo)" | "en-IN-Heera-Apollo" |
|  |  | Female | "Hlas převodu textu na řeč serveru Microsoft Server (en-IN, PriyaRUS)" | "en-IN-PriyaRUS" |
|  |  | Male | "Převod textu na řeč na řeč serveru Microsoft Server (en-IN, Ravi, Apollo)" | "en-IN-Ravi-Apollo" |
| `en-US` | Angličtina (USA) | Female | "Převod textu na řeč na řeč serveru Microsoft Server (en-US, ZiraRUS)" | "en-US-ZiraRUS" |
|  |  | Female | "Převod textu na řeč na řeč serveru Microsoft Server (en US, AriaRUS)" | "en-US-AriaRUS" |
|  |  | Male | "Převod textu na řeč na řeč serveru Microsoft Server (en US, BenjaminRUS)" | "en-US-BenjaminRUS" |
|  |  | Male | "Převod textu na řeč na řeč serveru Microsoft Server (en US, Guy24kRUS)" | "en-US-Guy24kRUS" |
| `es-ES` | Španělština (Španělsko) | Female | "Převod textu na řeč na řeč serveru Microsoft Server (es-ES, Laura, Apollo)" | "es-ES-Laura-Apollo" |
|  |  | Female | "Převod textu na řeč na řeč serveru Microsoft Server (es-ES, HelenaRUS)" | "es-ES-HelenaRUS" |
|  |  | Male | "Převod textu na řeč na řeč serveru Microsoft Server (es-ES, Pablo, Apollo)" | "es-ES-Pablo-Apollo" |
| `es-MX` | Španělština (Mexiko) | Female | "Převod textu na řeč na řeč serveru Microsoft Server (es-MX, HildaRUS)" | "es-MX-HildaRUS" |
|  |  | Male | "Převod textu na řeč na řeč serveru Microsoft Server (es-MX, Raul, Apollo)" | "es-MX-Raul-Apollo" |
| `fi-FI` | Finština | Female | "Hlas převodu textu na řeč serveru Microsoft Server (fi-FI, HeidiRUS)" | "fi-FI-HeidiRUS" |
| `fr-CA` | Francouzština (Kanada) | Female | "Hlas převodu textu na řeč serveru Microsoft Server (fr-CA, Caroline)" | "fr-CA-Caroline" |
|  |  | Female | "Hlas převodu textu na řeč serveru Microsoft Server (fr-CA, HarmonieRUS)" | "fr-CA-HarmonieRUS" |
| `fr-CH` | Francouzština (Švýcarsko) | Male | "Hlas převodu řeči na řeč serveru Microsoft Server (fr-CH, Guillaume)" | "fr-CH-Guillaume" |
| `fr-FR` | francouzština (Francie) | Female | "Převod textu na řeč na řeč serveru Microsoft Server (fr-FR, Julie, Apollo)" | "fr-FR-Julie-Apollo" |
|  |  | Female | "Hlas převodu textu na řeč serveru Microsoft Server (fr-FR, HortenseRUS)" | "fr-FR-HortenseRUS" |
|  |  | Male | "Hlas převodu řeči na řeč serveru Microsoft Server (fr-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo" |
| `he-IL` | Hebrejština (Izrael) | Male | "Hlas převodu textu na řeč serveru Microsoft Server (he-IL, Asaf)" | "he-IL-Asaf" |
| `hi-IN` | hindština (Indie) | Female | "Převod textu na řeč na řeč serveru Microsoft Server (hi-IN, Kalpana, Apollo)" | "hi-IN-Kalpana-Apollo" |
|  |  | Female | "Převod textu na řeč na řeč serveru Microsoft Server (hi-IN, Kalpana)" | "hi-IN-Kalpana" |
|  |  | Male | "Hlas převodu textu na řeč serveru Microsoft Server (hi-IN, hemant)" | "hi-IN-Hemant" |
| `hr-HR` | Chorvatština | Male | "Hlas převodu řeči na řeč serveru Microsoft Server (hr-HR, Matej)" | "hr-HR-Matej" |
| `hu-HU` | Maďarština | Male | "Převod textu na řeč na řeč serveru Microsoft Server (hu-HU, Szabolcs)" | "hu-HU-Szabolcs" |
| `id-ID` | Indonéština | Male | "Hlas převodu řeči na řeč serveru Microsoft Server (ID-ID, Andika)" | "id-ID-Andika" |
| `it-IT` | Italština | Male | "Převod textu na řeč na řeč serveru Microsoft Server (it-IT, Cosimo, Apollo)" | "it-IT-Cosimo-Apollo" |
|  |  | Female | "Hlas převodu řeči na řeč serveru Microsoft Server (it-IT, LuciaRUS)" | "it-IT-LuciaRUS" |
| `ja-JP` | Japonština | Female | "Převod textu na řeč na řeč serveru Microsoft Server (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo" |
|  |  | Male | "Převod textu na řeč na řeč serveru Microsoft Server (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo" |
|  |  | Female | "Převod textu na řeč na řeč serveru Microsoft Server (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS" |
| `ko-KR` | Korejština | Female | "Hlas převodu řeči na řeč serveru Microsoft Server (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS" |
| `ms-MY` | Malajština | Male | "Převod textu na řeč na řeč serveru Microsoft Server (ms-MY, Rizwan)" | "paní MY-Rizwan" |
| `nb-NO` | Norština | Female | "Hlas převodu řeči na řeč serveru Microsoft Server (nb-NO, HuldaRUS)" | "nb-NO-HuldaRUS" |
| `nl-NL` | Nizozemština | Female | "Hlas převodu textu na řeč serveru Microsoft Server (nl-NL, HannaRUS)" | "nl-NL-HannaRUS" |
| `pl-PL` | Polština | Female | "Převod textu na řeč na řeč serveru Microsoft Server (pl-PL, PaulinaRUS)" | "pl-PL-PaulinaRUS" |
| `pt-BR` | Portugalština (Brazílie) | Female | "Převod textu na řeč na řeč serveru Microsoft Server (pt-BR, HeloisaRUS)" | "pt-BR-HeloisaRUS" |
|  |  | Male | "Převod textu na řeč na řeč serveru Microsoft Server (pt-BR, Daniel, Apollo)" | "pt-BR-Daniel-Apollo" |
| `pt-PT` | portugalština (Portugalsko) | Female | "Převod textu na řeč na řeč serveru Microsoft Server (pt-PT, HeliaRUS)" | "pt-PT-HeliaRUS" |
| `ro-RO` | Rumunština | Male | "Hlas převodu řeči na řeč serveru Microsoft Server (ro-RO, Andrej)" | "ro-RO-Andrei" |
| `ru-RU` | Ruština | Female | "Převod textu na řeč serveru Microsoft Server (ru-RU, Irina, Apollo)" | "ru-RU-Irina-Apollo" |
|  |  | Male | "Převod mluvy na řeč serveru Microsoft Server (ru-RU, Pavel, Apollo)" | "ru-RU-Pavel-Apollo" |
|  |  | Female | "Převod textu na řeč serveru Microsoft Server (ru-RU, EkaterinaRUS)" | ru-RU-EkaterinaRUS |
| `sk-SK` | Slovenština | Male | "Hlas převodu řeči na řeč serveru Microsoft Server (sk-SK, Filip)" | "sk-SK-Filip" |
| `sl-SI` | Slovinština | Male | "Hlas převodu textu na řeč serveru Microsoft Server (sl-SI, Lado)" | "sl-SI-Lado" |
| `sv-SE` | Švédština | Female | "Převod textu na řeč na řeč serveru Microsoft Server (sv-SE, HedvigRUS)" | "sv-SE-HedvigRUS" |
| `ta-IN` | Tamil (Indie) | Male | "Převod textu na řeč na řeč serveru Microsoft Server (ta-IN, Valluvar)" | "ta-IN-Valluvar" |
| `te-IN` | Telugu (Indie) | Female | "Hlas převodu textu na řeč serveru Microsoft Server (te-IN, Chitra)" | "te-IN-Chitra" |
| `th-TH` | Thajština | Male | "Převod textu na řeč serveru Microsoft Server (th-TH, Pattara)" | "th-TH-Pattara" |
| `tr-TR` | turečtina (Turecko) | Female | "Hlas převodu řeči na řeč serveru Microsoft Server (tr-TR, SedaRUS)" | "tr-TR-SedaRUS" |
| `vi-VN` | Vietnamština | Male | "Hlas převodu textu na řeč serveru Microsoft Server (vi-VN, An)" | "vi-VN-An" |
| `zh-CN` | Čínština (mandarínština, zjednodušená) | Female | "Převod textu na řeč serveru Microsoft Server (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS" |
|  |  | Female | "Převod textu na řeč na řeč serveru Microsoft Server (zh-CN, Yaoyao, Apollo)" | "zh-CN-Yaoyao-Apollo" |
|  |  | Male | "Převod textu na řeč na řeč serveru Microsoft Server (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo" |
| `zh-HK` | Čínština (kantonská, tradiční) | Female | "Převod textu na řeč na řeč serveru Microsoft Server (zh-HK, Tracy, Apollo)" | "zh-HK-Tracy-Apollo" |
|  |  | Female | "Převod textu na řeč na řeč serveru Microsoft Server (zh-HK, TracyRUS)" | "zh-HK-TracyRUS" |
|  |  | Male | "Převod textu na řeč na řeč serveru Microsoft Server (zh-HK, Danny, Apollo)" | "zh-HK-Danny-Apollo" |
| `zh-TW` | Čínština (tchajwanská mandarínština) | Female | "Převod textu na řeč na řeč serveru Microsoft Server (zh-TW, Yating, Apollo)" | "zh-TW-Yating-Apollo" |
|  |  | Female | "Převod textu na řeč serveru Microsoft Server (zh-TW, HanHanRUS)" | "zh-TW-HanHanRUS" |
|  |  | Male | "Převod textu na řeč na řeč serveru Microsoft Server (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo" |

**1** *ar-EG podporuje moderní standardní arabštinu (MSA).*

> [!IMPORTANT]
> Hlas `en-US-Jessa` se změnil `en-US-Aria`na . Pokud jste používali "Jessa" dříve, převést na "Aria".

> [!TIP]
> V požadavcích na syntézu řeči můžete použít mapování názvů celé služby nebo krátký název hlasu.

### <a name="customization"></a>Přizpůsobení

Hlasové přizpůsobení je `de-DE` `en-GB`k `en-IN` `en-US`dispozici `es-MX` `fr-FR`pro `it-IT` `pt-BR`aplikace `zh-CN`, , , , , a . Vyberte správné národní prostředí, které odpovídá trénovacím datům, která potřebujete k trénování vlastního hlasového modelu. Pokud jsou například data záznamu, která máte, mluvená v angličtině s britským přízvukem, vyberte . `en-GB`

> [!NOTE]
> Nepodporujeme bi-lingvální model školení v Custom Voice, s výjimkou čínsko-anglické bi-lingual. Vyberte "čínsko-anglicky dvojjazyčné", pokud chcete trénovat čínský hlas, který může mluvit anglicky stejně. Hlasové školení ve všech národních prostředích začíná datovou sadou 2 000+ projevy, s výjimkou `en-US` a `zh-CN` kde můžete začít s libovolnou velikostí trénovacích dat.

## <a name="speech-translation"></a>Překlad řeči

Rozhraní **API pro překlad řeči** podporuje různé jazyky pro překlad řeči na řeč a převod řeči na text. Zdrojový jazyk musí být vždy z tabulky jazyka Řeč na text. Dostupné cílové jazyky závisí na tom, zda je cílem překladu řeč nebo text. Příchozí řeč můžete přeložit do více než [60 jazyků](https://www.microsoft.com/translator/business/languages/). Pro [syntézu řeči](language-support.md#text-languages)je k dispozici podmnožina jazyků .

### <a name="text-languages"></a>Textové jazyky

| Jazyk textu           | Kód jazyka |
|:------------------------|:-------------:|
| Afrikánština               | `af`          |
| Arabština                  | `ar`          |
| Bangla                  | `bn`          |
| Bosenština (latinka)         | `bs`          |
| Bulharština               | `bg`          |
| Kantonská (tradiční) | `yue`         |
| Katalánština                 | `ca`          |
| Čínština (zjednodušená)      | `zh-Hans`     |
| Čínština (tradiční)     | `zh-Hant`     |
| Chorvatština                | `hr`          |
| Čeština                   | `cs`          |
| Dánština                  | `da`          |
| Nizozemština                   | `nl`          |
| Angličtina                 | `en`          |
| Estonština                | `et`          |
| Fidžijská                  | `fj`          |
| Filipino                | `fil`         |
| Finština                 | `fi`          |
| Francouzština                  | `fr`          |
| Němčina                  | `de`          |
| Řečtina                   | `el`          |
| Haitská kreolština          | `ht`          |
| Hebrejština                  | `he`          |
| Hindština                   | `hi`          |
| Hmong Daw               | `mww`         |
| Maďarština               | `hu`          |
| Indonéština              | `id`          |
| Irština                   | `ga`          |
| Italština                 | `it`          |
| Japonština                | `ja`          |
| Kannadština                 | `kn`          |
| Kiswahili               | `sw`          |
| Klingonské                 | `tlh`         |
| Klingon (plqaD)         | `tlh-Qaak`    |
| Korejština                  | `ko`          |
| Lotyština                 | `lv`          |
| Litevština              | `lt`          |
| Malagasy                | `mg`          |
| Malajština                   | `ms`          |
| Malajalámština               | `ml`          |
| Maltština                 | `mt`          |
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
| Tongan                  | `to`          |
| Turečtina                 | `tr`          |
| Ukrajinština               | `uk`          |
| Urdština                    | `ur`          |
| Vietnamština              | `vi`          |
| Velština                   | `cy`          |
| Yucatec Maya            | `yua`         |

## <a name="next-steps"></a>Další kroky

* [Získejte zkušební předplatné služby Speech Service](https://azure.microsoft.com/try/cognitive-services/)
* [Podívejte se, jak rozpoznat řeč v C #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
