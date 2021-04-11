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
ms.date: 01/07/2021
ms.author: trbye
ms.custom: references_regions
ms.openlocfilehash: 9be9e9b4c5153776bd78675cdc0c9da8c0506f75
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106764"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Podpora jazyka a hlasu pro službu rozpoznávání řeči

Jazyková podpora se liší podle funkcí služby Speech. Následující tabulka shrnuje jazykovou podporu pro nabídky převodu [řeči na text](#speech-to-text), převod [textu na řeč](#text-to-speech)a [rozpoznávání řeči](#speech-translation) .

## <a name="speech-to-text"></a>Převod řeči na text

Sada Microsoft Speech SDK i REST API podporují následující jazyky (národní prostředí). 

Pro zvýšení přesnosti se přizpůsobení nabídne pro podmnožinu jazyků prostřednictvím nahrávání **zvuku a přepisu s popiskem** nebo **souvisejícího textu: věty**. Podpora pro přizpůsobení akustického modelu s **přepisy zvuku a s popiskem** na základě lidského textu je omezená na konkrétní základní modely uvedené níže. Další základní modely a jazyky budou používat jenom text přepisů, aby se vlastní modely využívaly stejně jako v **souvisejícím textu: věty**. Další informace o přizpůsobení najdete v tématu [Začínáme s Custom Speech](./custom-speech-overview.md).

<!--
To get the AM and ML bits:
https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Jazyk                 | Národní prostředí (BCP-47) | Vlastní nastavení  | [Rozpoznávání jazyka](how-to-automatic-language-detection.md) |
|------------------------------------|--------|---------------------------------------------------|-------------------------------|
| Arabština (Bahrajn), moderní Standard  | `ar-BH` | Text                                   | Yes                           | 
| Arabština (Egypt)                     | `ar-EG` | Text                                   | Yes                          |
| Arabština (Irák)                      | `ar-IQ` | Text                                   |                           |
| Arabština (Izrael)                    | `ar-IL` | Text                                   |                           |
| Arabština (Jordánsko)                    | `ar-JO` | Text                                   |                           |
| Arabština (Kuvajt)                    | `ar-KW` | Text                                   |                           |
| Arabština (Libanon)                   | `ar-LB` | Text                                   |                           |
| Arabština (Omán)                      | `ar-OM` | Text                                   |                           |
| Arabština (Katar)                     | `ar-QA` | Text                                   |                           |
| Arabština (Saúdská Arábie)              | `ar-SA` | Text                                   | Yes                          |
| Arabština (stav palestinská samospráva)        | `ar-PS` | Text                                   |                           |
| Arabština (Sýrie)                     | `ar-SY` | Text                                   | Yes                          |
| Arabština (Spojené arabské emiráty)      | `ar-AE` | Text                                   |                           |
| Bulharština (Bulharsko)               | `bg-BG` | Text                                   |                           |
| katalánština (Španělsko)                    | `ca-ES` | Text                                   | Yes                          |
| Čínština (kantonština, tradiční)   | `zh-HK` | Zvuk (20201015)<br>Text                 |        Yes                   |
| Čínština (Mandarin, zjednodušená)     | `zh-CN` | Zvuk (20200910)<br>Text                 |     Yes                      |
| Čínština (tchajwanský mandarinka)       | `zh-TW` | Zvuk (20190701, 20201015)<br>Text                 |           Yes                |
| chorvatština (Chorvatsko)                 | `hr-HR` | Text                                   |                           |
| čeština (Česká republika)             | `cs-CZ` | Text                                   |                           |
| dánština (Dánsko)                   | `da-DK` | Text                                   | Yes                          |
| nizozemština (Nizozemsko)                | `nl-NL` | Zvuk (20201015)<br>Text<br>Výslovnost|    Yes                       |
| Angličtina (Austrálie)                | `en-AU` | Zvuk (20201019)<br>Text                 | Yes                          |
| Angličtina (Kanada)                   | `en-CA` | Zvuk (20201019)<br>Text                 | Yes                          |
| Angličtina (Ghana)                    | `en-GH` | Text                                   |                           |
| Angličtina (Hongkong)                | `en-HK` | Text                                   |                           |
| Angličtina (Indie)                    | `en-IN` | Zvuk (20200923)<br>Text                 | Yes                          |
| Angličtina (Irsko)                  | `en-IE` | Text                                   |                           |
| Angličtina (Keňa)                    | `en-KE` | Text                                   |                           |
| Angličtina (Nový Zéland)              | `en-NZ` | Zvuk (20201019)<br>Text                 |  Yes                         |
| Angličtina (Nigérie)                  | `en-NG` | Text                                   |                           |
| Angličtina (Filipíny)              | `en-PH` | Text                                   |                           |
| Angličtina (Singapur)                | `en-SG` | Text                                   |                           |
| Angličtina (Jihoafrická republika)             | `en-ZA` | Text                                   |                           |
| Angličtina (Tanzánie)                 | `en-TZ` | Text                                   |                           |
| Angličtina (Spojené království)           | `en-GB` | Zvuk (20201019)<br>Text<br>Výslovnost| Yes                          |
| Angličtina (Spojené státy)            | `en-US` | Zvuk (20201019)<br>Text<br>Výslovnost| Yes                          |
| Estonština (Estonsko)                  | `et-EE` | Text                                   |                           |
| Filipínský (Filipíny)             | `fil-PH`| Text                                   |                           |
| finština (Finsko)                  | `fi-FI` | Text                                   |     Yes                      |
| Francouzština (Kanada)                    | `fr-CA` | Zvuk (20201015)<br>Text<br>Výslovnost|     Yes                      |
| francouzština (Francie)                    | `fr-FR` | Zvuk (20201015)<br>Text<br>Výslovnost|      Yes                     |
| Francouzština (Švýcarsko)               | `fr-CH` | Text<br>Výslovnost                  |                           |
| Němčina (Rakousko)                   | `de-AT` | Text<br>Výslovnost                  |                           |
| němčina (Německo)                   | `de-DE` | Zvuk (20190701, 20200619, 20201127)<br>Text<br>Výslovnost|  Yes                         |
| Řečtina (Řecko)                     | `el-GR` | Text                                   |                           |
| Gudžarátština (Indie)                  | `gu-IN` | Text                                   |                           |
| hindština (Indie)                      | `hi-IN` | Zvuk (20200701)<br>Text                 |     Yes                      |
| Maďarština (Maďarsko)                | `hu-HU` | Text                                   |                           |
| Indonéština (Indonésie)             | `id-ID` | Text                                   |                           |
| Irština (Irsko)                     | `ga-IE` | Text                                   |                           |
| italština (Itálie)                    | `it-IT` | Zvuk (20201016)<br>Text<br>Výslovnost|      Yes                     |
| japonština (Japonsko)                   | `ja-JP` | Text                                   |      Yes                     |
| korejština (Jižní Korea)                     | `ko-KR` | Zvuk (20201015)<br>Text                 |      Yes                     |
| lotyština (Lotyšsko)                   | `lv-LV` | Text                                   |                           |
| litevština (Litva)             | `lt-LT` | Text                                   |                           |
| malajština (Malajsie)                   | `ms-MY` | Text                                   |                           |
| Maltština (Malta)                    | `mt-MT` | Text                                   |                           |
| Maráthština (Indie)                    | `mr-IN` | Text                                   |                           |
| Norština, Bokmål (Norsko)         | `nb-NO` | Text                                   |     Yes                      |
| polština (Polsko)                    | `pl-PL` | Text                                   |       Yes                    |
| Portugalština (Brazílie)                | `pt-BR` | Zvuk (20190620, 20201015)<br>Text<br>Výslovnost|          Yes                 |
| portugalština (Portugalsko)              | `pt-PT` | Text<br>Výslovnost                  |             Yes              |
| rumunština (Rumunsko)                 | `ro-RO` | Text                                   |                           |
| ruština (Rusko)                   | `ru-RU` | Zvuk (20200907)<br>Text                 |                Yes           |
| slovenština (Slovensko)                  | `sk-SK` | Text                                   |                           |
| slovinština (Slovinsko)               | `sl-SI` | Text                                   |                           |
| Španělština (Argentina)                | `es-AR` | Text<br>Výslovnost                  |                           |
| Španělština (Bolívie)                  | `es-BO` | Text<br>Výslovnost                  |                           |
| Španělština (Chile)                    | `es-CL` | Text<br>Výslovnost                  |                           |
| Španělština (Kolumbie)                 | `es-CO` | Text<br>Výslovnost                  |                           |
| Španělština (Kostarika)               | `es-CR` | Text<br>Výslovnost                  |                           |
| Španělština (Kuba)                     | `es-CU` | Text<br>Výslovnost                  |                           |
| Španělština (Dominikánská republika)       | `es-DO` | Text<br>Výslovnost                  |                           |
| Španělština (Ekvádor)                  | `es-EC` | Text<br>Výslovnost                  |                           |
| Španělština (Salvador)              | `es-SV` | Text<br>Výslovnost                  |                           |
| Španělština (Rovníková Guinea)        | `es-GQ` | Text                                   |                           |
| Španělština (Guatemala)                | `es-GT` | Text<br>Výslovnost                  |                           |
| Španělština (Honduras)                 | `es-HN` | Text<br>Výslovnost                  |                           |
| Španělština (Mexiko)                   | `es-MX` | Zvuk (20200907)<br>Text<br>Výslovnost|    Yes                       |
| Španělština (Nikaragua)                | `es-NI` | Text<br>Výslovnost                  |                           |
| Španělština (Panama)                   | `es-PA` | Text<br>Výslovnost                  |                           |
| Španělština (Paraguay)                 | `es-PY` | Text<br>Výslovnost                  |                           |
| Španělština (Peru)                     | `es-PE` | Text<br>Výslovnost                  |                           |
| Španělština (Portoriko)              | `es-PR` | Text<br>Výslovnost                  |                           |
| Španělština (Španělsko)                    | `es-ES` | Zvuk (20201015)<br>Text<br>Výslovnost|  Yes                         |
| Španělština (Uruguay)                  | `es-UY` | Text<br>Výslovnost                  |                           |
| Španělština (USA)                      | `es-US` | Text<br>Výslovnost                  |                           |
| Španělština (Venezuela)                | `es-VE` | Text<br>Výslovnost                  |                           |
| švédština (Švédsko)                   | `sv-SE` | Text                                   |   Yes                        |
| Tamilština (Indie)                      | `ta-IN` | Text                                   |                           |
| Telugština (Indie)                     | `te-IN` | Text                                   |                           |
| thajština (Thajsko)                    | `th-TH` | Text                                   |      Yes                     |
| turečtina (Turecko)                   | `tr-TR` | Text                                   |                           |
| Vietnamština (Vietnam)               | `vi-VN` | Text                                   |                           |

## <a name="text-to-speech"></a>Převod textu na řeč

Sada Microsoft Speech SDK a rozhraní REST API podporují tyto hlasy, z nichž každý podporuje konkrétní jazyk a dialekt identifikovaný národním prostředím. Můžete také získat úplný seznam jazyků a hlasů podporovaných pro každou konkrétní oblast nebo koncový bod prostřednictvím [rozhraní API pro hlasy a seznam](rest-text-to-speech.md#get-a-list-of-voices). 

> [!IMPORTANT]
> Ceny se liší pro standardní, vlastní a neuronové hlasy. Další informace najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) .

### <a name="neural-voices"></a>Hlasy neuronové

Neuronové převod textu na řeč je nový typ syntézy řeči, který využívá rozsáhlé sítě neuronové. Při použití hlasu neuronové je syntetizované rozpoznávání řeči skoro neodlišitelné od lidských nahrávek.

Neuronové hlasy se dají použít k zajištění většího přirozeného a poutavého působení s chatovacích robotů o a hlasovými asistenty, konverzí digitálních textů, jako jsou například e-knihy, do audiobooks a vylepšení navigačních systémů v klecích. V případě přirozeného Prosody jako přirozeného a jasného kloubování slov, neuronové hlasy významně omezují naslouchat únavu při interakci uživatelů se systémy AI.

> [!NOTE]
> Hlasy neuronové se vytvářejí z ukázek, které používají vzorkovací frekvenci 24 kHz.
> Všechny hlasy můžou při syntetizaci převzorkovat nebo převzorkovat na jiné vzorkovací sazby.


| Jazyk | Národní prostředí | Pohlaví | Název hlasu | Podpora stylů |
|---|---|---|---|---|
| Arabština (Egypt) | `ar-EG` | Female | `ar-EG-SalmaNeural` | Obecné |
| Arabština (Egypt) | `ar-EG` | Male | `ar-EG-ShakirNeural` | Obecné |
| Arabština (Saúdská Arábie) | `ar-SA` | Female | `ar-SA-ZariyahNeural` | Obecné |
| Arabština (Saúdská Arábie) | `ar-SA` | Male | `ar-SA-HamedNeural` | Obecné |
| Bulharština (Bulharsko) | `bg-BG` | Female | `bg-BG-KalinaNeural` | Obecné |
| Bulharština (Bulharsko) | `bg-BG` | Male | `bg-BG-BorislavNeural` | Obecné |
| katalánština (Španělsko) | `ca-ES` | Female | `ca-ES-AlbaNeural` | Obecné |
| katalánština (Španělsko) | `ca-ES` | Female | `ca-ES-JoanaNeural` | Obecné |
| katalánština (Španělsko) | `ca-ES` | Male | `ca-ES-EnricNeural` | Obecné |
| Čínština (kantonština, tradiční) | `zh-HK` | Female | `zh-HK-HiuGaaiNeural` | Obecné |
| Čínština (kantonština, tradiční) | `zh-HK` | Female | `zh-HK-HiuMaanNeural` | Obecné |
| Čínština (kantonština, tradiční) | `zh-HK` | Male | `zh-HK-WanLungNeural` | Obecné |
| Čínština (Mandarin, zjednodušená) | `zh-CN` | Female | `zh-CN-XiaoxiaoNeural` | Obecné, více dostupných stylů hlasu [pomocí SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Čínština (Mandarin, zjednodušená) | `zh-CN` | Female | `zh-CN-XiaoyouNeural` | Podřízený hlas, optimalizovaný pro mluvený popis scénáře |
| Čínština (Mandarin, zjednodušená) | `zh-CN` | Male | `zh-CN-YunyangNeural` | Optimalizováno pro čtení zpráv,<br /> více dostupných stylů hlasu [pomocí SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Čínština (Mandarin, zjednodušená) | `zh-CN` | Male | `zh-CN-YunyeNeural` | Optimalizováno pro mluvený popis scénáře |
| Čínština (tchajwanský mandarinka) | `zh-TW` | Female | `zh-TW-HsiaoChenNeural` | Obecné |
| Čínština (tchajwanský mandarinka) | `zh-TW` | Female | `zh-TW-HsiaoYuNeural` | Obecné |
| Čínština (tchajwanský mandarinka) | `zh-TW` | Male | `zh-TW-YunJheNeural` | Obecné |
| chorvatština (Chorvatsko) | `hr-HR` | Female | `hr-HR-GabrijelaNeural` | Obecné |
| chorvatština (Chorvatsko) | `hr-HR` | Male | `hr-HR-SreckoNeural` | Obecné |
| Čeština (Čeština) | `cs-CZ` | Female | `cs-CZ-VlastaNeural` | Obecné |
| Čeština (Čeština) | `cs-CZ` | Male | `cs-CZ-AntoninNeural` | Obecné |
| dánština (Dánsko) | `da-DK` | Female | `da-DK-ChristelNeural` | Obecné |
| dánština (Dánsko) | `da-DK` | Male | `da-DK-JeppeNeural` | Obecné |
| Nizozemština (Belgie) | `nl-BE` | Female | `nl-BE-DenaNeural` <sup>Nová</sup> | Obecné | 
| Nizozemština (Belgie) | `nl-BE` | Male | `nl-BE-ArnaudNeural` <sup>Nová</sup> | Obecné | 
| nizozemština (Nizozemsko) | `nl-NL` | Female | `nl-NL-ColetteNeural` | Obecné |
| nizozemština (Nizozemsko) | `nl-NL` | Female | `nl-NL-FennaNeural` | Obecné |
| nizozemština (Nizozemsko) | `nl-NL` | Male | `nl-NL-MaartenNeural` | Obecné |
| Angličtina (Austrálie) | `en-AU` | Female | `en-AU-NatashaNeural` | Obecné |
| Angličtina (Austrálie) | `en-AU` | Male | `en-AU-WilliamNeural` | Obecné |
| Angličtina (Kanada) | `en-CA` | Female | `en-CA-ClaraNeural` | Obecné |
| Angličtina (Kanada) | `en-CA` | Male | `en-CA-LiamNeural` | Obecné |
| Angličtina (Indie) | `en-IN` | Female | `en-IN-NeerjaNeural` | Obecné |
| Angličtina (Indie) | `en-IN` | Male | `en-IN-PrabhatNeural` | Obecné |
| Angličtina (Irsko) | `en-IE` | Female | `en-IE-EmilyNeural` | Obecné |
| Angličtina (Irsko) | `en-IE` | Male | `en-IE-ConnorNeural` | Obecné |
| Angličtina (Filipíny) | `en-PH` | Female | `en-PH-RosaNeural` <sup>Nová</sup> | Obecné | 
| Angličtina (Filipíny) | `en-PH` | Male | `en-PH-JamesNeural` <sup>Nová</sup> | Obecné | 
| Angličtina (Spojené království) | `en-GB` | Female | `en-GB-LibbyNeural` | Obecné |
| Angličtina (Spojené království) | `en-GB` | Female | `en-GB-MiaNeural` | Obecné |
| Angličtina (Spojené království) | `en-GB` | Male | `en-GB-RyanNeural` | Obecné |
| Angličtina (Spojené státy) | `en-US` | Female | `en-US-AriaNeural` | Obecné, více dostupných stylů hlasu [pomocí SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Angličtina (Spojené státy) | `en-US` | Female | `en-US-JennyNeural` | Obecné |
| Angličtina (Spojené státy) | `en-US` | Male | `en-US-GuyNeural` | Obecné |
| Estonština (Estonsko) | `et-EE` | Female | `et-EE-AnuNeural` | Obecné |
| Estonština (Estonsko) | `et-EE` | Male | `et-EE-KertNeural` | Obecné |
| finština (Finsko) | `fi-FI` | Female | `fi-FI-NooraNeural` | Obecné |
| finština (Finsko) | `fi-FI` | Female | `fi-FI-SelmaNeural` | Obecné |
| finština (Finsko) | `fi-FI` | Male | `fi-FI-HarriNeural` | Obecné |
| Francouzština (Belgie) | `fr-BE` | Female | `fr-BE-CharlineNeural` <sup>Nová</sup> | Obecné | 
| Francouzština (Belgie) | `fr-BE` | Male | `fr-BE-GerardNeural` <sup>Nová</sup> | Obecné | 
| Francouzština (Kanada) | `fr-CA` | Female | `fr-CA-SylvieNeural` | Obecné |
| Francouzština (Kanada) | `fr-CA` | Male | `fr-CA-AntoineNeural` | Obecné |
| Francouzština (Kanada) | `fr-CA` | Male | `fr-CA-JeanNeural` | Obecné |
| francouzština (Francie) | `fr-FR` | Female | `fr-FR-DeniseNeural` | Obecné |
| francouzština (Francie) | `fr-FR` | Male | `fr-FR-HenriNeural` | Obecné |
| Francouzština (Švýcarsko) | `fr-CH` | Female | `fr-CH-ArianeNeural` | Obecné |
| Francouzština (Švýcarsko) | `fr-CH` | Male | `fr-CH-FabriceNeural` | Obecné |
| Němčina (Rakousko) | `de-AT` | Female | `de-AT-IngridNeural` | Obecné |
| Němčina (Rakousko) | `de-AT` | Male | `de-AT-JonasNeural` | Obecné |
| němčina (Německo) | `de-DE` | Female | `de-DE-KatjaNeural` | Obecné |
| němčina (Německo) | `de-DE` | Male | `de-DE-ConradNeural` | Obecné |
| Němčina (Švýcarsko) | `de-CH` | Female | `de-CH-LeniNeural` | Obecné |
| Němčina (Švýcarsko) | `de-CH` | Male | `de-CH-JanNeural` | Obecné |
| Řečtina (Řecko) | `el-GR` | Female | `el-GR-AthinaNeural` | Obecné |
| Řečtina (Řecko) | `el-GR` | Male | `el-GR-NestorasNeural` | Obecné |
| Hebrejština (Izrael) | `he-IL` | Female | `he-IL-HilaNeural` | Obecné |
| Hebrejština (Izrael) | `he-IL` | Male | `he-IL-AvriNeural` | Obecné |
| hindština (Indie) | `hi-IN` | Female | `hi-IN-SwaraNeural` | Obecné |
| hindština (Indie) | `hi-IN` | Male | `hi-IN-MadhurNeural` | Obecné |
| Maďarština (Maďarsko) | `hu-HU` | Female | `hu-HU-NoemiNeural` | Obecné |
| Maďarština (Maďarsko) | `hu-HU` | Male | `hu-HU-TamasNeural` | Obecné |
| Indonéština (Indonésie) | `id-ID` | Female | `id-ID-GadisNeural` | Obecné |
| Indonéština (Indonésie) | `id-ID` | Male | `id-ID-ArdiNeural` | Obecné |
| Irština (Irsko) | `ga-IE` | Female | `ga-IE-OrlaNeural` | Obecné |
| Irština (Irsko) | `ga-IE` | Male | `ga-IE-ColmNeural` | Obecné |
| italština (Itálie) | `it-IT` | Female | `it-IT-ElsaNeural` | Obecné |
| italština (Itálie) | `it-IT` | Female | `it-IT-IsabellaNeural` | Obecné |
| italština (Itálie) | `it-IT` | Male | `it-IT-DiegoNeural` | Obecné |
| japonština (Japonsko) | `ja-JP` | Female | `ja-JP-NanamiNeural` | Obecné |
| japonština (Japonsko) | `ja-JP` | Male | `ja-JP-KeitaNeural` | Obecné |
| korejština (Jižní Korea) | `ko-KR` | Female | `ko-KR-SunHiNeural` | Obecné |
| korejština (Jižní Korea) | `ko-KR` | Male | `ko-KR-InJoonNeural` | Obecné |
| lotyština (Lotyšsko) | `lv-LV` | Female | `lv-LV-EveritaNeural` | Obecné |
| lotyština (Lotyšsko) | `lv-LV` | Male | `lv-LV-NilsNeural` | Obecné |
| litevština (Litva) | `lt-LT` | Female | `lt-LT-OnaNeural` | Obecné |
| litevština (Litva) | `lt-LT` | Male | `lt-LT-LeonasNeural` | Obecné |
| malajština (Malajsie) | `ms-MY` | Female | `ms-MY-YasminNeural` | Obecné |
| malajština (Malajsie) | `ms-MY` | Male | `ms-MY-OsmanNeural` | Obecné |
| Maltština (Malta) | `mt-MT` | Female | `mt-MT-GraceNeural` | Obecné |
| Maltština (Malta) | `mt-MT` | Male | `mt-MT-JosephNeural` | Obecné |
| Norština, Bokmål (Norsko) | `nb-NO` | Female | `nb-NO-IselinNeural` | Obecné |
| Norština, Bokmål (Norsko) | `nb-NO` | Female | `nb-NO-PernilleNeural` | Obecné |
| Norština, Bokmål (Norsko) | `nb-NO` | Male | `nb-NO-FinnNeural` | Obecné |
| polština (Polsko) | `pl-PL` | Female | `pl-PL-AgnieszkaNeural` | Obecné |
| polština (Polsko) | `pl-PL` | Female | `pl-PL-ZofiaNeural` | Obecné |
| polština (Polsko) | `pl-PL` | Male | `pl-PL-MarekNeural` | Obecné |
| Portugalština (Brazílie) | `pt-BR` | Female | `pt-BR-FranciscaNeural` | Obecné, více dostupných stylů hlasu [pomocí SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Portugalština (Brazílie) | `pt-BR` | Male | `pt-BR-AntonioNeural` | Obecné |
| portugalština (Portugalsko) | `pt-PT` | Female | `pt-PT-FernandaNeural` | Obecné |
| portugalština (Portugalsko) | `pt-PT` | Female | `pt-PT-RaquelNeural` | Obecné |
| portugalština (Portugalsko) | `pt-PT` | Male | `pt-PT-DuarteNeural` | Obecné |
| rumunština (Rumunsko) | `ro-RO` | Female | `ro-RO-AlinaNeural` | Obecné |
| rumunština (Rumunsko) | `ro-RO` | Male | `ro-RO-EmilNeural` | Obecné |
| ruština (Rusko) | `ru-RU` | Female | `ru-RU-DariyaNeural` | Obecné |
| ruština (Rusko) | `ru-RU` | Female | `ru-RU-SvetlanaNeural` | Obecné |
| ruština (Rusko) | `ru-RU` | Male | `ru-RU-DmitryNeural` | Obecné |
| slovenština (Slovensko) | `sk-SK` | Female | `sk-SK-ViktoriaNeural` | Obecné |
| slovenština (Slovensko) | `sk-SK` | Male | `sk-SK-LukasNeural` | Obecné |
| slovinština (Slovinsko) | `sl-SI` | Female | `sl-SI-PetraNeural` | Obecné |
| slovinština (Slovinsko) | `sl-SI` | Male | `sl-SI-RokNeural` | Obecné |
| Španělština (Mexiko) | `es-MX` | Female | `es-MX-DaliaNeural` | Obecné |
| Španělština (Mexiko) | `es-MX` | Male | `es-MX-JorgeNeural` | Obecné |
| Španělština (Španělsko) | `es-ES` | Female | `es-ES-ElviraNeural` | Obecné |
| Španělština (Španělsko) | `es-ES` | Male | `es-ES-AlvaroNeural` | Obecné |
| švédština (Švédsko) | `sv-SE` | Female | `sv-SE-HilleviNeural` | Obecné |
| švédština (Švédsko) | `sv-SE` | Female | `sv-SE-SofieNeural` | Obecné |
| švédština (Švédsko) | `sv-SE` | Male | `sv-SE-MattiasNeural` | Obecné |
| Tamilština (Indie) | `ta-IN` | Female | `ta-IN-PallaviNeural` | Obecné |
| Tamilština (Indie) | `ta-IN` | Male | `ta-IN-ValluvarNeural` | Obecné |
| Telugština (Indie) | `te-IN` | Female | `te-IN-ShrutiNeural` | Obecné |
| Telugština (Indie) | `te-IN` | Male | `te-IN-MohanNeural` | Obecné |
| thajština (Thajsko) | `th-TH` | Female | `th-TH-AcharaNeural` | Obecné |
| thajština (Thajsko) | `th-TH` | Female | `th-TH-PremwadeeNeural` | Obecné |
| thajština (Thajsko) | `th-TH` | Male | `th-TH-NiwatNeural` | Obecné |
| turečtina (Turecko) | `tr-TR` | Female | `tr-TR-EmelNeural` | Obecné |
| turečtina (Turecko) | `tr-TR` | Male | `tr-TR-AhmetNeural` | Obecné |
| ukrajinština (Ukrajina) | `uk-UA` | Female | `uk-UA-PolinaNeural` <sup>Nová</sup> | Obecné | 
| ukrajinština (Ukrajina) | `uk-UA` | Male | `uk-UA-OstapNeural` <sup>Nová</sup> | Obecné | 
| Urdština (Pákistán) | `ur-PK` | Female | `ur-PK-UzmaNeural` <sup>Nová</sup>  | Obecné | 
| Urdština (Pákistán) | `ur-PK` | Male | `ur-PK-AsadNeural` <sup>Nová</sup> | Obecné | 
| Vietnamština (Vietnam) | `vi-VN` | Female | `vi-VN-HoaiMyNeural` | Obecné |
| Vietnamština (Vietnam) | `vi-VN` | Male | `vi-VN-NamMinhNeural` | Obecné |
| Velština (Spojené království) | `cy-GB` | Female | `cy-GB-NiaNeural` <sup>Nová</sup> | Obecné | 
| Velština (Spojené království) | `cy-GB` | Male | `cy-GB-AledNeural` <sup>Nová</sup> | Obecné | 

#### <a name="neural-voices-in-preview"></a>Hlasy neuronové ve verzi Preview

Níže jsou hlasy neuronové ve verzi Public Preview. 

| Jazyk                         | Národní prostředí  | Pohlaví | Název hlasu                             | Podpora stylů |
|----------------------------------|---------|--------|----------------------------------------|---------------|
| Čínština (Mandarin, zjednodušená) | `zh-CN` | Female | `zh-CN-XiaohanNeural` | Obecné, více stylů dostupných [pomocí SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Čínština (Mandarin, zjednodušená) | `zh-CN` | Female | `zh-CN-XiaomoNeural` | Obecné, více aktérů rolí a stylů dostupných [pomocí SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Čínština (Mandarin, zjednodušená) | `zh-CN` | Female | `zh-CN-XiaoruiNeural` | Vrchní hlas, více stylů dostupných [pomocí SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Čínština (Mandarin, zjednodušená) | `zh-CN` | Female | `zh-CN-XiaoxuanNeural` | Obecné, více aktérů rolí a stylů dostupných [pomocí SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Čínština (Mandarin, zjednodušená) | `zh-CN` | Male   | `zh-CN-YunxiNeural` | Obecné, více stylů dostupných [pomocí SSML](speech-synthesis-markup.md#adjust-speaking-styles) |

> [!IMPORTANT]
> Hlasy ve verzi Public Preview jsou dostupné jenom ve třech oblastech služby: Východní USA, Západní Evropa a jihovýchodní Asie.

Další informace o regionální dostupnosti najdete v tématu [oblasti](regions.md#neural-and-standard-voices).

Další informace o tom, jak můžete nakonfigurovat a upravit hlasy neuronové, jako jsou například mluvené styly, najdete v tématu [řeč syntézy kódu](speech-synthesis-markup.md#adjust-speaking-styles).

> [!IMPORTANT]
> `en-US-JessaNeural`Hlas se změnil na `en-US-AriaNeural` . Pokud jste předtím používali "Jessa", převeďte ho na standard ARIA.

> [!TIP]
> V požadavcích na syntézu řeči můžete dál používat úplné mapování názvu služby, jako je například Microsoft Server Speech Převod textu na řeč Voice (EN-US, AriaNeural).

### <a name="standard-voices"></a>Standardní hlasy

Více než 75 standardních hlasů je k dispozici ve více než 45 jazycích a národních prostředích, což vám umožní převést text na syntetizované řeč. Další informace o regionální dostupnosti najdete v tématu [oblasti](regions.md#neural-and-standard-voices).

> [!NOTE]
> Se dvěma výjimkami se vytvářejí standardní hlasy ze vzorků, které používají vzorkovací frekvenci 16 kHz.
> Hlasy **en-US-AriaRUS** a **en-US-GuyRUS** jsou také vytvořeny z ukázek, které používají vzorkovací frekvenci 24 kHz.
> Všechny hlasy můžou při syntetizaci převzorkovat nebo převzorkovat na jiné vzorkovací sazby.

| Jazyk | Národní prostředí (BCP-47) | Pohlaví | Název hlasu |
|--|--|--|--|
| Arabština (arabské písmo) | `ar-EG` | Female | `ar-EG-Hoda`|
| Arabština (Saúdská Arábie) | `ar-SA` | Male | `ar-SA-Naayf`|
| Bulharština (Bulharsko) | `bg-BG` | Male | `bg-BG-Ivan`|
| katalánština (Španělsko) | `ca-ES` | Female | `ca-ES-HerenaRUS`|
| Čínština (kantonština, tradiční) | `zh-HK` | Male | `zh-HK-Danny`|
| Čínština (kantonština, tradiční) | `zh-HK` | Female | `zh-HK-TracyRUS`|
| Čínština (Mandarin, zjednodušená) | `zh-CN` | Female | `zh-CN-HuihuiRUS`|
| Čínština (Mandarin, zjednodušená) | `zh-CN` | Male | `zh-CN-Kangkang`|
| Čínština (Mandarin, zjednodušená) | `zh-CN` | Female | `zh-CN-Yaoyao`|
| Čínština (tchajwanský mandarinka) |  `zh-TW` | Female | `zh-TW-HanHanRUS`|
| Čínština (tchajwanský mandarinka) |  `zh-TW` | Female | `zh-TW-Yating`|
| Čínština (tchajwanský mandarinka) |  `zh-TW` | Male | `zh-TW-Zhiwei`|
| chorvatština (Chorvatsko) | `hr-HR` | Male | `hr-HR-Matej`|
| čeština (Česká republika) | `cs-CZ` | Male | `cs-CZ-Jakub`|
| dánština (Dánsko) | `da-DK` | Female | `da-DK-HelleRUS`|
| nizozemština (Nizozemsko) | `nl-NL` | Female | `nl-NL-HannaRUS`|
| Angličtina (Austrálie) | `en-AU` | Female | `en-AU-Catherine`|
| Angličtina (Austrálie) | `en-AU` | Female | `en-AU-HayleyRUS`|
| Angličtina (Kanada) | `en-CA` | Female | `en-CA-HeatherRUS`|
| Angličtina (Kanada) | `en-CA` | Female | `en-CA-Linda`|
| Angličtina (Indie) | `en-IN` | Female | `en-IN-Heera`|
| Angličtina (Indie) | `en-IN` | Female | `en-IN-PriyaRUS`|
| Angličtina (Indie) | `en-IN` | Male | `en-IN-Ravi`|
| Angličtina (Irsko) | `en-IE` | Male | `en-IE-Sean`|
| Angličtina (Spojené království) | `en-GB` | Male | `en-GB-George`|
| Angličtina (Spojené království) | `en-GB` | Female | `en-GB-HazelRUS`|
| Angličtina (Spojené království) | `en-GB` | Female | `en-GB-Susan`|
| Angličtina (Spojené státy) | `en-US` | Male | `en-US-BenjaminRUS`|
| Angličtina (Spojené státy) | `en-US` | Male | `en-US-GuyRUS`|
| Angličtina (Spojené státy) | `en-US` | Female | `en-US-AriaRUS`|
| Angličtina (Spojené státy) | `en-US` | Female | `en-US-ZiraRUS`|
| finština (Finsko) | `fi-FI` | Female | `fi-FI-HeidiRUS`|
| Francouzština (Kanada) | `fr-CA` | Female | `fr-CA-Caroline`|
| Francouzština (Kanada) | `fr-CA` | Female | `fr-CA-HarmonieRUS`|
| francouzština (Francie) | `fr-FR` | Female | `fr-FR-HortenseRUS`|
| francouzština (Francie) | `fr-FR` | Female | `fr-FR-Julie`|
| francouzština (Francie) | `fr-FR` | Male | `fr-FR-Paul`|
| Francouzština (Švýcarsko) | `fr-CH` | Male | `fr-CH-Guillaume`|
| Němčina (Rakousko) | `de-AT` | Male | `de-AT-Michael`|
| němčina (Německo) | `de-DE` | Female | `de-DE-HeddaRUS`|
| němčina (Německo) | `de-DE` | Male | `de-DE-Stefan`|
| Němčina (Švýcarsko) | `de-CH` | Male | `de-CH-Karsten`|
| Řečtina (Řecko) | `el-GR` | Male | `el-GR-Stefanos`|
| Hebrejština (Izrael) | `he-IL` | Male | `he-IL-Asaf`|
| hindština (Indie) | `hi-IN` | Male | `hi-IN-Hemant`|
| hindština (Indie) | `hi-IN` | Female | `hi-IN-Kalpana`|
| Maďarština (Maďarsko) | `hu-HU` | Male | `hu-HU-Szabolcs`|
| Indonéština (Indonésie) | `id-ID` | Male | `id-ID-Andika`|
| italština (Itálie) | `it-IT` | Male | `it-IT-Cosimo`|
| italština (Itálie) | `it-IT` | Female | `it-IT-LuciaRUS`|
| japonština (Japonsko) | `ja-JP` | Female | `ja-JP-Ayumi`|
| japonština (Japonsko) | `ja-JP` | Female | `ja-JP-HarukaRUS`|
| japonština (Japonsko) | `ja-JP` | Male | `ja-JP-Ichiro`|
| korejština (Jižní Korea) | `ko-KR` | Female | `ko-KR-HeamiRUS`|
| malajština (Malajsie) | `ms-MY` | Male | `ms-MY-Rizwan`|
| Norština, Bokmål (Norsko) | `nb-NO` | Female | `nb-NO-HuldaRUS`|
| polština (Polsko) | `pl-PL` | Female | `pl-PL-PaulinaRUS`|
| Portugalština (Brazílie) | `pt-BR` | Male | `pt-BR-Daniel`|
| Portugalština (Brazílie) | `pt-BR` | Female | `pt-BR-HeloisaRUS`|
| portugalština (Portugalsko) | `pt-PT` | Female | `pt-PT-HeliaRUS`|
| rumunština (Rumunsko) | `ro-RO` | Male | `ro-RO-Andrei`|
| ruština (Rusko) | `ru-RU` | Female | `ru-RU-EkaterinaRUS`|
| ruština (Rusko) | `ru-RU` | Female | `ru-RU-Irina`|
| ruština (Rusko) | `ru-RU` | Male | `ru-RU-Pavel`|
| slovenština (Slovensko) | `sk-SK` | Male | `sk-SK-Filip`|
| slovinština (Slovinsko) | `sl-SI` | Male | `sl-SI-Lado`|
| Španělština (Mexiko) | `es-MX` | Female | `es-MX-HildaRUS`|
| Španělština (Mexiko) | `es-MX` | Male | `es-MX-Raul`|
| Španělština (Španělsko) | `es-ES` | Female | `es-ES-HelenaRUS`|
| Španělština (Španělsko) | `es-ES` | Female | `es-ES-Laura`|
| Španělština (Španělsko) | `es-ES` | Male | `es-ES-Pablo`|
| švédština (Švédsko) | `sv-SE` | Female | `sv-SE-HedvigRUS`|
| Tamilština (Indie) | `ta-IN` | Male | `ta-IN-Valluvar`|
| Telugština (Indie) | `te-IN` | Female | `te-IN-Chitra`|
| thajština (Thajsko) | `th-TH` | Male | `th-TH-Pattara`|
| turečtina (Turecko) | `tr-TR` | Female | `tr-TR-SedaRUS`|
| Vietnamština (Vietnam) | `vi-VN` | Male | `vi-VN-An` |

> [!IMPORTANT]
> `en-US-Jessa`Hlas se změnil na `en-US-Aria` . Pokud jste předtím používali "Jessa", převeďte ho na standard ARIA.

> [!TIP]
> V požadavcích na syntézu řeči můžete dál používat úplné mapování názvu služby, jako je například Microsoft Server Speech Převod textu na řeč Voice (EN-US, AriaRUS).

### <a name="customization"></a>Přizpůsobení

Vlastní hlas je k dispozici v úrovni Standard a neuronové. Podporované jazyky jsou pro tyto dvě úrovně odlišné. 

| Jazyk | Národní prostředí | Standard | Neuronové |
|--|--|--|--|
| Čínština (Mandarin, zjednodušená) | `zh-CN` | Yes | Yes |
| Čínština (Mandarin, zjednodušená), anglické dvojjazyčné | `zh-CN` dvojjazyčný | Yes | Yes |
| Angličtina (Austrálie) | `en-AU` | No | Yes |
| Angličtina (Indie) | `en-IN` | Yes | Yes |
| Angličtina (Spojené království) | `en-GB` | Yes | Yes |
| Angličtina (Spojené státy) | `en-US` | Yes | Yes |
| Francouzština (Kanada) | `fr-CA` | No | Yes |
| francouzština (Francie) | `fr-FR` | Yes | Yes |
| němčina (Německo) | `de-DE` | Yes | Yes |
| italština (Itálie) | `it-IT` | Yes | Yes |
| japonština (Japonsko) | `ja-JP` | No | Yes |
| korejština (Jižní Korea) | `ko-KR` | No | Yes |
| Portugalština (Brazílie) | `pt-BR` | Yes | Yes |
| Španělština (Mexiko) | `es-MX` | Yes | Yes |
| Španělština (Španělsko) | `es-ES` | No | Yes |

Vyberte správné národní prostředí, které odpovídá školicím datům, které máte k dispozici pro výuku vlastního hlasového modelu. Například pokud jsou data nahrávání mluveného mluveného zvýraznění v angličtině, vyberte `en-GB` .

> [!NOTE]
> V rámci vlastního hlasu nepodporujeme školicí modely pro BI, s výjimkou Chinese-English BI-vícejazyčná. Pokud chcete naučit čínský hlas, který může mluvit i v angličtině, vyberte možnost dvojjazyčně v angličtině. Chinese-English dvojjazyčné školení pomocí metody Standard je k dispozici pouze v Severní Evropa a Střed USA – sever. Vlastní neuronové hlasové školení je k dispozici v Velká Británie – jih a Východní USA.

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

| Jazyk | Národní prostředí (BCP-47) | Ověřování závislé na textu | Ověřování nezávislé na textu | Identifikace nezávislá na textu |
|----|----|----|----|----|
|Angličtina (USA)  |  en-US  |  ano  |  ano  |  ano |
|Čínština (Mandarin, zjednodušená) | zh-CN     |     Není k dispozici |     ano |     ano|
|Angličtina (Austrálie)     | EN-AU     | Není k dispozici     | ano     | ano|
|Angličtina (Kanada)     | en-CA     | Není k dispozici |     ano |     ano|
|Angličtina (Spojené království)     | en-GB     | Není k dispozici     | ano     | ano|
|Francouzština (Kanada)     | fr – CA     | Není k dispozici     | ano |     ano|
|francouzština (Francie)     | fr-FR     | Není k dispozici     | ano     | ano|
|němčina (Německo)     | de-DE     | Není k dispozici     | ano     | ano|
|Italština | it-IT     |     Není k dispozici     | ano |     ano|
|Japonština     | ja-JP | Není k dispozici     | ano     | ano|
|Portugalština (Brazílie) | pt-BR |     Není k dispozici |     ano |     ano|
|Španělština (Mexiko)     | ES – MX     | Není k dispozici |     ano |     ano|
|Španělština (Španělsko)     | es-ES | Není k dispozici     | ano |     ano|

## <a name="next-steps"></a>Další kroky

* [Vytvoření bezplatného účtu Azure](https://azure.microsoft.com/free/cognitive-services/)
* [Přečtěte si téma rozpoznávání řeči v jazyce C. #](./get-started-speech-to-text.md?pivots=programming-language-chsarp)
