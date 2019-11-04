---
title: Jazyková podpora – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Služba rozpoznávání řeči podporuje mnoho jazyků pro převod řeči na text a převod textu na řeč spolu s překladem řeči. Tento článek poskytuje úplný seznam funkcí jazykové podpory podle funkcí služby.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: af8bb24862c05b232b7bb5d831b1eb3b1add3a7f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468804"
---
# <a name="language-and-region-support-for-the-speech-services"></a>Podpora jazyků a oblastí pro hlasové služby

Různé jazyky jsou podporovány pro různé funkce služby Speech Services. Následující tabulka shrnuje jazykovou podporu.

## <a name="speech-to-text"></a>Převod řeči na text

Sada Microsoft Speech SDK i REST API podporují následující jazyky (národní prostředí). Pro zvýšení přesnosti se přizpůsobení nabídne pro podmnožinu jazyků prostřednictvím nahrávání zvuku a přepisu s popiskem nebo souvisejícího textu: věty.  Přizpůsobení výslovnosti je aktuálně k dispozici pouze pro en-US a de-DE. Další informace o přizpůsobení [najdete tady](how-to-custom-speech.md).

  Národní prostředí | Jazyk | Podporováno | Vlastního
 ------|----------|---------------------|---------------------
 ar – např. | Arabština (Egypt), moderní Standard | Ano | Ano
 ar – SA | Arabština (Saúdská Arábie) | Ano | Ano
 ar-AE | Arabština (Spojené arabské emiráty) | Ano | Ano
 ar – KW | Arabština (Kuvajt) | Ano | Ano
 ar – QA | Arabština (Katar) | Ano | Ano
 CA – ES | Katalánština | Ano | Ne
 da – DK | Dánština (Dánsko) | Ano | Ne
 de-DE | Němčina (Německo) | Ano | Ano
 EN-AU | Angličtina (Austrálie) | Ano | Ano
 en-CA | Angličtina (Kanada) | Ano | Ano
 en-GB | Angličtina (Spojené království) | Ano | Ano
 en-IN | English (India) | Ano | Ano
 EN-NZ | Angličtina (Nový Zéland) | Ano | Ano
 EN-US | Angličtina (USA) | Ano | Ano
 ES-ES | Španělština (Španělsko) | Ano | Ano
 ES – MX | Španělština (Mexiko) | Ano | Ano
 Fi – FI | Finština (Finsko) | Ano | Ne
 fr – CA | Francouzština (Kanada) | Ano | Ano
 fr-FR | Francouzština (Francie) | Ano | Ano
 Gu – IN | Gudžarátština (Indie) | Ano | Ano
 Dobrý den | Hindština (Indie) | Ano | Ano
 IT oddělení IT | Italština (Itálie) | Ano | Ano
 ja-JP | Japonština (Japonsko) | Ano | Ano
 ko – KR | Korejština (Korea) | Ano | Ano
 Mr | Maráthština (Indie) | Ano | Ano
 NB – ne | Norština (Bokmål) (Norsko) | Ano | Ne
 nl – NL | Holandština (Nizozemsko) | Ano | Ano
 pl-PL | Polština (Polsko) | Ano | Ne
 pt – BR | Portugalština (Brazílie) | Ano | Ano
 pt-PT | Portugalština (Portugalsko) | Ano | Ano
 ru-RU | Ruština (Rusko) | Ano | Ano
 sv-SE | Švédština (Švédsko) | Ano | Ne
 Ta – IN | Tamilština (Indie) | Ano | Ano
 te-IN | Telugština (Indie) | Ano | Ano
 zh-CN | Čínština (Mandarin, zjednodušená) | Ano | Ano
 zh – HK | Čínština (kantonština, tradiční) | Ano | Ano
 zh – TW | Čínština (tchajwanský mandarinka) | Ano | Ano
 Th-tou | Thajština (Thajsko) | Ano | Ne
 tr – TR | Turecko | Ano | Ano |


## <a name="text-to-speech"></a>Převod textu na řeč

