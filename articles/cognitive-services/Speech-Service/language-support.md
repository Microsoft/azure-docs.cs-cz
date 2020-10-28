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
ms.openlocfilehash: a864717304dacaf6cf4c77c92050827af619ed39
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92736688"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Podpora jazyka a hlasu pro službu rozpoznávání řeči

Jazyková podpora se liší podle funkcí služby Speech. Následující tabulka shrnuje jazykovou podporu pro nabídky převodu [řeči na text](#speech-to-text), převod [textu na řeč](#text-to-speech)a [rozpoznávání řeči](#speech-translation) .

## <a name="speech-to-text"></a>Převod řeči na text

Sada Microsoft Speech SDK i REST API podporují následující jazyky (národní prostředí). 

Pro zvýšení přesnosti se přizpůsobení nabídne pro podmnožinu jazyků prostřednictvím nahrávání **zvuku a přepisu s popiskem** nebo **souvisejícího textu: věty** . Další informace o přizpůsobení najdete v tématu [Začínáme s Custom Speech](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Jazyk                          | Národní prostředí | Vlastní nastavení                                   |
|-----------------------------------|--------|--------------------------------------------------|
|Arabština (Bahrajn), moderní Standard  |`ar-BH` | Jazykový model                                   |
|Arabština (Egypt)                     |`ar-EG` | Jazykový model                                   |
|Arabština (Irák)                      |`ar-IQ` | Jazykový model                                   |
|Arabština (Jordánsko)                    |`ar-JO` | Jazykový model                                   |
|Arabština (Kuvajt)                    |`ar-KW` | Jazykový model                                   |
|Arabština (Libanon)                   |`ar-LB` | Jazykový model                                   |
|Arabština (Omán)                      |`ar-OM` | Jazykový model                                   |
|Arabština (Katar)                     |`ar-QA` | Jazykový model                                   |
|Arabština (Saúdská Arábie)              |`ar-SA` | Jazykový model                                   |
|Arabština (Sýrie)                     |`ar-SY` | Jazykový model                                   |
|Arabština (Spojené arabské emiráty)      |`ar-AE` | Jazykový model                                   |
|Bulharština (Bulharsko)               |`bg-BG` | Jazykový model                                   |
|katalánština (Španělsko)                    |`ca-ES` | Jazykový model                                   |
|Čínština (kantonština, tradiční)   |`zh-HK` | Jazykový model                                   |
|Čínština (Mandarin, zjednodušená)     |`zh-CN` | Akustický model<br>Jazykový model                 |
|Čínština (tchajwanský mandarinka)       |`zh-TW` | Jazykový model                                   |
|chorvatština (Chorvatsko)                 |`hr-HR` | Jazykový model                                   |
|čeština (Česká republika)             |`cs-CZ` | Jazykový model                                   |
|dánština (Dánsko)                   |`da-DK` | Jazykový model                                   |
|nizozemština (Nizozemsko)                |`nl-NL` | Jazykový model                                   |
|Angličtina (Austrálie)                |`en-AU` | Akustický model<br>Jazykový model                 |
|Angličtina (Kanada)                   |`en-CA` | Akustický model<br>Jazykový model                 |
|Angličtina (Hongkong)                |`en-HK` | Jazykový model                                   |
|Angličtina (Indie)                    |`en-IN` | Akustický model<br>Jazykový model                 |
|Angličtina (Irsko)                  |`en-IE` | Jazykový model                                   |
|Angličtina (Nový Zéland)              |`en-NZ` | Akustický model<br>Jazykový model                 |
|Angličtina (Filipíny)              |`en-PH` | Jazykový model                                   |
|Angličtina (Singapur)                |`en-SG` | Jazykový model                                   |
|Angličtina (Jihoafrická republika)             |`en-ZA` | Jazykový model                                   |
|Angličtina (Spojené království)           |`en-GB` | Akustický model<br>Jazykový model<br>Výslovnost|
|Angličtina (Spojené státy)            |`en-US` | Akustický model<br>Jazykový model<br>Výslovnost|
|Estonština (Estonsko)                  |`et-EE` | Jazykový model                                   |
|finština (Finsko)                  |`fi-FI` | Jazykový model                                   |
|Francouzština (Kanada)                    |`fr-CA` | Akustický model<br>Jazykový model                 |
|francouzština (Francie)                    |`fr-FR` | Akustický model<br>Jazykový model<br>Výslovnost|
|němčina (Německo)                   |`de-DE` | Akustický model<br>Jazykový model<br>Výslovnost|
|Řečtina (Řecko)                     |`el-GR` | Jazykový model                                   |
|Gudžarátština (Indie)                  |`gu-IN` | Jazykový model                                   |
|hindština (Indie)                      |`hi-IN` | Akustický model<br>Jazykový model                 |
|Maďarština (Maďarsko)                |`hu-HU` | Jazykový model                                   |
|Irština (Irsko)                     |`ga-IE` | Jazykový model                                   |
|italština (Itálie)                    |`it-IT` | Akustický model<br>Jazykový model<br>Výslovnost|
|japonština (Japonsko)                   |`ja-JP` | Jazykový model                                   |
|korejština (Jižní Korea)                     |`ko-KR` | Jazykový model                                   |
|lotyština (Lotyšsko)                   |`lv-LV` | Jazykový model                                   |
|litevština (Litva)             |`lt-LT` | Jazykový model                                   |
|Maltština (Malta)                     |`mt-MT` | Jazykový model                                   |
|Maráthština (Indie)                    |`mr-IN` | Jazykový model                                   |
|Norština (BokmÃ y l) (Norsko)       |`nb-NO` | Jazykový model                                   |
|polština (Polsko)                    |`pl-PL` | Jazykový model                                   |
|Portugalština (Brazílie)                |`pt-BR` | Akustický model<br>Jazykový model<br>Výslovnost|
|portugalština (Portugalsko)              |`pt-PT` | Jazykový model                                   |
|rumunština (Rumunsko)                 |`ro-RO` | Jazykový model                                   |
|ruština (Rusko)                   |`ru-RU` | Akustický model<br>Jazykový model                 |
|slovenština (Slovensko)                  |`sk-SK` | Jazykový model                                   |
|slovinština (Slovinsko)               |`sl-SI` | Jazykový model                                   |
|Španělština (Argentina)                |`es-AR` | Jazykový model                                   |
|Španělština (Bolívie)                  |`es-BO` | Jazykový model                                   |
|Španělština (Chile)                    |`es-CL` | Jazykový model                                   |
|Španělština (Kolumbie)                 |`es-CO` | Jazykový model                                   |
|Španělština (Kostarika)               |`es-CR` | Jazykový model                                   |
|Španělština (Kuba)                     |`es-CU` | Jazykový model                                   |
|Španělština (Dominikánská republika)       |`es-DO` | Jazykový model                                   |
|Španělština (Ekvádor)                  |`es-EC` | Jazykový model                                   |
|Španělština (Salvador)              |`es-SV` | Jazykový model                                   |
|Španělština (Guatemala)                |`es-GT` | Jazykový model                                   |
|Španělština (Honduras)                 |`es-HN` | Jazykový model                                   |
|Španělština (Mexiko)                   |`es-MX` | Akustický model<br>Jazykový model                 |
|Španělština (Nikaragua)                |`es-NI` | Jazykový model                                   |
|Španělština (Panama)                   |`es-PA` | Jazykový model                                   |
|Španělština (Paraguay)                 |`es-PY` | Jazykový model                                   |
|Španělština (Peru)                     |`es-PE` | Jazykový model                                   |
|Španělština (Portoriko)              |`es-PR` | Jazykový model                                   |
|Španělština (Španělsko)                    |`es-ES` | Akustický model<br>Jazykový model                 |
|Španělština (Uruguay)                  |`es-UY` | Jazykový model                                   |
|Španělština (USA)                      |`es-US` | Jazykový model                                   |
|Španělština (Venezuela)                |`es-VE` | Jazykový model                                   |
|švédština (Švédsko)                   |`sv-SE` | Jazykový model                                   |
|Tamilština (Indie)                      |`ta-IN` | Jazykový model                                   |
|Telugština (Indie)                     |`te-IN` | Jazykový model                                   |
|thajština (Thajsko)                    |`th-TH` | Jazykový model                                   |
|turečtina (Turecko)                   |`tr-TR` | Jazykový model                                   |

## <a name="text-to-speech"></a>Převod textu na řeč

Sada Microsoft Speech SDK a rozhraní REST API podporují tyto hlasy, z nichž každý podporuje konkrétní jazyk a dialekt identifikovaný národním prostředím. Můžete také získat úplný seznam jazyků a hlasů podporovaných pro každou konkrétní oblast nebo koncový bod prostřednictvím [rozhraní API pro hlasy a seznam](rest-text-to-speech.md#get-a-list-of-voices). 

> [!IMPORTANT]
> Ceny se liší pro standardní, vlastní a neuronové hlasy. Další informace najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) .

### <a name="neural-voices"></a>Hlasy neuronové

Neuronové převod textu na řeč je nový typ syntézy řeči, který využívá rozsáhlé sítě neuronové. Při použití hlasu neuronové je syntetizované rozpoznávání řeči skoro neodlišitelné od lidských nahrávek.

Neuronové hlasy se dají použít k zajištění většího přirozeného a poutavého působení s chatovacích robotů o a hlasovými asistenty, konverzí digitálních textů, jako jsou například e-knihy, do audiobooks a vylepšení navigačních systémů v klecích. V případě přirozeného Prosody jako přirozeného a jasného kloubování slov, neuronové hlasy významně omezují naslouchat únavu při interakci uživatelů se systémy AI.

Další informace o regionální dostupnosti najdete v tématu [oblasti](regions.md#standard-and-neural-voices).

|Jazyk  | Národní prostředí           | Pohlaví | Název hlasu | Podpora stylů |
|--|--|--|--|--|
| Arabština (Egypt) | `ar-EG` | Female | `ar-EG-SalmaNeural` | Obecné |
| Arabština (Saúdská Arábie) | `ar-SA` | Female | `ar-SA-ZariyahNeural` | Obecné |
| Bulharština (Bulgary) | `bg-BG` <sup>Nová</sup> | Female | `bg-BG-KalinaNeural` | Obecné |
| Kantonština (tradiční čínština, Hongkong) | `zh-HK` | Female | `zh-HK-HiuGaaiNeural` | Obecné |
| katalánština (Španělsko) | `ca-ES` | Female | `ca-ES-AlbaNeural` | Obecné |
| chorvatština (Chorvatsko) | `hr-HR` <sup>Nová</sup> | Female | `hr-HR-GabrijelaNeural` | Obecné |
| Čeština (Čeština) | `cs-CZ` <sup>Nová</sup> | Female | `cs-CZ-VlastaNeural` | Obecné |
| dánština (Dánsko) | `da-DK` | Female | `da-DK-ChristelNeural` | Obecné |
| nizozemština (Nizozemsko) | `nl-NL` | Female | `nl-NL-ColetteNeural` | Obecné |
| Angličtina (Austrálie) | `en-AU` | Female | `en-AU-NatashaNeural` | Obecné |
| Angličtina (Austrálie) | `en-AU` <sup>Nová</sup> | Male | `en-AU-WilliamNeural` | Obecné |
| Angličtina (Kanada) | `en-CA` | Female | `en-CA-ClaraNeural` | Obecné |
| Angličtina (Indie) | `en-IN` | Female | `en-IN-NeerjaNeural` | Obecné |
| Angličtina (Irsko) | `en-IE` <sup>Nová</sup> | Female | `en-IE-EmilyNeural` | Obecné |
| Angličtina (Spojené království) | `en-GB` | Female | `en-GB-LibbyNeural` | Obecné |
| Angličtina (Spojené království) | `en-GB` | Female | `en-GB-MiaNeural` | Obecné |
| Angličtina (Spojené království) | `en-GB` <sup>Nová</sup> | Male | `en-GB-RyanNeural` | Obecné |
| Angličtina (Spojené státy) | `en-US` | Female | `en-US-AriaNeural` | Obecné, více dostupných stylů hlasu |
| Angličtina (Spojené státy) | `en-US` | Male | `en-US-GuyNeural` | Obecné |
| Angličtina (Spojené státy) | `en-US` <sup>Nová</sup> | Female | `en-US-JennyNeural` | Obecné, více dostupných stylů hlasu |
| finština (Finsko) | `fi-FI` | Female | `fi-FI-NooraNeural` | Obecné |
| Francouzština (Kanada) | `fr-CA` | Female | `fr-CA-SylvieNeural` | Obecné |
| Francouzština (Kanada) | `fr-CA` <sup>Nová</sup> | Male | `fr-CA-JeanNeural` | Obecné |
| francouzština (Francie) | `fr-FR` | Female | `fr-FR-DeniseNeural` | Obecné |
| francouzština (Francie) | `fr-FR` <sup>Nová</sup> | Male | `fr-FR-HenriNeural` | Obecné |
| Francouzština (Švýcarsko) | `fr-CH` <sup>Nová</sup> | Female | `fr-CH-ArianeNeural` | Obecné |
| Němčina (Rakousko) | `de-AT` <sup>Nová</sup> | Female | `de-AT-IngridNeural` | Obecné |
| němčina (Německo) | `de-DE` | Female | `de-DE-KatjaNeural` | Obecné |
| němčina (Německo) | `de-DE` <sup>Nová</sup> | Male | `de-DE-ConradNeural` | Obecné |
| Němčina (Švýcarsko) | `de-CH` <sup>Nová</sup> | Female | `de-CH-LeniNeural` | Obecné |
| Řečtina (Řecko) | `el-GR` <sup>Nová</sup> | Female | `el-GR-AthinaNeural` | Obecné |
| Hebrejština (Izrael) | `he-IL` <sup>Nová</sup> | Female | `he-IL-HilaNeural` | Obecné |
| hindština (Indie) | `hi-IN` | Female | `hi-IN-SwaraNeural` | Obecné |
| Maďarština (Maďarsko) | `hu-HU` <sup>Nová</sup> | Female | `hu-HU-NoemiNeural` | Obecné |
| Indonéština (Indonésie) | `id-ID` <sup>Nová</sup> | Male | `id-ID-ArdiNeural` | Obecné |
| italština (Itálie) | `it-IT` | Female | `it-IT-ElsaNeural` | Obecné |
| italština (Itálie) | `it-IT` <sup>Nová</sup> | Female | `it-IT-IsabellaNeural` | Obecné |
| italština (Itálie) | `it-IT` <sup>Nová</sup> | Male | `it-IT-DiegoNeural` | Obecné |
| japonština (Japonsko) | `ja-JP` | Female | `ja-JP-NanamiNeural` | Obecné |
| japonština (Japonsko) | `ja-JP` <sup>Nová</sup> | Male | `ja-JP-KeitaNeural` | Obecné |
| korejština (Jižní Korea) | `ko-KR` | Female | `ko-KR-SunHiNeural` | Obecné |
| korejština (Jižní Korea) | `ko-KR` <sup>Nová</sup> | Male | `ko-KR-InJoonNeural` | Obecné |
| malajština (Malajsie) | `ms-MY` <sup>Nová</sup> | Female | `ms-MY-YasminNeural` | Obecné |
| Mandarinec (zjednodušená čínština, Čína) | `zh-CN` | Female | `zh-CN-XiaoxiaoNeural` | Obecné, více dostupných stylů hlasu |
| Mandarinec (zjednodušená čínština, Čína) | `zh-CN` | Female | `zh-CN-XiaoyouNeural` | Dětský hlas, optimalizovaný pro mluvený popis scénáře |
| Mandarinec (zjednodušená čínština, Čína) | `zh-CN` | Male | `zh-CN-YunyangNeural` | Optimalizováno pro čtení zpráv, je k dispozici více stylů hlasu |
| Mandarinec (zjednodušená čínština, Čína) | `zh-CN` | Male | `zh-CN-YunyeNeural` | Optimalizováno pro mluvený popis scénáře |
| Mandarinec (tradiční čínština, Tchaj-wan) | `zh-TW` | Female | `zh-TW-HsiaoYuNeural` | Obecné |
| Norština, BokmÃa y l (Norsko) | `nb-NO` | Female | `nb-NO-IselinNeural` | Obecné |
| polština (Polsko) | `pl-PL` | Female | `pl-PL-ZofiaNeural` | Obecné |
| Portugalština (Brazílie) | `pt-BR` | Female | `pt-BR-FranciscaNeural` | Obecné, více dostupných stylů hlasu |
| Portugalština (Brazílie) | `pt-BR` <sup>Nová</sup> | Male | `pt-BR-AntonioNeural` | Obecné |
| portugalština (Portugalsko) | `pt-PT` | Female | `pt-PT-FernandaNeural` | Obecné |
| rumunština (Rumunsko) | `ro-RO` <sup>Nová</sup> | Female | `ro-RO-AlinaNeural` | Obecné |
| ruština (Rusko) | `ru-RU` | Female | `ru-RU-DariyaNeural` | Obecné |
| slovenština (Slovensko) | `sk-SK` <sup>Nová</sup> | Female | `sk-SK-ViktoriaNeural` | Obecné |
| slovinština (Slovinsko) | `sl-SI` <sup>Nová</sup> | Female | `sl-SI-PetraNeural` | Obecné |
| Španělština (Mexiko) | `es-MX` | Female | `es-MX-DaliaNeural` | Obecné |
| Španělština (Mexiko) | `es-MX` <sup>Nová</sup> | Male | `es-MX-JorgeNeural` | Obecné |
| Španělština (Španělsko) | `es-ES` | Female | `es-ES-ElviraNeural` | Obecné |
| Španělština (Španělsko) | `es-ES` <sup>Nová</sup> | Male | `es-ES-AlvaroNeural` | Obecné |
| švédština (Švédsko) | `sv-SE` | Female | `sv-SE-HilleviNeural` | Obecné |
| Tamilština (Indie) | `ta-IN` <sup>Nová</sup> | Female | `ta-IN-PallaviNeural` | Obecné |
| Telugština (Indie) | `te-IN` <sup>Nová</sup> | Female | `te-IN-ShrutiNeural` | Obecné |
| thajština (Thajsko) | `th-TH` | Female | `th-TH-AcharaNeural` | Obecné |
| thajština (Thajsko) | `th-TH` <sup>Nová</sup> | Female | `th-TH-PremwadeeNeural` | Obecné |
| turečtina (Turecko) | `tr-TR` | Female | `tr-TR-EmelNeural` | Obecné |
| Vietnamština (Vietnam) | `vi-VN` <sup>Nová</sup> | Female | `vi-VN-HoaiMyNeural` | Obecné|

> [!IMPORTANT]
> `en-US-JessaNeural`Hlas se změnil na `en-US-AriaNeural` . Pokud jste předtím používali "Jessa", převeďte ho na standard ARIA.

Pokud se chcete dozvědět, jak můžete nakonfigurovat a upravit hlasy neuronové, přečtěte si téma věnované [jazykům pro označování řeči](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> V požadavcích na syntézu řeči můžete dál používat úplné mapování názvu služby, jako je například Microsoft Server Speech Převod textu na řeč Voice (EN-US, AriaNeural).

### <a name="standard-voices"></a>Standardní hlasy

Více než 75 standardních hlasů je k dispozici ve více než 45 jazycích a národních prostředích, což vám umožní převést text na syntetizované řeč. Další informace o regionální dostupnosti najdete v tématu [oblasti](regions.md#standard-and-neural-voices).

| Jazyk | Národní prostředí | Pohlaví | Název hlasu |
|--|--|--|--|
| Arabština (arabské písmo)  |  `ar-EG`  |  Female  |  `ar-EG-Hoda`|
| Arabština (Saúdská Arábie)  |  `ar-SA`  |  Male  |  `ar-SA-Naayf`|
| Bulharština (Bulharsko)  |  `bg-BG`  |  Male  |  `bg-BG-Ivan`|
| Kantonština (tradiční čínština, Hongkong)  |  `zh-HK`  |  Male  |  `zh-HK-Danny`|
| Kantonština (tradiční čínština, Hongkong)  |  `zh-HK`  |  Female  |  `zh-HK-TracyRUS`|
| katalánština (Španělsko)  |  `ca-ES`  |  Female  |  `ca-ES-HerenaRUS`|
| chorvatština (Chorvatsko)  |  `hr-HR`  |  Male  |  `hr-HR-Matej`|
| čeština (Česká republika)  |  `cs-CZ`  |  Male  |  `cs-CZ-Jakub`|
| dánština (Dánsko)  |  `da-DK`  |  Female  |  `da-DK-HelleRUS`|
| nizozemština (Nizozemsko)  |  `nl-NL`  |  Female  |  `nl-NL-HannaRUS`|
| Angličtina (Austrálie)  |  `en-AU`  |  Female  |  `en-AU-Catherine`|
| Angličtina (Austrálie)  |  `en-AU`  |  Female  |  `en-AU-HayleyRUS`|
| Angličtina (Kanada)  |  `en-CA`  |  Female  |  `en-CA-HeatherRUS`|
| Angličtina (Kanada)  |  `en-CA`  |  Female  |  `en-CA-Linda`|
| Angličtina (Indie)  |  `en-IN`  |  Female  |  `en-IN-Heera`|
| Angličtina (Indie)  |  `en-IN`  |  Female  |  `en-IN-PriyaRUS`|
| Angličtina (Indie)  |  `en-IN`  |  Male  |  `en-IN-Ravi`|
| Angličtina (Irsko)  |  `en-IE`  |  Male  |  `en-IE-Sean`|
| Angličtina (Spojené království)  |  `en-GB`  |  Male  |  `en-GB-George`|
| Angličtina (Spojené království)  |  `en-GB`  |  Female  |  `en-GB-HazelRUS`|
| Angličtina (Spojené království)  |  `en-GB`  |  Female  |  `en-GB-Susan`|
| Angličtina (Spojené státy)  |  `en-US`  |  Male  |  `en-US-BenjaminRUS`|
| Angličtina (Spojené státy)  |  `en-US`  |  Male  |  `en-US-GuyRUS`|
| Angličtina (Spojené státy)  |  `en-US`  |  Female  |  `en-US-JessaRUS`|
| Angličtina (Spojené státy)  |  `en-US`  |  Female  |  `en-US-ZiraRUS`|
| finština (Finsko)  |  `fi-FI`  |  Female  |  `fi-FI-HeidiRUS`|
| Francouzština (Kanada)  |  `fr-CA`  |  Female  |  `fr-CA-Caroline`|
| Francouzština (Kanada)  |  `fr-CA`  |  Female  |  `fr-CA-HarmonieRUS`|
| francouzština (Francie)  |  `fr-FR`  |  Female  |  `fr-FR-HortenseRUS`|
| francouzština (Francie)  |  `fr-FR`  |  Female  |  `fr-FR-Julie`|
| francouzština (Francie)  |  `fr-FR`  |  Male  |  `fr-FR-Paul`|
| Francouzština (Švýcarsko)  |  `fr-CH`  |  Male  |  `fr-CH-Guillaume`|
| Němčina (Rakousko)  |  `de-AT`  |  Male  |  `de-AT-Michael`|
| němčina (Německo)  |  `de-DE`  |  Female  |  `de-DE-HeddaRUS`|
| němčina (Německo)  |  `de-DE`  |  Male  |  `de-DE-Stefan`|
| Němčina (Švýcarsko)  |  `de-CH`  |  Male  |  `de-CH-Karsten`|
| Řečtina (Řecko)  |  `el-GR`  |  Male  |  `el-GR-Stefanos`|
| Hebrejština (Izrael)  |  `he-IL`  |  Male  |  `he-IL-Asaf`|
| hindština (Indie)  |  `hi-IN`  |  Male  |  `hi-IN-Hemant`|
| hindština (Indie)  |  `hi-IN`  |  Female  |  `hi-IN-Kalpana`|
| Maďarština (Maďarsko)  |  `hu-HU`  |  Male  |  `hu-HU-Szabolcs`|
| Indonéština (Indonésie)  |  `id-ID`  |  Male  |  `id-ID-Andika`|
| italština (Itálie)  |  `it-IT`  |  Male  |  `it-IT-Cosimo`|
| italština (Itálie)  |  `it-IT`  |  Female  |  `it-IT-LuciaRUS`|
| japonština (Japonsko)  |  `ja-JP`  |  Female  |  `ja-JP-Ayumi`|
| japonština (Japonsko)  |  `ja-JP`  |  Female  |  `ja-JP-HarukaRUS`|
| japonština (Japonsko)  |  `ja-JP`  |  Male  |  `ja-JP-Ichiro`|
| korejština (Jižní Korea)  |  `ko-KR`  |  Female  |  `ko-KR-HeamiRUS`|
| malajština (Malajsie)  |  `ms-MY`  |  Male  |  `ms-MY-Rizwan`|
| Mandarinec (zjednodušená čínština, Čína)  |  `zh-CN`  |  Female  |  `zh-CN-HuihuiRUS`|
| Mandarinec (zjednodušená čínština, Čína)  |  `zh-CN`  |  Male  |  `zh-CN-Kangkang`|
| Mandarinec (zjednodušená čínština, Čína)  |  `zh-CN`  |  Female  |  `zh-CN-Yaoyao`|
| Mandarinec (tradiční čínština, Tchaj-wan)  |  `zh-TW`  |  Female  |  `zh-TW-HanHanRUS`|
| Mandarinec (tradiční čínština, Tchaj-wan)  |  `zh-TW`  |  Female  |  `zh-TW-Yating`|
| Mandarinec (tradiční čínština, Tchaj-wan)  |  `zh-TW`  |  Male  |  `zh-TW-Zhiwei`|
| Norština, BokmÃa y l (Norsko)  |  `nb-NO`  |  Female  |  `nb-NO-HuldaRUS`|
| polština (Polsko)  |  `pl-PL`  |  Female  |  `pl-PL-PaulinaRUS`|
| Portugalština (Brazílie)  |  `pt-BR`  |  Male  |  `pt-BR-Daniel`|
| Portugalština (Brazílie)  |  `pt-BR`  |  Female  |  `pt-BR-HeloisaRUS`|
| portugalština (Portugalsko)  |  `pt-PT`  |  Female  |  `pt-PT-HeliaRUS`|
| rumunština (Rumunsko)  |  `ro-RO`  |  Male  |  `ro-RO-Andrei`|
| ruština (Rusko)  |  `ru-RU`  |  Female  |  `ru-RU-EkaterinaRUS`|
| ruština (Rusko)  |  `ru-RU`  |  Female  |  `ru-RU-Irina`|
| ruština (Rusko)  |  `ru-RU`  |  Male  |  `ru-RU-Pavel`|
| slovenština (Slovensko)  |  `sk-SK`  |  Male  |  `sk-SK-Filip`|
| slovinština (Slovinsko)  |  `sl-SI`  |  Male  |  `sl-SI-Lado`|
| Španělština (Mexiko)  |  `es-MX`  |  Female  |  `es-MX-HildaRUS`|
| Španělština (Mexiko)  |  `es-MX`  |  Male  |  `es-MX-Raul`|
| Španělština (Španělsko)  |  `es-ES`  |  Female  |  `es-ES-HelenaRUS`|
| Španělština (Španělsko)  |  `es-ES`  |  Female  |  `es-ES-Laura`|
| Španělština (Španělsko)  |  `es-ES`  |  Male  |  `es-ES-Pablo`|
| švédština (Švédsko)  |  `sv-SE`  |  Female  |  `sv-SE-HedvigRUS`|
| Tamilština (Indie)  |  `ta-IN`  |  Male  |  `ta-IN-Valluvar`|
| Telugština (Indie)  |  `te-IN`  |  Female  |  `te-IN-Chitra`|
| thajština (Thajsko)  |  `th-TH`  |  Male  |  `th-TH-Pattara`|
| turečtina (Turecko)  |  `tr-TR`  |  Female  |  `tr-TR-SedaRUS`|
| Vietnamština (Vietnam)  |  `vi-VN`  |  Male  |  `vi-VN-An`  |


> [!IMPORTANT]
> `en-US-Jessa`Hlas se změnil na `en-US-Aria` . Pokud jste předtím používali "Jessa", převeďte ho na standard ARIA.

> [!TIP]
> V požadavcích na syntézu řeči můžete dál používat úplné mapování názvu služby, jako je například Microsoft Server Speech Převod textu na řeč Voice (EN-US, AriaRUS).

### <a name="customization"></a>Přizpůsobení

Vlastní nastavení hlasu je k dispozici pro `de-DE` , `en-GB` , `en-IN` , `en-US` , `es-MX` , `fr-FR` , `it-IT` , `pt-BR` a `zh-CN` . Vyberte správné národní prostředí, které odpovídá školicím datům, které máte k dispozici pro výuku vlastního hlasového modelu. Například pokud jsou data nahrávání mluveného mluveného zvýraznění v angličtině, vyberte `en-GB` .

> [!NOTE]
> V rámci vlastního hlasu nepodporujeme školicí modely pro BI, s výjimkou Chinese-English BI-vícejazyčná. Pokud chcete naučit čínský hlas, který může mluvit i v angličtině, vyberte možnost dvojjazyčně v angličtině. Hlasové školení ve všech národních prostředích začíná sadou dat 2000 + projevy, s výjimkou `en-US` a `zh-CN` kde můžete začít s jakoukoli velikostí školicích dat.

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
| Klingon                 | `tlh-Latn`    |
| Klingon (plqaD)         | `tlh-Piqd`    |
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

## <a name="speaker-recognition"></a>Rozpoznávání mluvčího

V následující tabulce najdete podporované jazyky pro různá rozhraní API rozpoznávání mluvčího. Další informace o rozpoznávání mluvčího najdete v tématu [Přehled](speaker-recognition-overview.md) .

| Jazyk | Národní prostředí | Ověřování závislé na textu | Ověřování nezávislé na textu | Identifikace nezávislá na textu |
|----|----|----|----|----|
|Angličtina (USA)  |  en-US  |  ano  |  ano  |  ano |
|Čínština (Mandarin, zjednodušená) | zh-CN     |     neuvedeno |     ano |     ano|
|Angličtina (Austrálie)     | EN-AU     | neuvedeno     | ano     | ano|
|Angličtina (Kanada)     | en-CA     | neuvedeno |     ano |     ano|
|Angličtina (Spojené království)     | en-GB     | neuvedeno     | ano     | ano|
|Francouzština (Kanada)     | fr – CA     | neuvedeno     | ano |     ano|
|francouzština (Francie)     | fr-FR     | neuvedeno     | ano     | ano|
|němčina (Německo)     | de-DE     | neuvedeno     | ano     | ano|
|Italština | it-IT     |     neuvedeno     | ano |     ano|
|Japonština     | ja-JP | neuvedeno     | ano     | ano|
|Portugalština (Brazílie) | pt-BR |     neuvedeno |     ano |     ano|
|Španělština (Mexiko)     | ES – MX     | neuvedeno |     ano |     ano|
|Španělština (Španělsko)     | es-ES | neuvedeno     | ano |     ano|

## <a name="next-steps"></a>Další kroky

* [Vytvoření bezplatného účtu Azure](https://azure.microsoft.com/free/cognitive-services/)
* [Přečtěte si téma rozpoznávání řeči v jazyce C. #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
