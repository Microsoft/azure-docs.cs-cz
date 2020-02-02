---
title: Jazyková podpora – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Služba rozpoznávání řeči podporuje mnoho jazyků pro převod řeči na text a převod textu na řeč spolu s překladem řeči. Tento článek poskytuje úplný seznam funkcí jazykové podpory podle funkcí služby.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 20b99cfffdaa0d942ccd4d954909810342cbfcb8
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935343"
---
# <a name="language-and-region-support-for-the-speech-service"></a>Podpora jazyků a oblastí pro službu Speech

Jazyková podpora se liší podle funkcí služby Speech. Následující tabulka shrnuje jazykovou podporu pro nabídky převodu [řeči na text](#speech-to-text), převod [textu na řeč](#text-to-speech)a [rozpoznávání řeči](#speech-translation) .

## <a name="speech-to-text"></a>Převod řeči na text

Sada Microsoft Speech SDK i REST API podporují následující jazyky (národní prostředí). Pro zvýšení přesnosti se přizpůsobení nabídne pro podmnožinu jazyků prostřednictvím nahrávání zvuku a přepisu s popiskem nebo souvisejícího textu: věty. Přizpůsobení výslovnosti je aktuálně k dispozici pouze pro `en-US` a `de-DE`. Další informace o přizpůsobení [najdete tady](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

 Národní prostředí | Jazyk | Podporováno | Přizpůsobení
------|------------|-----------|-------------
`ar-AE` | Arabština (Spojené arabské emiráty) | Ano | Ne
`ar-BH` | Arabština (Bahrajn) | Ano | Jazykový model
`ar-EG` | Arabština (Egypt), moderní standard | Ano | Jazykový model
`ar-KW` | Arabština (Kuvajt) | Ano | Ne
`ar-QA` | Arabština (Katar) | Ano | Ne
`ar-SA` | Arabština (Saúdská Arábie) | Ano | Ne
`ca-ES` | Katalánština | Ano | Jazykový model
`da-DK` | Dánština (Dánsko) | Ano | Jazykový model
`de-DE` | Němčina (Německo) | Ano | Akustický model<br>Jazykový model<br>Výslovnost
`en-AU` | angličtina (Austrálie) | Ano | Akustický model<br>Jazykový model
`en-CA` | angličtina (Kanada) | Ano | Akustický model<br>Jazykový model
`en-GB` | Angličtina (Spojené království) | Ano | Akustický model<br>Jazykový model<br>Výslovnost
`en-IN` | English (India) | Ano | Akustický model<br>Jazykový model
`en-NZ` | angličtina (Nový Zéland) | Ano | Akustický model<br>Jazykový model
`en-US` | Angličtina (Spojené státy) | Ano | Akustický model<br>Jazykový model<br>Výslovnost
`es-ES` | Španělština (Španělsko) | Ano | Akustický model<br>Jazykový model
`es-MX` | Španělština (Mexiko) | Ano | Akustický model<br>Jazykový model
`fi-FI` | Finština (Finsko) | Ano | Jazykový model
`fr-CA` | Francouzština (Kanada) | Ano | Akustický model<br>Jazykový model
`fr-FR` | Francouzština (Francie) | Ano | Akustický model<br>Jazykový model<br>Výslovnost
`gu-IN` | Gudžarátština (Indie) | Ano | Jazykový model
`hi-IN` | hindština (Indie) | Ano | Akustický model<br>Jazykový model
`it-IT` | Italština (Itálie) | Ano | Akustický model<br>Jazykový model<br>Výslovnost
`ja-JP` | Japonština (Japonsko) | Ano | Jazykový model
`ko-KR` | Korejština (Jižní Korea) | Ano | Jazykový model
`mr-IN` | Maráthština (Indie) | Ano | Jazykový model
`nb-NO` | Norština (Bokmal) (Norsko) | Ano | Jazykový model
`nl-NL` | Nizozemština (Nizozemsko) | Ano | Jazykový model
`pl-PL` | Polština (Polsko) | Ano | Jazykový model
`pt-BR` | Portugalština (Brazílie) | Ano | Akustický model<br>Jazykový model<br>Výslovnost
`pt-PT` | Portugalština (Portugalsko) | Ano | Jazykový model
`ru-RU` | Ruština (Rusko) | Ano | Akustický model<br>Jazykový model
`sv-SE` | Švédština (Švédsko) | Ano | Jazykový model
`ta-IN` | tamilština (Indie) | Ano | Jazykový model
`te-IN` | telugština (Indie) | Ano | Ne
`th-TH` | thajština (Thajsko) | Ano | Ne
`tr-TR` | Turečtina (Turecko) | Ano | Ne
`zh-CN` | Čínština (Mandarínština, zjednodušená čínština) | Ano | Akustický model<br>Jazykový model
`zh-HK` | Čínština (kantonština, tradiční) | Ano | Jazykový model
`zh-TW` | Čínština (Tchaj-wanská Mandarínština) | Ano | Jazykový model

## <a name="text-to-speech"></a>Převod textu na řeč

Sada Microsoft Speech SDK a rozhraní REST API podporují tyto hlasy, z nichž každý podporuje konkrétní jazyk a dialekt identifikovaný národním prostředím.

> [!IMPORTANT]
> Ceny se liší pro standardní, vlastní a neuronové hlasy. Další informace najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) .

### <a name="neural-voices"></a>Hlasy neuronové

Neuronové převod textu na řeč je nový typ syntézy řeči, který využívá rozsáhlé sítě neuronové. Při použití hlasu neuronové je syntetizované rozpoznávání řeči skoro neodlišitelné od lidských nahrávek.

Neuronové hlasy se dají použít k zajištění většího přirozeného a poutavého působení s chatovacích robotů o a hlasovými asistenty, konverzí digitálních textů, jako jsou například e-knihy, do audiobooks a vylepšení navigačních systémů v klecích. V případě přirozeného Prosody jako přirozeného a jasného kloubování slov, neuronové hlasy významně omezují naslouchat únavu při interakci uživatelů se systémy AI.

Další informace o regionální dostupnosti najdete v tématu [oblasti](regions.md#standard-and-neural-voices).

Národní prostředí | Jazyk | Pohlaví | Úplné mapování názvu služby | Krátký název hlasu
--------|----------|--------|---------|------------
`de-DE` | Němčina (Německo) | Žena | Hlasový Převod textu na řeč pro Microsoft Server Speech (de-DE, KatjaNeural) | de-DE-KatjaNeural "
`en-US` | English (US) | Muž | Hlasový Převod textu na řeč pro Microsoft Server Speech (EN-US, GuyNeural) | "en-US-GuyNeural"
`en-US` | English (US) | Žena | Hlasový Převod textu na řeč pro Microsoft Server Speech (EN-US, JessaNeural) | "en-US-JessaNeural"
`it-IT` | Italština (Itálie) | Žena |Microsoft Server Speech Převod textu na řeč hlas (IT – IT, ElsaNeural) | "IT-IT-ElsaNeural"
`zh-CN` | Čínština (Španělsko) | Žena | "Microsoft Server Speech Převod textu na řeč Voice (zh-CN, XiaoxiaoNeural)" | "zh-CN-XiaoxiaoNeural"

Pokud se chcete dozvědět, jak můžete nakonfigurovat a upravit hlasy neuronové, přečtěte si téma věnované [jazykům pro označování řeči](speech-synthesis-markup.md#adjust-speaking-styles).

> [!NOTE]
> V požadavcích na Shrnutí řeči můžete použít buď úplné mapování názvu služby, nebo krátký název hlasu.

### <a name="standard-voices"></a>Standardní hlasy

Více než 75 standardních hlasů je k dispozici ve více než 45 jazycích a národních prostředích, což vám umožní převést text na syntetizované řeč. Další informace o regionální dostupnosti najdete v tématu [oblasti](regions.md#standard-and-neural-voices).

Národní prostředí | Jazyk | Pohlaví | Úplné mapování názvu služby | Krátký název
-------|----------|---------|----------|----------
<sup>1</sup>`ar-EG` | arabština (Egypt) | Žena | "Microsoft Server řeči Text na řeč hlas (ar např Hoda)" | ar-EG-Hoda
`ar-SA` | Arabština (Saúdská Arábie) | Muž | "Microsoft serveru řeči Text na řeč hlas (ar-SA, Naayf)" | ar-SA-Naayf "
`bg-BG` | Bulharština | Muž | "Microsoft Server řeči převod textu na řeč hlasové (bg – BG, Ivan)" | BG-BG-Ivan
`ca-ES` | katalánština (Španělsko) | Žena | "Microsoft Server řeči převod textu na řeč hlasové (ca-ES, HerenaRUS)" | "Ca-ES-HerenaRUS"
`cs-CZ` | Čeština | Muž | "Microsoft Server řeči Text na řeč hlas (cs-CZ, Jakub)" | cs-CZ-Jakub "
`da-DK` | dánština | Žena | "Microsoft serveru řeči Text na řeč hlas (da-DK HelleRUS)" | "da-DK-HelleRUS"
`de-AT` | němčina (Rakousko) | Muž | "Microsoft Server řeči Text na řeč hlas (de-AT, musí Michael)" | de-AT-Michael
`de-CH` | němčina (Švýcarsko) | Muž | "Microsoft serveru řeči Text na řeč hlas (de-CH, Karsten)" | de-CH-Karsten
`de-DE` | Němčina (Německo) | Žena | "Microsoft serveru řeči Text na řeč hlas (de-DE, Hedda)" | de-DE-Hedda "
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (de-DE, HeddaRUS)" | de-DE-HeddaRUS "
| | | Muž | "Microsoft serveru řeči Text na řeč hlas (de-DE, Stefan, Apollo)" | de-DE-Stefan-Apollo "
`el-GR` | Řečtina | Muž | "Microsoft serveru řeči Text na řeč hlas (el-GR, Stefanos)" | "El-GR-Stefanos"
`en-AU` | angličtina (Austrálie) | Žena | "Microsoft serveru řeči Text na řeč hlas (en-AU, Catherine)" | "en-AU-Catherine"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (en-AU, HayleyRUS)" | "en-AU-HayleyRUS"
`en-CA` | angličtina (Kanada) | Žena | "Microsoft serveru řeči Text na řeč hlas (en-CA, Lenka)" | "en-CA-Linda"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (en-CA, HeatherRUS)" | "en-CA-HeatherRUS"
`en-GB` | English (UK) | Žena | "Microsoft serveru řeči Text na řeč hlas (en-GB, Susan, Apollo)" | "en-GB-Zuzana-Apollo"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (en-GB, HazelRUS)" | "en-GB-HazelRUS"
| | | Muž | "Microsoft serveru řeči Text na řeč hlas (en-GB, George, Apollo)" | "en-GB-Jiří-Apollo"
`en-IE` | angličtina (Irsko) | Muž | "Microsoft serveru řeči Text na řeč hlas (en-IE, Sean)" | "en-IE-Novák"
`en-IN` | English (India) | Žena | "Microsoft serveru řeči Text na řeč hlas (en-IN, Heera, Apollo)" | "en-IN-Heera-Apollo"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (en-IN, PriyaRUS)" | "en-IN-PriyaRUS"
| | | Muž | "Microsoft serveru řeči Text na řeč hlas (en-IN, Ravi, Apollo)" | "en-IN-Ravi-Apollo"
`en-US` | English (US) | Žena | "Microsoft serveru řeči Text na řeč hlas (en US, ZiraRUS)" | "en-US-ZiraRUS"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (en US, JessaRUS)" | "en-US-JessaRUS"
| | | Muž | "Microsoft serveru řeči Text na řeč hlas (en US, BenjaminRUS)" | "en-US-BenjaminRUS"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (en US, Jessa24kRUS)" | "en-US-Jessa24kRUS"
| | | Muž | "Microsoft serveru řeči Text na řeč hlas (en US, Guy24kRUS)" | "en-US-Guy24kRUS"
`es-ES` | Španělština (Španělsko) |Žena | "Microsoft Server řeči převod textu na řeč hlasové (es-ES, Laura, Apollo)" | ES-ES-Laura-Apollo "
| | | Žena | "Microsoft Server řeči převod textu na řeč hlasové (es-ES, HelenaRUS)" | ES-ES-HelenaRUS "
| | | Muž | "Microsoft Server řeči převod textu na řeč hlasové (es-ES, tablet Pablo, Apollo)" | ES-ES-Pablo-Apollo
`es-MX` | Španělština (Mexiko) | Žena | "Microsoft Server řeči převod textu na řeč hlasové (es-MX, HildaRUS)" | ES-MX-HildaRUS
| | | Muž | "Microsoft Server řeči převod textu na řeč hlasové (es-MX, Raul, Apollo)" | ES-MX-Raul-Apollo
`fi-FI` | Finština | Žena | "Microsoft serveru řeči Text na řeč hlas (fi-FI, HeidiRUS)" | "Fi-FI-HeidiRUS"
`fr-CA` | Francouzština (Kanada) |Žena | "Microsoft Server řeči převod textu na řeč hlasové (fr-CA, Caroline)" | "fr-CA-Caroline"
| | | Žena | "Microsoft Server řeči převod textu na řeč hlasové (fr-CA, HarmonieRUS)" | "fr-CA-HarmonieRUS"
`fr-CH` | francouzština (Švýcarsko)| Muž | "Microsoft Server řeči převod textu na řeč hlasové (fr-CH, Guillaume)" | "fr-CH-Guillaume"
`fr-FR` | Francouzština (Francie)| Žena | "Microsoft Server řeči převod textu na řeč hlasové (fr-FR, Julie, Apollo)" | "fr-FR-Julie-Apollo"
| | | Žena | "Microsoft Server řeči převod textu na řeč hlasové (fr-FR, HortenseRUS)" | "fr-FR-HortenseRUS"
| | | Muž | "Microsoft Server řeči převod textu na řeč hlasové (fr-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo"
`he-IL` | hebrejština (Izrael) | Muž| "Microsoft serveru řeči Text na řeč hlas (he-IL, Asaf)" | "he-IL-Asaf"
`hi-IN` | hindština (Indie) | Žena | "Microsoft serveru řeči Text na řeč hlas (hi v, Kalpana, Apollo)" | "Hi-IN-Kalpana-Apollo"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (hi v, Kalpana)" | "Dobrý den-Kalpana"
| | | Muž | "Microsoft serveru řeči Text na řeč hlas (hi v, Hemant)" | "Dobrý den-Hemant"
`hr-HR` | Chorvatština | Muž | "Microsoft Server řeči převod textu na řeč hlasové (hr-HR, Matej)" | "HR-HR-Matej"
`hu-HU` | Maďarština | Muž | "Microsoft serveru řeči Text na řeč hlas (hu-HU, Szabolcs)" | "hu-HU-Szabolcs"
`id-ID` | Indonéština| Muž | "Microsoft serveru řeči Text na řeč hlas (id-ID, Andika)" | ID-ID-Andika
`it-IT` | italština | Muž | "Microsoft serveru řeči Text hlas řeči (it-IT, Cosimo, Apollo)" | "IT-IT-Cosimo-Apollo"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (it-IT, LuciaRUS)" | "IT-IT-LuciaRUS"
`ja-JP` | Japonština | Žena | "Microsoft Server řeči převod textu na řeč hlasové (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo"
| | | Muž | "Microsoft Server řeči převod textu na řeč hlasové (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo"
| | | Žena | "Microsoft Server řeči převod textu na řeč hlasové (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS"
`ko-KR` | Korejština | Žena | "Microsoft serveru řeči Text na řeč hlas (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS"
`ms-MY` | Malajština | Muž | "Microsoft Server řeči převod textu na řeč hlasové (ms Moje, Rizwan)" | "MS-MY-Rizwan"
`nb-NO` | norština | Žena | "Microsoft Server řeči převod textu na řeč hlasové (nb-NO HuldaRUS)" | "NB-NO-HuldaRUS"
`nl-NL` | Holandština | Žena | "Microsoft Server řeči převod textu na řeč hlasové (nl-NL, HannaRUS)" | nl-NL-HannaRUS "
`pl-PL` | polština | Žena | "Microsoft Server řeči převod textu na řeč hlasové (pl-PL, PaulinaRUS)" | pl-PL-PaulinaRUS "
`pt-BR` | Portugalština (Brazílie) | Žena | "Microsoft Server řeči převod textu na řeč hlasové (pt-BR, HeloisaRUS)" | pt-BR-HeloisaRUS
| | | Muž |"Microsoft Server řeči převod textu na řeč hlasové (pt-BR, ADAM, Apollo)" | pt-BR-Daniel-Apollo
`pt-PT` | Portugalština (Portugalsko) | Žena | "Microsoft Server řeči převod textu na řeč hlasové (pt-PT, HeliaRUS)" | pt-PT-HeliaRUS
`ro-RO` | Rumunština | Muž | "Microsoft serveru řeči Text na řeč hlas (ro-RO, Andrei)" | "RO-RO-Andrei"
`ru-RU` |ruština| Žena | "Microsoft Server řeči převod textu na řeč hlasové (ru-RU, Irina, Apollo)" | ru-RU-Irina-Apollo "
| | | Muž | "Microsoft Server řeči převod textu na řeč hlasové (ru-RU, Pavel, Apollo)" | ru-RU-Pavel-Apollo "
| | | Žena | "Microsoft Server řeči převod textu na řeč hlasové (ru-RU, EkaterinaRUS)" | ru-RU – EkaterinaRUS
`sk-SK` | Slovenština | Muž | "Microsoft Server řeči převod textu na řeč hlasové (sk-SK, Filip)" | "SK-SK-Filip"
`sl-SI` | Slovinština | Muž | "Microsoft Server řeči převod textu na řeč hlasové (sl Incidentech Lado)" | "SL-SI-lado"
`sv-SE` | švédština | Žena | "Microsoft serveru řeči Text na řeč hlas (sv-SE, HedvigRUS)" | "sv-SE-HedvigRUS"
`ta-IN` | tamilština (Indie) | Muž | "Microsoft serveru řeči Text na řeč hlas, (ta v, Valluvar)" | "ta-IN-Valluvar"
`te-IN` | telugština (Indie) | Žena | "Microsoft serveru řeči Text na řeč hlas (te v, Chitra)" | "te-IN-Chitra"
`th-TH` | Thajština | Muž | "Microsoft Server řeči převod textu na řeč hlasové (th TÝ, Pattara)" | "th-TH-Pattara"
`tr-TR` | Turečtina (Turecko) | Žena | "Microsoft Server řeči převod textu na řeč hlasové (tr-TR, SedaRUS)" | "tr-TR-SedaRUS"
`vi-VN` | Vietnamština | Muž | "Microsoft Server řeči převod textu na řeč hlasové (vi VN)" | "VI-VN-a"
`zh-CN` | Čínština (Španělsko) | Žena | "Microsoft serveru řeči Text na řeč hlas (zh-CN, HuihuiRUS)" | zh-CN-HuihuiRUS "
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (zh-CN, Yaoyao, Apollo)" | zh-CN-Yaoyao-Apollo "
| | | Muž | "Microsoft serveru řeči Text na řeč hlas (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo"
`zh-HK` | čínština (Hongkong) | Žena | "Microsoft serveru řeči Text na řeč hlas (zh-HK, Tracy, Apollo)" | "zh-HK-Tracy-Apollo"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (zh-HK, TracyRUS)" | "zh-HK-TracyRUS"
| | | Muž | "Microsoft serveru řeči Text na řeč hlas (zh-HK, Danny, Apollo)" | "zh-HK-Danny-Apollo"
`zh-TW` | Čínština (Tchaj-wan) | Žena | "Microsoft serveru řeči Text na řeč hlas (zh-TW, Yating, Apollo)" | "zh-TW-Yating-Apollo"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (zh-TW, HanHanRUS)" | "zh-TW-HanHanRUS"
| | | Muž | "Microsoft serveru řeči Text na řeč hlas (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo"

**1** *ar-eg podporuje moderní arabštinu Standard (MSA).*

> [!NOTE]
> V požadavcích na Shrnutí řeči můžete použít buď úplné mapování názvu služby, nebo krátký název hlasu.

### <a name="customization"></a>Přizpůsobení

Vlastní nastavení hlasu je k dispozici pro `de-DE`, `en-GB`, `en-IN`, `en-US`, `es-MX`, `fr-FR`, `it-IT`, `pt-BR`a `zh-CN`. Vyberte správné národní prostředí, které odpovídá školicím datům, které máte k dispozici pro výuku vlastního hlasového modelu. Například pokud jsou data nahrávání, která používáte v angličtině, označená pomocí britské zvýraznění, vyberte `en-GB`.

> [!NOTE]
> Ve vlastním hlasu nepodporujeme školicí modely v jazyce BI, s výjimkou české jazykové verze pro čínské angličtiny. Pokud chcete naučit čínský hlas, který může mluvit i v angličtině, vyberte možnost dvojjazyčně v angličtině. Hlasové školení ve všech národních prostředích začíná sadou dat 2000 + projevy, s výjimkou `en-US` a `zh-CN`, kde můžete začít s jakoukoli velikostí školicích dat.

## <a name="speech-translation"></a>Překlad řeči

**Překlad řeči** rozhraní API podporuje různé jazyky pro překlad řeči řeči a rozpoznávání řeči na text. Zdrojový jazyk musí být vždy z tabulky jazyka Speech-to-text. Dostupné jazyky cílové závisí na tom, zda cílový překlad řeči nebo text. Může překládat příchozí řeči do více než [60 jazyků](https://www.microsoft.com/translator/business/languages/). K dispozici je podmnožina jazyků pro [syntézu řeči](language-support.md#text-languages).

### <a name="text-languages"></a>Text jazyky

| Jazyk textu    | Kód jazyka |
|:----------- |:-------------:|
| Afrikánština      | `af`          |
| arabština       | `ar`          |
| Bengálština      | `bn`          |
| Bosenština (latinka)      | `bs`          |
| Bulharština      | `bg`          |
| Kantonština (tradiční)      | `yue`          |
| Katalánština      | `ca`          |
| Zjednodušená čínština      | `zh-Hans`          |
| Tradiční čínština      | `zh-Hant`          |
| Chorvatština      | `hr`          |
| Čeština      | `cs`          |
| dánština      | `da`          |
| Holandština      | `nl`          |
| Angličtina      | `en`          |
| Estonština      | `et`          |
| Vládní      | `fj`          |
| Filipínština      | `fil`          |
| Finština      | `fi`          |
| Francouzština      | `fr`          |
| Němčina      | `de`          |
| Řečtina      | `el`          |
| Haitská kreolština      | `ht`          |
| Hebrejština      | `he`          |
| Hindština      | `hi`          |
| Hmong Daw      | `mww`          |
| Maďarština      | `hu`          |
| Indonéština      | `id`          |
| Irština      | `ga`          |
| italština      | `it`          |
| Japonština      | `ja`          |
| Kannadština      | `kn`          |
| Svahilština      | `sw`          |
| Klingon      | `tlh`          |
| Klingon (plqaD)      | `tlh-Qaak`          |
| Korejština      | `ko`          |
| Lotyština      | `lv`          |
| Litevština      | `lt`          |
| Malgašský      | `mg`          |
| Malajština      | `ms`          |
| Malajálamština      | `ml`          |
| Maltština      | `mt`          |
| norština      | `nb`          |
| Perština      | `fa`          |
| polština      | `pl`          |
| Portugalština (Brazílie)      | `pt-br`          |
| Portugalština (Portugalsko)      | `pt-pt`          |
| Paňdžábština      | `pa`          |
| Queretaro Otomi      | `otq`          |
| Rumunština      | `ro`          |
| ruština      | `ru`          |
| Samoan      | `sm`          |
| Srbština (cyrilice)      | `sr-Cyrl`          |
| Srbština (latinka)      | `sr-Latn`          |
| Slovenština     | `sk`          |
| Slovinština      | `sl`          |
| Španělština      | `es`          |
| švédština      | `sv`          |
| Tahitian      | `ty`          |
| Tamilština      | `ta`          |
| Telugština      | `te`          |
| Thajština      | `th`          |
| Tonžská      | `to`          |
| turečtina      | `tr`          |
| Ukrajinština      | `uk`          |
| Urdština      | `ur`          |
| Vietnamština      | `vi`          |
| Velština      | `cy`          |
| Yucatec Maya      | `yua`          |


## <a name="next-steps"></a>Další kroky

* [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Zjistěte, jak rozpoznávat řeč v jazyce C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
