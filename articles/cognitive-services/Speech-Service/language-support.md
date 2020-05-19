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
ms.openlocfilehash: 3fe1b999fbdc03157778a1329e05e8c342183528
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587370"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Podpora jazyka a hlasu pro službu rozpoznávání řeči

Jazyková podpora se liší podle funkcí služby Speech. Následující tabulka shrnuje jazykovou podporu pro nabídky převodu [řeči na text](#speech-to-text), převod [textu na řeč](#text-to-speech)a [rozpoznávání řeči](#speech-translation) .

## <a name="speech-to-text"></a>Převod řeči na text

Sada Microsoft Speech SDK i REST API podporují následující jazyky (národní prostředí). Pro zvýšení přesnosti se přizpůsobení nabídne pro podmnožinu jazyků prostřednictvím nahrávání zvuku a přepisu s popiskem nebo souvisejícího textu: věty. Přizpůsobení výslovnosti je aktuálně k dispozici pouze pro `en-US` a `de-DE` . Další informace o přizpůsobení [najdete tady](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Národní prostředí  | Jazyk                          | Podporuje se | Vlastní nastavení                                    |
|---------|-----------------------------------|-----------|---------------------------------------------------|
| `ar-AE` | Arabština (Spojené arabské emiráty)                      | Ano       | Ne                                                |
| `ar-BH` | Arabština (Bahrajn), moderní Standard | Yes       | Jazykový model                                    |
| `ar-EG` | Arabština (Egypt)                    | Yes       | Jazykový model                                    |
| `ar-KW` | Arabština (Kuvajt)                   | Ano       | Ne                                                |
| `ar-QA` | Arabština (Katar)                    | Ano       | Ne                                                |
| `ar-SA` | Arabština (Saúdská Arábie)             | Ano       | Ne                                                |
| `ar-SY` | Arabština (Sýrie)                    | Yes       | Jazykový model                                    |
| `ca-ES` | Katalánština                           | Yes       | Jazykový model                                    |
| `da-DK` | dánština (Dánsko)                  | Yes       | Jazykový model                                    |
| `de-DE` | němčina (Německo)                  | Yes       | Akustický model<br>Jazykový model<br>Výslovnost |
| `en-AU` | Angličtina (Austrálie)               | Yes       | Akustický model<br>Jazykový model                  |
| `en-CA` | Angličtina (Kanada)                  | Yes       | Akustický model<br>Jazykový model                  |
| `en-GB` | Angličtina (Spojené království)          | Yes       | Akustický model<br>Jazykový model<br>Výslovnost |
| `en-IN` | Angličtina (Indie)                   | Yes       | Akustický model<br>Jazykový model                  |
| `en-NZ` | Angličtina (Nový Zéland)             | Yes       | Akustický model<br>Jazykový model                  |
| `en-US` | Angličtina (Spojené státy)           | Yes       | Akustický model<br>Jazykový model<br>Výslovnost |
| `es-ES` | Španělština (Španělsko)                   | Yes       | Akustický model<br>Jazykový model                  |
| `es-MX` | Španělština (Mexiko)                  | Yes       | Akustický model<br>Jazykový model                  |
| `fi-FI` | finština (Finsko)                 | Yes       | Jazykový model                                    |
| `fr-CA` | Francouzština (Kanada)                   | Yes       | Akustický model<br>Jazykový model                  |
| `fr-FR` | francouzština (Francie)                   | Yes       | Akustický model<br>Jazykový model<br>Výslovnost |
| `gu-IN` | Gudžarátština (Indie)                 | Yes       | Jazykový model                                    |
| `hi-IN` | hindština (Indie)                     | Yes       | Akustický model<br>Jazykový model                  |
| `it-IT` | italština (Itálie)                   | Yes       | Akustický model<br>Jazykový model<br>Výslovnost |
| `ja-JP` | japonština (Japonsko)                  | Yes       | Jazykový model                                    |
| `ko-KR` | korejština (Jižní Korea)                    | Yes       | Jazykový model                                    |
| `mr-IN` | Maráthština (Indie)                   | Yes       | Jazykový model                                    |
| `nb-NO` | Norština (Bokmål) (Norsko)       | Yes       | Jazykový model                                    |
| `nl-NL` | nizozemština (Nizozemsko)               | Yes       | Jazykový model                                    |
| `pl-PL` | polština (Polsko)                   | Yes       | Jazykový model                                    |
| `pt-BR` | Portugalština (Brazílie)               | Yes       | Akustický model<br>Jazykový model<br>Výslovnost |
| `pt-PT` | portugalština (Portugalsko)             | Yes       | Jazykový model                                    |
| `ru-RU` | ruština (Rusko)                  | Yes       | Akustický model<br>Jazykový model                  |
| `sv-SE` | švédština (Švédsko)                  | Yes       | Jazykový model                                    |
| `ta-IN` | Tamilština (Indie)                     | Yes       | Jazykový model                                    |
| `te-IN` | Telugština (Indie)                    | Ano       | Ne                                                |
| `th-TH` | thajština (Thajsko)                   | Ano       | Ne                                                |
| `tr-TR` | turečtina (Turecko)                  | Ano       | Ne                                                |
| `zh-CN` | Čínština (Mandarin, zjednodušená)    | Yes       | Akustický model<br>Jazykový model                  |
| `zh-HK` | Čínština (kantonština, tradiční)  | Yes       | Jazykový model                                    |
| `zh-TW` | Čínština (tchajwanský mandarinka)      | Yes       | Jazykový model                                    |

## <a name="text-to-speech"></a>Převod textu na řeč

Sada Microsoft Speech SDK a rozhraní REST API podporují tyto hlasy, z nichž každý podporuje konkrétní jazyk a dialekt identifikovaný národním prostředím. Můžete také získat úplný seznam jazyků a hlasů podporovaných pro každou konkrétní oblast nebo koncový bod prostřednictvím [rozhraní API pro hlasy a seznam](rest-text-to-speech.md#get-a-list-of-voices). 

> [!IMPORTANT]
> Ceny se liší pro standardní, vlastní a neuronové hlasy. Další informace najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) .

### <a name="neural-voices"></a>Hlasy neuronové

Neuronové převod textu na řeč je nový typ syntézy řeči, který využívá rozsáhlé sítě neuronové. Při použití hlasu neuronové je syntetizované rozpoznávání řeči skoro neodlišitelné od lidských nahrávek.

Neuronové hlasy se dají použít k zajištění většího přirozeného a poutavého působení s chatovacích robotů o a hlasovými asistenty, konverzí digitálních textů, jako jsou například e-knihy, do audiobooks a vylepšení navigačních systémů v klecích. V případě přirozeného Prosody jako přirozeného a jasného kloubování slov, neuronové hlasy významně omezují naslouchat únavu při interakci uživatelů se systémy AI.

Další informace o regionální dostupnosti najdete v tématu [oblasti](regions.md#standard-and-neural-voices).

|Národní prostředí  | Jazyk            | Pohlaví | Název hlasu  | Podpora pro BI | Podpora stylů |
|--|--|--|--|--|--|
| `de-DE` | němčina (Německo)    | Female | de-DE-KatjaNeural " | Yes. Angličtina (USA) | Obecné |
| `en-AU` | Angličtina (Austrálie) | Female |  "en-AU-NatashaNeural" | No | Obecné |
| `en-CA` | Angličtina (Kanada)    | Female |  "en-CA-ClaraNeural"| No | Obecné |
| `en-GB` | Angličtina (Spojené království)        | Female |  "en-GB-LibbyNeural"| No | Obecné |
|  |      | Female |  "en-GB-MiaNeural" | No | Obecné |
| `en-US` | Angličtina (USA)        | Female |  "en-US-AriaNeural"| No | Obecné, více dostupných stylů hlasu |
|  |      | Male   | "en-US-GuyNeural" | No | Obecné |
| `es-ES` | Španělština (Španělsko)     | Female |  ES-ES-ElviraNeural "| Yes. Angličtina (USA) | Obecné |
| `es-MX` | Španělština (Mexiko)    | Female |  ES-MX-DaliaNeural | Yes. Angličtina (USA) | Obecné |
| `fr-CA` | Francouzština (Kanada)     | Female |  "fr-CA-SylvieNeural" | Yes. Angličtina (USA) | Obecné |
| `fr-FR` | francouzština (Francie)     | Female | "fr-FR-DeniseNeural"  | Yes. Angličtina (USA) | Obecné |
| `it-IT` | italština (Itálie)     | Female |  "IT-IT-ElsaNeural"  | Yes. Angličtina (USA) | Obecné |
| `ja-JP` | Japonština            | Female |  "ja-JP-NanamiNeural" | Yes. Angličtina (USA) | Obecné |
| `ko-KR` | Korejština              | Female |  "ko-KR-SunHiNeural" | Yes. Angličtina (USA) | Obecné |
| `nb-NO` | Norština           | Female | "NB-NO-IselinNeural" | No | Obecné |
| `pt-BR` | Portugalština (Brazílie) | Female |  pt-BR-FranciscaNeural | No | Obecné |
| `tr-TR` | Turečtina             | Female | "tr-TR-EmelNeural" | No | Obecné |
| `zh-CN` | Čínština (Mandarin, zjednodušená)  | Female |  zh-CN-XiaoxiaoNeural "  | Yes. Angličtina (USA) | Obecné, více dostupných stylů hlasu |
|  |  | Female |  zh-CN-XiaoyouNeural "  | Yes. Angličtina (USA) | Dětský hlas, optimalizovaný pro mluvený popis scénáře |
|  |  | Male |  zh-CN-YunyangNeural "  | Yes. Angličtina (USA) | Optimalizováno pro čtení zpráv, je k dispozici více stylů hlasu |
|  |  | Male |  zh-CN-YunyeNeural "  | No | Optimalizováno pro mluvený popis scénáře |

> [!IMPORTANT]
> `en-US-JessaNeural`Hlas se změnil na `en-US-AriaNeural` . Pokud jste předtím používali "Jessa", převeďte ho na standard ARIA.

Pokud se chcete dozvědět, jak můžete nakonfigurovat a upravit hlasy neuronové, přečtěte si téma věnované [jazykům pro označování řeči](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> V požadavcích na syntézu řeči můžete dál používat úplné mapování názvu služby, jako je například Microsoft Server Speech Převod textu na řeč Voice (EN-US, AriaNeural).

### <a name="standard-voices"></a>Standardní hlasy

Více než 75 standardních hlasů je k dispozici ve více než 45 jazycích a národních prostředích, což vám umožní převést text na syntetizované řeč. Další informace o regionální dostupnosti najdete v tématu [oblasti](regions.md#standard-and-neural-voices).

| Národní prostředí | Jazyk | Pohlaví | Název hlasu |
|--|--|--|--|
| <sup>první</sup>`ar-EG` | Arabština (Egypt) | Female | ar-EG-Hoda |
| `ar-SA` | Arabština (Saúdská Arábie) | Male | ar-SA-Naayf " |
| `bg-BG` | Bulharština | Male |  BG-BG-Ivan |
| `ca-ES` | katalánština (Španělsko) | Female |  "Ca-ES-HerenaRUS" |
| `cs-CZ` | Čeština | Male | cs-CZ-Jakub " |
| `da-DK` | Dánština | Female |  "da-DK-HelleRUS" |
| `de-AT` | Němčina (Rakousko) | Male | de-AT-Michael |
| `de-CH` | Němčina (Švýcarsko) | Male |  de-CH-Karsten |
| `de-DE` | němčina (Německo) | Female |  de-DE-Hedda " |
|  |  | Female | de-DE-HeddaRUS " |
|  |  | Male |  de-DE-Stefan-Apollo " |
| `el-GR` | Řečtina | Male | "El-GR-Stefanos" |
| `en-AU` | Angličtina (Austrálie) | Female |  "en-AU-Catherine" |
|  |  | Female |  "en-AU-HayleyRUS" |
| `en-CA` | Angličtina (Kanada) | Female |  "en-CA-Linda" |
|  |  | Female |  "en-CA-HeatherRUS" |
| `en-GB` | Angličtina (Spojené království) | Female |  "en-GB-Zuzana-Apollo" |
|  |  | Female |  "en-GB-HazelRUS" |
|  |  | Male |  "en-GB-Jiří-Apollo" |
| `en-IE` | Angličtina (Irsko) | Male | "en-IE-Novák" |
| `en-IN` | Angličtina (Indie) | Female | "en-IN-Heera-Apollo" |
|  |  | Female |  "en-IN-PriyaRUS" |
|  |  | Male |  "en-IN-Ravi-Apollo" |
| `en-US` | Angličtina (USA) | Female |  "en-US-ZiraRUS" |
|  |  | Female | "en-US-AriaRUS" |
|  |  | Male | "en-US-BenjaminRUS" |
|  |  | Male |  "en-US-Guy24kRUS" |
| `es-ES` | Španělština (Španělsko) | Female |  ES-ES-Laura-Apollo " |
|  |  | Female | ES-ES-HelenaRUS " |
|  |  | Male | ES-ES-Pablo-Apollo |
| `es-MX` | Španělština (Mexiko) | Female |  ES-MX-HildaRUS |
|  |  | Male | ES-MX-Raul-Apollo |
| `fi-FI` | Finština | Female | "Fi-FI-HeidiRUS" |
| `fr-CA` | Francouzština (Kanada) | Female | "fr-CA-Caroline" |
|  |  | Female | "fr-CA-HarmonieRUS" |
| `fr-CH` | Francouzština (Švýcarsko) | Male | "fr-CH-Guillaume" |
| `fr-FR` | francouzština (Francie) | Female |  "fr-FR-Julie-Apollo" |
|  |  | Female |"fr-FR-HortenseRUS" |
|  |  | Male |  "fr-FR-Paul-Apollo" |
| `he-IL` | Hebrejština (Izrael) | Male |  "he-IL-Asaf" |
| `hi-IN` | hindština (Indie) | Female | "Hi-IN-Kalpana-Apollo" |
|  |  | Female |  "Dobrý den-Kalpana" |
|  |  | Male |  "Dobrý den-Hemant" |
| `hr-HR` | Chorvatština | Male | "HR-HR-Matej" |
| `hu-HU` | Maďarština | Male |  "hu-HU-Szabolcs" |
| `id-ID` | Indonéština | Male | ID-ID-Andika |
| `it-IT` | Italština | Male |  "IT-IT-Cosimo-Apollo" |
|  |  | Female |  "IT-IT-LuciaRUS" |
| `ja-JP` | Japonština | Female |  "ja-JP-Ayumi-Apollo" |
|  |  | Male | "ja-JP-Ichiro-Apollo" |
|  |  | Female |  "ja-JP-HarukaRUS" |
| `ko-KR` | Korejština | Female | "ko-KR-HeamiRUS" |
| `ms-MY` | Malajština | Male |  "MS-MY-Rizwan" |
| `nb-NO` | Norština | Female |  "NB-NO-HuldaRUS" |
| `nl-NL` | Nizozemština | Female |  nl-NL-HannaRUS " |
| `pl-PL` | Polština | Female |  pl-PL-PaulinaRUS " |
| `pt-BR` | Portugalština (Brazílie) | Female | pt-BR-HeloisaRUS |
|  |  | Male |  pt-BR-Daniel-Apollo |
| `pt-PT` | portugalština (Portugalsko) | Female | pt-PT-HeliaRUS |
| `ro-RO` | Rumunština | Male | "RO-RO-Andrei" |
| `ru-RU` | Ruština | Female |  ru-RU-Irina-Apollo " |
|  |  | Male | ru-RU-Pavel-Apollo " |
|  |  | Female |  ru-RU – EkaterinaRUS |
| `sk-SK` | Slovenština | Male | "SK-SK-Filip" |
| `sl-SI` | Slovinština | Male |  "SL-SI-lado" |
| `sv-SE` | Švédština | Female | "sv-SE-HedvigRUS" |
| `ta-IN` | Tamilština (Indie) | Male |  "ta-IN-Valluvar" |
| `te-IN` | Telugština (Indie) | Female |  "te-IN-Chitra" |
| `th-TH` | Thajština | Male |  "th-TH-Pattara" |
| `tr-TR` | turečtina (Turecko) | Female | "tr-TR-SedaRUS" |
| `vi-VN` | Vietnamština | Male |  "VI-VN-a" |
| `zh-CN` | Čínština (Mandarin, zjednodušená) | Female |  zh-CN-HuihuiRUS " |
|  |  | Female | zh-CN-Yaoyao-Apollo " |
|  |  | Male | zh-CN-Kangkang-Apollo " |
| `zh-HK` | Čínština (kantonština, tradiční) | Female |  "zh-HK-Tracy-Apollo" |
|  |  | Female | "zh-HK-TracyRUS" |
|  |  | Male |  "zh-HK-Danny-Apollo" |
| `zh-TW` | Čínština (tchajwanský mandarinka) | Female |  "zh-TW-Yating-Apollo" |
|  |  | Female | "zh-TW-HanHanRUS" |
|  |  | Male |  "zh-TW-Zhiwei-Apollo" |

**1** *ar-eg podporuje moderní arabštinu Standard (MSA).*

> [!IMPORTANT]
> `en-US-Jessa`Hlas se změnil na `en-US-Aria` . Pokud jste předtím používali "Jessa", převeďte ho na standard ARIA.

> [!TIP]
> V požadavcích na syntézu řeči můžete dál používat úplné mapování názvu služby, jako je například Microsoft Server Speech Převod textu na řeč Voice (EN-US, AriaRUS).

### <a name="customization"></a>Přizpůsobení

Vlastní nastavení hlasu je k dispozici pro `de-DE` , `en-GB` , `en-IN` , `en-US` , `es-MX` , `fr-FR` , `it-IT` , `pt-BR` a `zh-CN` . Vyberte správné národní prostředí, které odpovídá školicím datům, které máte k dispozici pro výuku vlastního hlasového modelu. Například pokud jsou data nahrávání mluveného mluveného zvýraznění v angličtině, vyberte `en-GB` .

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
