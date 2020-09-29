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
ms.openlocfilehash: e0d6694d44fdac9958acdf70be067348fadde57f
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91461236"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Podpora jazyka a hlasu pro službu rozpoznávání řeči

Jazyková podpora se liší podle funkcí služby Speech. Následující tabulka shrnuje jazykovou podporu pro nabídky převodu [řeči na text](#speech-to-text), převod [textu na řeč](#text-to-speech)a [rozpoznávání řeči](#speech-translation) .

## <a name="speech-to-text"></a>Převod řeči na text

Sada Microsoft Speech SDK i REST API podporují následující jazyky (národní prostředí). 

Pro zvýšení přesnosti se přizpůsobení nabídne pro podmnožinu jazyků prostřednictvím nahrávání **zvuku a přepisu s popiskem** nebo **souvisejícího textu: věty**. Další informace o přizpůsobení najdete v tématu [Začínáme s Custom Speech](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Národní prostředí  | Jazyk                          | Vlastní nastavení                                    |
|---------|-----------------------------------|---------------------------------------------------|
| `ar-AE` | Arabština (Spojené arabské emiráty)     | Jazykový model                                    |
| `ar-BH` | Arabština (Bahrajn), moderní Standard | Jazykový model                                    |
| `ar-EG` | Arabština (Egypt)                    | Jazykový model                                    |
| `ar-IQ` | Arabština (Irák)                     | Jazykový model                                    |
| `ar-JO` | Arabština (Jordánsko)                   | Jazykový model                                    |
| `ar-KW` | Arabština (Kuvajt)                   | Jazykový model                                    |
| `ar-LB` | Arabština (Libanon)                  | Jazykový model                                    |
| `ar-OM` | Arabština (Omán)                     | Jazykový model                                    |
| `ar-QA` | Arabština (Katar)                    | Jazykový model                                    |
| `ar-SA` | Arabština (Saúdská Arábie)             | Jazykový model                                    |
| `ar-SY` | Arabština (Sýrie)                    | Jazykový model                                    |
| `bg-BG` | Bulharština (Bulharsko)              | Jazykový model                                    |
| `ca-ES` | katalánština (Španělsko)                   | Jazykový model                                    |
| `cs-CZ` | čeština (Česká republika)            | Jazykový model                                    | 
| `da-DK` | dánština (Dánsko)                  | Jazykový model                                    |
| `de-DE` | němčina (Německo)                  | Akustický model<br>Jazykový model<br>Výslovnost |
| `el-GR` | Řečtina (Řecko)                    | Jazykový model                                    |
| `en-AU` | Angličtina (Austrálie)               | Akustický model<br>Jazykový model                  |
| `en-CA` | Angličtina (Kanada)                  | Akustický model<br>Jazykový model                  |
| `en-GB` | Angličtina (Spojené království)          | Akustický model<br>Jazykový model<br>Výslovnost |
| `en-HK` | Angličtina (Hongkong)               | Jazykový model                                    | 
| `en-IE` | Angličtina (Irsko)                 | Jazykový model                                    | 
| `en-IN` | Angličtina (Indie)                   | Akustický model<br>Jazykový model                  |
| `en-NZ` | Angličtina (Nový Zéland)             | Akustický model<br>Jazykový model                  |
| `en-PH` | Angličtina (Filipíny)             | Jazykový model                                    | 
| `en-SG` | Angličtina (Singapur)               | Jazykový model                                    | 
| `en-US` | Angličtina (Spojené státy)           | Akustický model<br>Jazykový model<br>Výslovnost |
| `en-ZA` | Angličtina (Jihoafrická republika)            | Jazykový model                                    | 
| `es-AR` | Španělština (Argentina)               | Jazykový model                                    | 
| `es-BO` | Španělština (Bolívie)                 | Jazykový model                                    | 
| `es-CL` | Španělština (Chile)                   | Jazykový model                                    | 
| `es-CO` | Španělština (Kolumbie)                | Jazykový model                                    | 
| `es-CR` | Španělština (Kostarika)              | Jazykový model                                    | 
| `es-CU` | Španělština (Kuba)                    | Jazykový model                                    | 
| `es-DO` | Španělština (Dominikánská republika)      | Jazykový model                                    | 
| `es-EC` | Španělština (Ekvádor)                 | Jazykový model                                    | 
| `es-ES` | Španělština (Španělsko)                   | Akustický model<br>Jazykový model                  |
| `es-GT` | Španělština (Guatemala)               | Jazykový model                                    | 
| `es-HN` | Španělština (Honduras)                | Jazykový model                                    | 
| `es-MX` | Španělština (Mexiko)                  | Akustický model<br>Jazykový model                  |
| `es-NI` | Španělština (Nikaragua)               | Jazykový model                                    | 
| `es-PA` | Španělština (Panama)                  | Jazykový model                                    | 
| `es-PE` | Španělština (Peru)                    | Jazykový model                                    | 
| `es-PR` | Španělština (Portoriko)             | Jazykový model                                    | 
| `es-PY` | Španělština (Paraguay)                | Jazykový model                                    | 
| `es-SV` | Španělština (Salvador)             | Jazykový model                                    | 
| `es-US` | Španělština (USA)                     | Jazykový model                                    | 
| `es-UY` | Španělština (Uruguay)                 | Jazykový model                                    | 
| `es-VE` | Španělština (Venezuela)               | Jazykový model                                    |
| `et-EE` | Estonština (Estonsko)                 | Jazykový model                                    | 
| `fi-FI` | finština (Finsko)                 | Jazykový model                                    |
| `fr-CA` | Francouzština (Kanada)                   | Akustický model<br>Jazykový model                  |
| `fr-FR` | francouzština (Francie)                   | Akustický model<br>Jazykový model<br>Výslovnost |
| `ga-IE` | Irština (Irsko)                    | Jazykový model                                    |
| `gu-IN` | Gudžarátština (Indie)                 | Jazykový model                                    |
| `hi-IN` | hindština (Indie)                     | Akustický model<br>Jazykový model                  |
| `hr-HR` | chorvatština (Chorvatsko)                | Jazykový model                                    |
| `hu-HU` | Maďarština (Maďarsko)               | Jazykový model                                    | 
| `it-IT` | italština (Itálie)                   | Akustický model<br>Jazykový model<br>Výslovnost |
| `ja-JP` | japonština (Japonsko)                  | Jazykový model                                    |
| `ko-KR` | korejština (Jižní Korea)                    | Jazykový model                                    |
| `lt-LT` | litevština (Litva)            | Jazykový model                                    |
| `lv-LV` | lotyština (Lotyšsko)                  | Jazykový model                                    |
| `mr-IN` | Maráthština (Indie)                   | Jazykový model                                    |
| `mt-MT` | Maltština (Malta)                    | Jazykový model                                    |
| `nb-NO` | Norština (Bokmål) (Norsko)       | Jazykový model                                    |
| `nl-NL` | nizozemština (Nizozemsko)               | Jazykový model                                    |
| `pl-PL` | polština (Polsko)                   | Jazykový model                                    |
| `pt-BR` | Portugalština (Brazílie)               | Akustický model<br>Jazykový model<br>Výslovnost |
| `pt-PT` | portugalština (Portugalsko)             | Jazykový model                                    |
| `ro-RO` | rumunština (Rumunsko)                | Jazykový model                                    |
| `ru-RU` | ruština (Rusko)                  | Akustický model<br>Jazykový model                  |
| `sk-SK` | slovenština (Slovensko)                 | Jazykový model                                    |
| `sl-SI` | slovinština (Slovinsko)              | Jazykový model                                    |
| `sv-SE` | švédština (Švédsko)                  | Jazykový model                                    |
| `ta-IN` | Tamilština (Indie)                     | Jazykový model                                    |
| `te-IN` | Telugština (Indie)                    | Jazykový model                                    |
| `th-TH` | thajština (Thajsko)                   | Jazykový model                                    |
| `tr-TR` | turečtina (Turecko)                  | Jazykový model                                    |
| `zh-CN` | Čínština (Mandarin, zjednodušená)    | Akustický model<br>Jazykový model                  |
| `zh-HK` | Čínština (kantonština, tradiční)  | Jazykový model                                    |
| `zh-TW` | Čínština (tchajwanský mandarinka)      | Jazykový model                                    |

## <a name="text-to-speech"></a>Převod textu na řeč

Sada Microsoft Speech SDK a rozhraní REST API podporují tyto hlasy, z nichž každý podporuje konkrétní jazyk a dialekt identifikovaný národním prostředím. Můžete také získat úplný seznam jazyků a hlasů podporovaných pro každou konkrétní oblast nebo koncový bod prostřednictvím [rozhraní API pro hlasy a seznam](rest-text-to-speech.md#get-a-list-of-voices). 

> [!IMPORTANT]
> Ceny se liší pro standardní, vlastní a neuronové hlasy. Další informace najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) .

### <a name="neural-voices"></a>Hlasy neuronové

Neuronové převod textu na řeč je nový typ syntézy řeči, který využívá rozsáhlé sítě neuronové. Při použití hlasu neuronové je syntetizované rozpoznávání řeči skoro neodlišitelné od lidských nahrávek.

Neuronové hlasy se dají použít k zajištění většího přirozeného a poutavého působení s chatovacích robotů o a hlasovými asistenty, konverzí digitálních textů, jako jsou například e-knihy, do audiobooks a vylepšení navigačních systémů v klecích. V případě přirozeného Prosody jako přirozeného a jasného kloubování slov, neuronové hlasy významně omezují naslouchat únavu při interakci uživatelů se systémy AI.

Další informace o regionální dostupnosti najdete v tématu [oblasti](regions.md#standard-and-neural-voices).

|Národní prostředí  | Jazyk            | Pohlaví | Název hlasu | Podpora stylů |
|--|--|--|--|--|
| `ar-EG` | Arabština (Egypt) | Female | `ar-EG-SalmaNeural` | Obecné |
| `ar-SA` | Arabština (Saúdská Arábie) | Female | `ar-SA-ZariyahNeural` | Obecné |
| `bg-BG` <sup>Nová</sup> | Bulharština (Bulgary) | Female | `bg-BG-KalinaNeural` | Obecné |
| `ca-ES` | Katalánština (Španělsko) | Female | `ca-ES-AlbaNeural` | Obecné |
| `cs-CZ` <sup>Nová</sup> | Čeština (Čeština)  | Female | `cs-CZ-VlastaNeural` | Obecné |
| `da-DK` | Dánština (Dánsko) | Female | `da-DK-ChristelNeural` | Obecné |
| `de-AT` <sup>Nová</sup> | Němčina (Rakousko) | Female | `de-AT-IngridNeural` | Obecné |
| `de-CH` <sup>Nová</sup> | Němčina (Švýcarsko) | Female | `de-CH-LeniNeural` | Obecné |
| `de-DE` | Němčina (Německo) | Female | `de-DE-KatjaNeural` | Obecné |
| `de-DE` <sup>Nová</sup> | němčina (Německo) | Male | `de-DE-ConradNeural` | Obecné |
| `el-GR` <sup>Nová</sup> | Řečtina (Řecko) | Female | `el-GR-AthinaNeural` | Obecné |
| `en-AU` | Angličtina (Austrálie) | Female | `en-AU-NatashaNeural` | Obecné |
| `en-AU` <sup>Nová</sup> | Austrálie (Austrálie) | Male | `en-AU-WilliamNeural` | Obecné |
| `en-CA` | Angličtina (Kanada) | Female | `en-CA-ClaraNeural` | Obecné |
| `en-GB` | Angličtina (Spojené království) | Female | `en-GB-LibbyNeural` | Obecné |
| `en-GB` | Angličtina (Spojené království) | Female | `en-GB-MiaNeural` | Obecné |
| `en-GB` <sup>Nová</sup> | Angličtina (Spojené království) | Male | `En-GB-RyanNeural` | Obecné |
| `en-IE` <sup>Nová</sup> | Irská angličtina (Irsko) | Female | `en-IE-EmilyNeural` | Obecné |
| `en-IN` | Angličtina (Indie) | Female | `en-IN-NeerjaNeural` | Obecné |
| `en-US` | Angličtina (USA) | Female | `en-US-AriaNeural` | Obecné, více dostupných stylů hlasu |
| `en-US` | Angličtina (USA) | Male | `en-US-GuyNeural` | Obecné |
| `en-US` <sup>Nová</sup> | Angličtina (USA) | Female | `en-US-JennyNeural` | Obecné, více dostupných stylů hlasu |
| `es-ES` | Španělština (Španělsko) | Female | `es-ES-ElviraNeural` | Obecné |
| `es-ES` <sup>Nová</sup> | Španělština (Španělsko) | Male | `es-ES-AlvaroNeural` | Obecné |
| `es-MX` | Španělština (Mexiko) | Female | `es-MX-DaliaNeural` | Obecné |
| `es-MX` <sup>Nová</sup> | Španělština (Mexiko) | Male | `es-MX-JorgeNeural` | Obecné |
| `fi-FI` | Finština (Finsko) | Female | `fi-FI-NooraNeural` | Obecné |
| `fr-CA` | Francouzština (Kanada) | Female | `fr-CA-SylvieNeural` | Obecné |
| `fr-CA` <sup>Nová</sup> | Francouzština (Kanada) | Male | `fr-CA-JeanNeural` | Obecné |
| `fr-CH` <sup>Nová</sup> | Francouzština (Švýcarsko) | Female | `fr-CH-ArianeNeural` | Obecné |
| `fr-FR` | Francouzština (Francie) | Female | `fr-FR-DeniseNeural` | Obecné |
| `fr-FR` <sup>Nová</sup> | francouzština (Francie) | Male | `fr-FR-HenriNeural` | Obecné |
| `he-IL` <sup>Nová</sup> | Hebrejština (plochá) | Female | `he-IL-HilaNeural` | Obecné |
| `hi-IN` | Hindština (Indie) | Female | `hi-IN-SwaraNeural` | Obecné |
| `hr-HR` <sup>Nová</sup> | chorvatština (Chorvatsko) | Female | `hr-HR-GabrijelaNeural` | Obecné |
| `hu-HU` <sup>Nová</sup> | Maďarština (Maďarsko) | Female | `hu-HU-NoemiNeural` | Obecné |
| `id-ID` <sup>Nová</sup> | Bahasa indonéský (Indonésie) | Male | `id-ID-ArdiNeural` | Obecné |
| `it-IT` | Italština (Itálie) | Female | `it-IT-ElsaNeural` | Obecné |
| `it-IT` <sup>Nová</sup> | italština (Itálie) | Female | `it-IT-IsabellaNeural` | Obecné |
| `it-IT` <sup>Nová</sup> | italština (Itálie) | Male | `it-IT-DiegoNeural` | Obecné |
| `ja-JP` | Japonština (Japonsko) | Female | `ja-JP-NanamiNeural` | Obecné |
| `ja-JP` <sup>Nová</sup> | japonština (Japonsko) | Male | `ja-JP-KeitaNeural` | Obecné |
| `ko-KR` | Korejština (Korea) | Female | `ko-KR-SunHiNeural` | Obecné |
| `ko-KR` <sup>Nová</sup> | korejština (Jižní Korea) | Male | `ko-KR-InJoonNeural` | Obecné |
| `ms-MY` <sup>Nová</sup> | malajština (Malajsie) | Female | `ms-MY-YasminNeural` | Obecné |
| `nb-NO` | Norština, Bokmål (Norsko) | Female | `nb-NO-IselinNeural` | Obecné |
| `nl-NL` | Holandština (Nizozemsko) | Female | `nl-NL-ColetteNeural` | Obecné |
| `pl-PL` | Polština (Polsko) | Female | `pl-PL-ZofiaNeural` | Obecné |
| `pt-BR` | Portugalština (Brazílie) | Female | `pt-BR-FranciscaNeural` | Obecné, více dostupných stylů hlasu |
| `pt-BR` <sup>Nová</sup> | Brazilská portugalština (Brazílie) | Male | `pt-BR-AntonioNeural` | Obecné |
| `pt-PT` | Portugalština (Portugalsko) | Female | `pt-PT-FernandaNeural` | Obecné |
| `ro-RO` <sup>Nová</sup> | rumunština (Rumunsko) | Female | `ro-RO-AlinaNeural` | Obecné |
| `ru-RU` | Ruština (Rusko) | Female | `ru-RU-DariyaNeural` | Obecné |
| `sk-SK` <sup>Nová</sup> | slovenština (Slovensko) | Female | `sk-SK-ViktoriaNeural` | Obecné |
| `sl-SI` <sup>Nová</sup> | slovinština (Slovinsko) | Female | `sl-SI-PetraNeural` | Obecné |
| `sv-SE` | Švédština (Švédsko) | Female | `sv-SE-HilleviNeural` | Obecné |
| `ta-IN` <sup>Nová</sup> | Tamilština (Indie) | Female | `ta-IN-PallaviNeural` | Obecné |
| `te-IN` <sup>Nová</sup> | Telugština (Indie) | Female | `te-IN-ShrutiNeural` | Obecné |
| `th-TH` | Thajština (Thajsko) | Female | `th-TH-AcharaNeural` | Obecné |
| `th-TH` <sup>Nová</sup> | thajština (Thajsko) | Female | `th-TH-PremwadeeNeural` | Obecné |
| `tr-TR` | Turečtina (Turecko) | Female | `tr-TR-EmelNeural` | Obecné |
| `vi-VN` <sup>Nová</sup> | Vietnamština (Vietnam) | Female | `vi-VN-HoaiMyNeural` | Obecné |
| `zh-CN` | Mandarinec (zjednodušená čínština, Čína) | Female | `zh-CN-XiaoxiaoNeural` | Obecné, více dostupných stylů hlasu |
| `zh-CN` | Mandarinec (zjednodušená čínština, Čína) | Female | `zh-CN-XiaoyouNeural` | Dětský hlas, optimalizovaný pro mluvený popis scénáře |
| `zh-CN` | Mandarinec (zjednodušená čínština, Čína) | Male | `zh-CN-YunyangNeural` | Optimalizováno pro čtení zpráv, je k dispozici více stylů hlasu |
| `zh-CN` | Mandarinec (zjednodušená čínština, Čína) | Male | `zh-CN-YunyeNeural` | Optimalizováno pro mluvený popis scénáře |
| `zh-HK` | Kantonština (tradiční čínština, Hongkong) | Female | `zh-HK-HiuGaaiNeural` | Obecné |
| `zh-TW` | Mandarinec (tradiční čínština, Tchaj-wan) | Female | `zh-TW-HsiaoYuNeural` | Obecné |


> [!IMPORTANT]
> `en-US-JessaNeural`Hlas se změnil na `en-US-AriaNeural` . Pokud jste předtím používali "Jessa", převeďte ho na standard ARIA.

Pokud se chcete dozvědět, jak můžete nakonfigurovat a upravit hlasy neuronové, přečtěte si téma věnované [jazykům pro označování řeči](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> V požadavcích na syntézu řeči můžete dál používat úplné mapování názvu služby, jako je například Microsoft Server Speech Převod textu na řeč Voice (EN-US, AriaNeural).

### <a name="standard-voices"></a>Standardní hlasy

Více než 75 standardních hlasů je k dispozici ve více než 45 jazycích a národních prostředích, což vám umožní převést text na syntetizované řeč. Další informace o regionální dostupnosti najdete v tématu [oblasti](regions.md#standard-and-neural-voices).

| Národní prostředí | Jazyk | Pohlaví | Název hlasu |
|--|--|--|--|
| `ar-EG` | Arabština (arabské písmo) | Female | `ar-EG-Hoda` |
| `ar-SA` | Arabština (Saúdská Arábie) | Male | `ar-SA-Naayf` |
| `bg-BG` | Bulharština (Bulharsko) | Male | `bg-BG-Ivan` |
| `ca-ES` | katalánština (Španělsko) | Female | `ca-ES-HerenaRUS` |
| `cs-CZ` | čeština (Česká republika) | Male | `cs-CZ-Jakub` |
| `da-DK` | dánština (Dánsko) | Female | `da-DK-HelleRUS` |
| `de-AT` | Němčina (Rakousko) | Male | `de-AT-Michael` |
| `de-CH` | Němčina (Švýcarsko) | Male | `de-CH-Karsten` |
| `de-DE` | němčina (Německo) | Female | `de-DE-HeddaRUS` |
| `de-DE` | němčina (Německo) | Male | `de-DE-Stefan` |
| `el-GR` | Řečtina (Řecko) | Male | `el-GR-Stefanos` |
| `en-AU` | Angličtina (Austrálie) | Female | `en-AU-Catherine` |
| `en-AU` | Angličtina (Austrálie) | Female | `en-AU-HayleyRUS` |
| `en-CA` | Angličtina (Kanada) | Female | `en-CA-HeatherRUS` |
| `en-CA` | Angličtina (Kanada) | Female | `en-CA-Linda` |
| `en-GB` | Angličtina (Spojené království) | Male | `en-GB-George` |
| `en-GB` | Angličtina (Spojené království) | Female | `en-GB-HazelRUS` |
| `en-GB` | Angličtina (Spojené království) | Female | `en-GB-Susan` |
| `en-IE` | Angličtina (Irsko) | Male | `en-IE-Sean` |
| `en-IN` | Angličtina (Indie) | Female | `en-IN-Heera` |
| `en-IN` | Angličtina (Indie) | Female | `en-IN-PriyaRUS` |
| `en-IN` | Angličtina (Indie) | Male | `en-IN-Ravi` |
| `en-US` | Angličtina (Spojené státy) | Male | `en-US-BenjaminRUS` |
| `en-US` | Angličtina (Spojené státy) | Male | `en-US-GuyRUS` |
| `en-US` | Angličtina (Spojené státy) | Female | `en-US-JessaRUS` |
| `en-US` | Angličtina (Spojené státy) | Female | `en-US-ZiraRUS` |
| `es-ES` | Španělština (Španělsko) | Female | `es-ES-HelenaRUS` |
| `es-ES` | Španělština (Španělsko) | Female | `es-ES-Laura` |
| `es-ES` | Španělština (Španělsko) | Male | `es-ES-Pablo` |
| `es-MX` | Španělština (Mexiko) | Female | `es-MX-HildaRUS` |
| `es-MX` | Španělština (Mexiko) | Male | `es-MX-Raul` |
| `fi-FI` | finština (Finsko) | Female | `fi-FI-HeidiRUS` |
| `fr-CA` | Francouzština (Kanada) | Female | `fr-CA-Caroline` |
| `fr-CA` | Francouzština (Kanada) | Female | `fr-CA-HarmonieRUS` |
| `fr-CH` | Francouzština (Švýcarsko) | Male | `fr-CH-Guillaume` |
| `fr-FR` | francouzština (Francie) | Female | `fr-FR-HortenseRUS` |
| `fr-FR` | francouzština (Francie) | Female | `fr-FR-Julie` |
| `fr-FR` | francouzština (Francie) | Male | `fr-FR-Paul` |
| `he-IL` | Hebrejština (Izrael) | Male | `he-IL-Asaf` |
| `hi-IN` | hindština (Indie) | Male | `hi-IN-Hemant` |
| `hi-IN` | hindština (Indie) | Female | `hi-IN-Kalpana` |
| `hr-HR` | chorvatština (Chorvatsko) | Male | `hr-HR-Matej` |
| `hu-HU` | Maďarština (Maďarsko) | Male | `hu-HU-Szabolcs` |
| `id-ID` | Indonéština (Indonésie) | Male | `id-ID-Andika` |
| `it-IT` | italština (Itálie) | Male | `it-IT-Cosimo` |
| `it-IT` | italština (Itálie) | Female | `it-IT-LuciaRUS` |
| `ja-JP` | japonština (Japonsko) | Female | `ja-JP-Ayumi` |
| `ja-JP` | japonština (Japonsko) | Female | `ja-JP-HarukaRUS` |
| `ja-JP` | japonština (Japonsko) | Male | `ja-JP-Ichiro` |
| `ko-KR` | korejština (Jižní Korea) | Female | `ko-KR-HeamiRUS` |
| `ms-MY` | malajština (Malajsie) | Male | `ms-MY-Rizwan` |
| `nb-NO` | Norština, Bokmål (Norsko) | Female | `nb-NO-HuldaRUS` |
| `nl-NL` | nizozemština (Nizozemsko) | Female | `nl-NL-HannaRUS` |
| `pl-PL` | polština (Polsko) | Female | `pl-PL-PaulinaRUS` |
| `pt-BR` | Portugalština (Brazílie) | Male | `pt-BR-Daniel` |
| `pt-BR` | Portugalština (Brazílie) | Female | `pt-BR-HeloisaRUS` |
| `pt-PT` | portugalština (Portugalsko) | Female | `pt-PT-HeliaRUS` |
| `ro-RO` | rumunština (Rumunsko) | Male | `ro-RO-Andrei` |
| `ru-RU` | ruština (Rusko) | Female | `ru-RU-EkaterinaRUS` |
| `ru-RU` | ruština (Rusko) | Female | `ru-RU-Irina` |
| `ru-RU` | ruština (Rusko) | Male | `ru-RU-Pavel` |
| `sk-SK` | slovenština (Slovensko) | Male | `sk-SK-Filip` |
| `sl-SI` | slovinština (Slovinsko) | Male | `sl-SI-Lado` |
| `sv-SE` | švédština (Švédsko) | Female | `sv-SE-HedvigRUS` |
| `ta-IN` | Tamilština (Indie) | Male | `ta-IN-Valluvar` |
| `te-IN` | Telugština (Indie) | Female | `te-IN-Chitra` |
| `th-TH` | thajština (Thajsko) | Male | `th-TH-Pattara` |
| `tr-TR` | turečtina (Turecko) | Female | `tr-TR-SedaRUS` |
| `vi-VN` | Vietnamština (Vietnam) | Male | `vi-VN-An` |
| `zh-CN` | Mandarinec (zjednodušená čínština, Čína) | Female | `zh-CN-HuihuiRUS` |
| `zh-CN` | Mandarinec (zjednodušená čínština, Čína) | Male | `zh-CN-Kangkang` |
| `zh-CN` | Mandarinec (zjednodušená čínština, Čína) | Female | `zh-CN-Yaoyao` |
| `zh-HK` | Kantonština (tradiční čínština, Hongkong) | Male | `zh-HK-Danny` |
| `zh-HK` | Kantonština (tradiční čínština, Hongkong) | Female | `zh-HK-TracyRUS` |
| `zh-TW` | Mandarinec (tradiční čínština, Tchaj-wan) | Female | `zh-TW-HanHanRUS` |
| `zh-TW` | Mandarinec (tradiční čínština, Tchaj-wan) | Female | `zh-TW-Yating` |
| `zh-TW` | Mandarinec (tradiční čínština, Tchaj-wan) | Male | `zh-TW-Zhiwei` |

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

| Národní prostředí | Jazyk | Ověřování závislé na textu | Ověřování nezávislé na textu | Identifikace nezávislá na textu |
|----|----|----|----|----|
| cs-CZ | Angličtina (USA) | ano | ano | ano |
|zh-CN    |Čínština (Mandarin, zjednodušená)|    neuvedeno|    ano|    ano|
|de-DE    |němčina (Německo)    |neuvedeno    |ano    |ano|
|en-GB    |Angličtina (Spojené království)    |neuvedeno    |ano    |ano|
|fr-FR    |francouzština (Francie)    |neuvedeno    |ano    |ano|
|EN-AU    |Angličtina (Austrálie)    |neuvedeno    |ano    |ano|
|en-CA    |Angličtina (Kanada)    |neuvedeno|    ano|    ano|
|fr – CA    |Francouzština (Kanada)    |neuvedeno    |ano|    ano|
|it-IT    |Italština|    neuvedeno    |ano|    ano|
|es-ES|    Španělština (Španělsko)    |neuvedeno    |ano|    ano|
|ES – MX    |Španělština (Mexiko)    |neuvedeno|    ano|    ano|
|ja-JP|    Japonština    |neuvedeno    |ano    |ano|
|pt-BR|    Portugalština (Brazílie)|    neuvedeno|    ano|    ano|

## <a name="next-steps"></a>Další kroky

* [Vytvoření bezplatného účtu Azure](https://azure.microsoft.com/free/cognitive-services/)
* [Přečtěte si téma rozpoznávání řeči v jazyce C. #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