Sada Microsoft Speech SDK i REST API podporuje tyto hlasy, z nichž každý podporuje konkrétní jazyk a dialekt identifikovaný národním prostředím.

> [!IMPORTANT]
> Ceny se liší pro standardní, vlastní a neuronové hlasy. Další informace najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) .

### <a name="neural-voices"></a>Hlasy neuronové

Neuronové převod textu na řeč je nový typ syntézy řeči, který využívá rozsáhlé sítě neuronové. Při použití hlasu neuronové je syntetizované rozpoznávání řeči skoro neodlišitelné od lidských nahrávek.

Neuronové hlasy se dají použít k zajištění většího přirozeného a poutavého působení s chatovacích robotů o a hlasovými asistenty, konverzí digitálních textů, jako jsou například e-knihy, do audiobooks a vylepšení navigačních systémů v klecích. V případě přirozeného Prosody jako přirozeného a jasného kloubování slov, neuronové hlasy významně omezují naslouchat únavu při interakci uživatelů se systémy AI.

Úplný seznam hlasů neuronové a regionální dostupnosti najdete v tématu [oblasti](regions.md#standard-and-neural-voices).

Národní prostředí | Jazyk | Pohlaví | Úplné mapování názvu služby | Krátký název hlasu
--------|----------|--------|---------|------------
de-DE | Němčina (Německo) | Žena | Hlasový Převod textu na řeč pro Microsoft Server Speech (de-DE, KatjaNeural) | de-DE-KatjaNeural "
EN-US | English (US) | Muž | Hlasový Převod textu na řeč pro Microsoft Server Speech (EN-US, GuyNeural) | "en-US-GuyNeural"
EN-US | English (US) | Žena | Hlasový Převod textu na řeč pro Microsoft Server Speech (EN-US, JessaNeural) | "en-US-JessaNeural"
IT oddělení IT | Italština (Itálie) | Žena |Microsoft Server Speech Převod textu na řeč hlas (IT – IT, ElsaNeural) | "IT-IT-ElsaNeural"
zh-CN | Čínština (kontinentální) | Žena | "Microsoft Server Speech Převod textu na řeč Voice (zh-CN, XiaoxiaoNeural)" | zh-CN-XiaoxiaoNeural "

> [!NOTE]
> V požadavcích na Shrnutí řeči můžete použít buď úplné mapování názvu služby, nebo krátký název hlasu.

### <a name="standard-voices"></a>Standardní hlasy

Více než 75 standardních hlasů je k dispozici ve více než 45 jazycích a národních prostředích, což vám umožní převést text na syntetizované řeč. Další informace o regionální dostupnosti najdete v tématu [oblasti](regions.md#standard-and-neural-voices).

Národní prostředí | Jazyk | Pohlaví | Úplné mapování názvu služby | Krátký název hlasu
-------|----------|---------|----------|----------
ar – například\* | Arabština (Egypt) | Žena | Microsoft Server Speech Převod textu na řeč Voice (ar-EG, Hoda) | ar-EG-Hoda
ar – SA | Arabština (Saúdská Arábie) | Muž | Microsoft Server Speech Převod textu na řeč Voice (ar-SA, Naayf) | ar-SA-Naayf "
BG-BG | Bulharština | Muž | "Microsoft Server Speech Převod textu na řeč Voice (BG-BG, Ivan)" | BG-BG-Ivan
CA – ES | Katalánština (Španělsko) | Žena | Microsoft Server Speech Převod textu na řeč hlas (CA-ES, HerenaRUS) | "Ca-ES-HerenaRUS"
cs-CZ | Čeština | Muž | Hlasový Převod textu na řeč pro Microsoft Server Speech (cs-CZ, Jakub) | cs-CZ-Jakub "
da – DK | dánština | Žena | Microsoft Server Speech Převod textu na řeč Voice (da-DK, HelleRUS) | "da-DK-HelleRUS"
de-AT | Němčina (Rakousko) | Muž | Microsoft Server Speech Převod textu na řeč hlas (de-AT, Michael) | de-AT-Michael
de-CH | Němčina (Švýcarsko) | Muž | Microsoft Server Speech Převod textu na řeč Voice (de-CH; Karsten) | de-CH-Karsten
de-DE | Němčina (Německo) | Žena | Hlasový Převod textu na řeč pro Microsoft Server Speech (de-DE, Hedda) | de-DE-Hedda "
| | | Žena | Hlasový Převod textu na řeč pro Microsoft Server Speech (de-DE, HeddaRUS) | de-DE-HeddaRUS "
| | | Muž | Hlasový Převod textu na řeč pro Microsoft Server Speech (de-DE, Stefan, Apollo) | de-DE-Stefan-Apollo "
El-GR | Řečtina | Muž | Hlasový Převod textu na řeč pro Microsoft Server Speech (El-GR, Stefanos) | "El-GR-Stefanos"
EN-AU | Angličtina (Austrálie) | Žena | Microsoft Server Speech Převod textu na řeč Voice (EN-AU, Catherine) | "en-AU-Catherine"
| | | Žena | Microsoft Server Speech Převod textu na řeč Voice (EN-AU, HayleyRUS) | "en-AU-HayleyRUS"
en-CA | Angličtina (Kanada) | Žena | Microsoft Server Speech Převod textu na řeč hlas (en-CA, Linda) | "en-CA-Linda"
| | | Žena | Microsoft Server Speech Převod textu na řeč hlas (en-CA, HeatherRUS) | "en-CA-HeatherRUS"
en-GB | English (UK) | Žena | Hlasový Převod textu na řeč pro Microsoft Server Speech (en-GB, Zuzana, Apollo) | "en-GB-Zuzana-Apollo"
| | | Žena | Hlasový Převod textu na řeč pro Microsoft Server Speech (en-GB, HazelRUS) | "en-GB-HazelRUS"
| | | Muž | Hlasový Převod textu na řeč pro Microsoft Server Speech (en-GB, Jiří, Apollo) | "en-GB-Jiří-Apollo"
EN-IE | Angličtina (Irsko) | Muž | Microsoft Server Speech Převod textu na řeč Voice (EN-IE, Novák) " | "en-IE-Novák"
en-IN | English (India) | Žena | Microsoft Server Speech Převod textu na řeč Voice (en-IN, Heera, Apollo) | "en-IN-Heera-Apollo"
| | | Žena | Microsoft Server Speech Převod textu na řeč Voice (en-IN, PriyaRUS) | "en-IN-PriyaRUS"
| | | Muž | Microsoft Server Speech Převod textu na řeč Voice (en-IN, Ravi, Apollo) | "en-IN-Ravi-Apollo"
EN-US | English (US) | Žena | Hlasový Převod textu na řeč pro Microsoft Server Speech (EN-US, ZiraRUS) | "en-US-ZiraRUS"
| | | Žena | Hlasový Převod textu na řeč pro Microsoft Server Speech (EN-US, JessaRUS) | "en-US-JessaRUS"
| | | Muž | Hlasový Převod textu na řeč pro Microsoft Server Speech (EN-US, BenjaminRUS) | "en-US-BenjaminRUS"
| | | Žena | Hlasový Převod textu na řeč pro Microsoft Server Speech (EN-US, Jessa24kRUS) | "en-US-Jessa24kRUS"
| | | Muž | Hlasový Převod textu na řeč pro Microsoft Server Speech (EN-US, Guy24kRUS) | "en-US-Guy24kRUS"
ES-ES | Španělština (Španělsko) |Žena | Microsoft Server Speech Převod textu na řeč Voice (ES-ES, Laura, Apollo) | ES-ES-Laura-Apollo "
| | | Žena | Microsoft Server Speech Převod textu na řeč Voice (ES-ES, HelenaRUS) | ES-ES-HelenaRUS "
| | | Muž | Microsoft Server Speech Převod textu na řeč Voice (ES-ES, Pablo, Apollo) | ES-ES-Pablo-Apollo
ES – MX | Španělština (Mexiko) | Žena | "Microsoft Server Speech Převod textu na řeč Voice (ES-MX, HildaRUS)" | ES-MX-HildaRUS
| | | Muž | "Microsoft Server Speech Převod textu na řeč Voice (ES-MX, Raul, Apollo)" | ES-MX-Raul-Apollo
Fi – FI | Finština | Žena | Microsoft Server Speech Převod textu na řeč Voice (Fi-FI, HeidiRUS) | "Fi-FI-HeidiRUS"
fr – CA | Francouzština (Kanada) |Žena | Microsoft Server Speech Převod textu na řeč hlas (fr-CA, Caroline) | "fr-CA-Caroline"
| | | Žena | Microsoft Server Speech Převod textu na řeč hlas (fr-CA, HarmonieRUS) | "fr-CA-HarmonieRUS"
fr – CH | Francouzština (Švýcarsko)| Muž | Microsoft Server Speech Převod textu na řeč Voice (fr-CH, Guillaume) | "fr-CH-Guillaume"
fr-FR | Francouzština (Francie)| Žena | Hlasový Převod textu na řeč pro Microsoft Server Speech (fr-FR, Julie, Apollo) | "fr-FR-Julie-Apollo"
| | | Žena | Hlasový Převod textu na řeč pro Microsoft Server Speech (fr-FR, HortenseRUS) | "fr-FR-HortenseRUS"
| | | Muž | Hlasový Převod textu na řeč pro Microsoft Server Speech (fr-FR, Paul, Apollo) | "fr-FR-Paul-Apollo"
He-IL| Hebrejština (Izrael) | Muž| Microsoft Server Speech Převod textu na řeč hlas (HE-IL, Asaf) | "he-IL-Asaf"
Dobrý den | Hindština (Indie) | Žena | Microsoft Server Speech Převod textu na řeč Voice (dobrý den, Kalpana, Apollo) | "Hi-IN-Kalpana-Apollo"
| | |Žena | Microsoft Server Speech Převod textu na řeč Voice (dobrý den, Kalpana) | "Dobrý den-Kalpana"
| | | Muž | Microsoft Server Speech Převod textu na řeč Voice (dobrý den, Hemant) | "Dobrý den-Hemant"
HR – HR | Chorvatština | Muž | Microsoft Server Speech Převod textu na řeč Voice (HR-HR, Matej) | "HR-HR-Matej"
hu – HU | Maďarština | Muž | Microsoft Server Speech Převod textu na řeč Voice (hu-HU, Szabolcs) | "hu-HU-Szabolcs"
ID – ID | Indonéština| Muž | Hlasový Převod textu na řeč pro Microsoft Server Speech (ID-ID, Andika) | ID-ID-Andika
IT oddělení IT | italština | Muž | Microsoft Server Speech Převod textu na řeč hlas (IT – IT, Cosimo, Apollo) | "IT-IT-Cosimo-Apollo"
| | | Žena | Microsoft Server Speech Převod textu na řeč hlas (IT – IT, LuciaRUS) | "IT-IT-LuciaRUS"
ja-JP | Japonština | Žena | Microsoft Server Speech Převod textu na řeč Voice (ja-JP, Ayumi, Apollo) | "ja-JP-Ayumi-Apollo"
| | | Muž | Microsoft Server Speech Převod textu na řeč Voice (ja-JP, Ichiro, Apollo) | "ja-JP-Ichiro-Apollo"
| | | Žena | Hlasový Převod textu na řeč pro Microsoft Server Speech (ja-JP, HarukaRUS) | "ja-JP-HarukaRUS"
ko – KR | Korejština | Žena | Microsoft Server Speech Převod textu na řeč Voice (ko-KR, HeamiRUS) | "ko-KR-HeamiRUS"
MS-MY | Malajština | Muž | Microsoft Server Speech Převod textu na řeč hlas (MS-MY, Rizwan) | "MS-MY-Rizwan"
NB – ne | norština | Žena | Hlasový Převod textu na řeč pro Microsoft Server Speech (NB-NO, HuldaRUS) | "NB-NO-HuldaRUS"
nl – NL | Holandština | Žena | Microsoft Server Speech Převod textu na řeč Voice (nl-NL, HannaRUS) | nl-NL-HannaRUS "
pl-PL | polština | Žena | Microsoft Server Speech Převod textu na řeč Voice (pl-PL, PaulinaRUS) | pl-PL-PaulinaRUS "
pt – BR | Portugalština (Brazílie) | Žena | Microsoft Server Speech Převod textu na řeč Voice (pt-BR, HeloisaRUS) | pt-BR-HeloisaRUS
| | | Muž |Microsoft Server Speech Převod textu na řeč Voice (pt-BR, Daniel, Apollo) | pt-BR-Daniel-Apollo
pt-PT | Portugalština (Portugalsko) | Žena | "Microsoft Server Speech Převod textu na řeč Voice (pt-PT, HeliaRUS)" | pt-PT-HeliaRUS
RO-RO | Rumunština | Muž | Microsoft Server Speech Převod textu na řeč Voice (RO-RO, Andrei) | "RO-RO-Andrei"
ru-RU |ruština| Žena | Microsoft Server Speech Převod textu na řeč Voice (ru-RU, Irina, Apollo) | ru-RU-Irina-Apollo "
| | | Muž | Microsoft Server Speech Převod textu na řeč Voice (ru-RU, Pavel, Apollo) | ru-RU-Pavel-Apollo "
| | | Žena | Microsoft Server Speech Převod textu na řeč Voice (ru-RU, EkaterinaRUS) | ru-RU – EkaterinaRUS
SK-SK | Slovenština | Muž | Hlasový Převod textu na řeč pro Microsoft Server Speech (SK-SK; Filip) | "SK-SK-Filip"
SL-SI | Slovinština | Muž | Microsoft Server Speech Převod textu na řeč Voice (SL-SI, lado) | "SL-SI-lado"
sv-SE | švédština | Žena | Microsoft Server Speech Převod textu na řeč Voice (sv-SE, HedvigRUS) | "sv-SE-HedvigRUS"
Ta – IN | Tamilština (Indie) | Muž | Microsoft Server Speech Převod textu na řeč Voice (ta-IN, Valluvar) | "ta-IN-Valluvar"
te-IN | Telugština (Indie) | Žena | Hlasový Převod textu na řeč pro Microsoft Server Speech (te-IN, Chitra) | "te-IN-Chitra"
Th-tou | Thajština | Muž | Microsoft Server Speech Převod textu na řeč Voice (Th-tou, Pattara) | "th-TH-Pattara"
tr – TR | turečtina | Žena | "Hlasový Převod textu na řeč pro Microsoft Server Speech (tr-TR, SedaRUS)" | "tr-TR-SedaRUS"
VI – VN | Vietnamština | Muž | Microsoft Server Speech Převod textu na řeč Voice (VI-VN, a) " | "VI-VN-a"
zh-CN | Čínština (kontinentální) | Žena | "Microsoft Server Speech Převod textu na řeč Voice (zh-CN, HuihuiRUS)" | zh-CN-HuihuiRUS "
| | | Žena | Microsoft Server Speech Převod textu na řeč Voice (zh-CN, Yaoyao, Apollo) | zh-CN-Yaoyao-Apollo "
| | | Muž | Microsoft Server Speech Převod textu na řeč Voice (zh-CN, Kangkang, Apollo) | zh-CN-Kangkang-Apollo "
zh – HK | Čínština (Hongkong) | Žena | Microsoft Server Speech Převod textu na řeč Voice (zh-HK, Tracy, Apollo) | "zh-HK-Tracy-Apollo"
| | | Žena | Microsoft Server Speech Převod textu na řeč Voice (zh-HK, TracyRUS) | "zh-HK-TracyRUS"
| | | Muž | Microsoft Server Speech Převod textu na řeč Voice (zh-HK, Danny, Apollo) | "zh-HK-Danny-Apollo"
zh – TW | Čínština (Tchaj-wan) | Žena | Microsoft Server Speech Převod textu na řeč Voice (zh-TW, Yating, Apollo) | "zh-TW-Yating-Apollo"
| | | Žena | Microsoft Server Speech Převod textu na řeč Voice (zh-TW, HanHanRUS) | "zh-TW-HanHanRUS"
| | | Muž | Microsoft Server Speech Převod textu na řeč Voice (zh-TW, Zhiwei, Apollo) | "zh-TW-Zhiwei-Apollo"

\* *ar-eg podporuje moderní arabštinu Standard (MSA).*

> [!NOTE]
> V požadavcích na Shrnutí řeči můžete použít buď úplné mapování názvu služby, nebo krátký název hlasu.

### <a name="customization"></a>Přizpůsobení

Přizpůsobení hlasu je k dispozici pro de-DE, en-GB, en-IN, en-US, ES-MX, fr-FR, IT-IT, pt-BR a zh-CN. Vyberte správné národní prostředí, které odpovídá školicím datům, které máte k dispozici pro výuku vlastního hlasového modelu. Například pokud jsou data nahrávání, která používáte v angličtině, v češtině, vyberte en-GB.  

> [!NOTE]
> Ve vlastním hlasu nepodporujeme školicí modely v jazyce BI, s výjimkou české jazykové verze pro čínské angličtiny. Pokud chcete naučit čínský hlas, který může mluvit i v angličtině, vyberte možnost dvojjazyčně English dvojjazyčně. Hlasové školení ve všech národních prostředích začíná sadou dat 2000 + projevy, s výjimkou en-US a zh-CN, kde můžete začít s jakoukoli velikostí školicích dat.

## <a name="speech-translation"></a>Překlad řeči

Rozhraní API pro **Překlad** řeči podporuje různé jazyky pro překlad hlasu na řeč a převod řeči na text. Zdrojový jazyk musí být vždy z tabulky jazyka Speech-to-text. Dostupné cílové jazyky závisí na tom, zda je cíl překladu řeč nebo text. Příchozí řeč můžete přeložit do více než [60 jazyků](https://www.microsoft.com/translator/business/languages/). K dispozici je podmnožina těchto jazyků pro [syntézu řeči](language-support.md#text-languages).

### <a name="text-languages"></a>Jazyky textu

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
| Fijian      | `fj`          |
| Filipínština      | `fil`          |
| Finština      | `fi`          |
| Francouzština      | `fr`          |
| Němčina      | `de`          |
| Řečtina      | `el`          |
| Haitská kreolština      | `ht`          |
| Hebrejština      | `he`          |
| Hindština      | `hi`          |
| Hmong Macek      | `mww`          |
| Maďarština      | `hu`          |
| Indonéština      | `id`          |
| italština      | `it`          |
| Japonština      | `ja`          |
| Svahilština      | `sw`          |
| Klingon      | `tlh`          |
| Klingon (plqaD)      | `tlh-Qaak`          |
| Korejština      | `ko`          |
| Lotyština      | `lv`          |
| Litevština      | `lt`          |
| Malgašština      | `mg`          |
| Malajština      | `ms`          |
| Maltština      | `mt`          |
| norština      | `nb`          |
| Perština      | `fa`          |
| polština      | `pl`          |
| Portugalština      | `pt`          |
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
| Thajština      | `th`          |
| Tongánština      | `to`          |
| turečtina      | `tr`          |
| Ukrajinština      | `uk`          |
| Urdština      | `ur`          |
| Vietnamština      | `vi`          |
| Velština      | `cy`          |
| Yucatec Maya      | `yua`          |


## <a name="next-steps"></a>Další kroky

* [Získání zkušebního předplatného služeb Speech Services](https://azure.microsoft.com/try/cognitive-services/)
* [Přečtěte si téma rozpoznávání řeči v jazyce c. #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
