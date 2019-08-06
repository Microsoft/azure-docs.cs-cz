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
ms.date: 07/05/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: ac3cb1d637eae1b4ee0a7db59efe631c7eb1ac6f
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815259"
---
# <a name="language-and-region-support-for-the-speech-services"></a>Podpora jazyků a oblastí pro hlasové služby

Různé jazyky jsou podporovány pro různé funkce služby Speech Services. Následující tabulky shrnují jazykovou podporu.

## <a name="speech-to-text"></a>Převod řeči na text

Sada SDK pro rozpoznávání řeči od Microsoftu i REST API podporují následující jazyky (národní prostředí). Různé úrovně vlastního nastavení jsou k dispozici pro jednotlivé jazyky.

  Kód | Jazyk | [Akustické přizpůsobení](how-to-customize-acoustic-models.md) | [Přizpůsobení jazyka](how-to-customize-language-model.md) | [Výslovnost přizpůsobení](how-to-customize-pronunciation.md)
 ------|----------|---------------------|---------------------|-------------------------
 ar – například | Arabština (Egypt), moderní standard | Ne | Ano | Ne
 ES certifikační autority | Katalánština | Ne | Ne | Ne
 da-DK | Dánština (Dánsko) | Ne | Ne | Ne
 de-DE | Němčina (Německo) | Ano | Ano | Ano
 cs AU | angličtina (Austrálie) | Ne | Ano | Ne
 cs CA | angličtina (Kanada) | Ne | Ano | Ne
 en-GB | Angličtina (Spojené království) | Ne | Ano | Ne
 en-IN | English (India) | Ano | Ano | Ne
 cs NZ | angličtina (Nový Zéland) | Ne | Ano | Ne 
 en-US | Angličtina (Spojené státy) | Ano | Ano | Ano
 es-ES | Španělština (Španělsko) | Ano | Ano | Ne
 es-MX | Španělština (Mexiko) | Ne | Ano | Ne
 fi-FI | Finština (Finsko) | Ne | Ne | Ne
 fr-CA | Francouzština (Kanada) | Ne | Ano | Ne
 fr-FR | Francouzština (Francie) | Ano | Ano | Ne
 Dobrý den – v | hindština (Indie) | Ne | Ano | Ne
 IT-IT | Italština (Itálie) | Ano | Ano | Ne
 ja-JP | Japonština (Japonsko) | Ne | Ano | Ne
 ko-KR | Korejština (Jižní Korea) | Ne | Ano | Ne
 nb-NO | Norština (Bokmal) (Norsko) | Ne | Ne | Ne
 NL-NL | Nizozemština (Nizozemsko) | Ne | Ano | Ne
 pl-PL | Polština (Polsko) | Ne | Ne | Ne
 pt-BR | Portugalština (Brazílie) | Ano | Ano | Ne
 pt-PT | Portugalština (Portugalsko) | Ne | Ano | Ne
 ru-RU | Ruština (Rusko) | Ano | Ano | Ne
 sv-SE | Švédština (Švédsko) | Ne | Ne | Ne
 zh-CN | Čínština (Mandarínština, zjednodušená čínština) | Ano | Ano | Ne
 zh-HK | Čínština (kantonština, tradiční) | Ne | Ano | Ne
 zh-TW | Čínština (Tchaj-wanská Mandarínština) | Ne | Ano | Ne
 th TH | thajština (Thajsko) | Ne | Ne | Ne


## <a name="text-to-speech"></a>Převod textu na řeč

REST API převodu textu na řeč podporuje tyto hlasy, z nichž každý podporuje konkrétní jazyk a dialekt identifikovaný národním prostředím.

> [!IMPORTANT]
> Ceny se liší pro standardní, vlastní a neuronové hlasy. Další informace najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) .

### <a name="neural-voices"></a>Hlasy neuronové

Neuronové převod textu na řeč je nový typ syntézy řeči, který využívá rozsáhlé sítě neuronové. Při použití hlasu neuronové je syntetizované rozpoznávání řeči skoro neodlišitelné od lidských nahrávek.

Neuronové hlasy se dají použít k zajištění většího přirozeného a poutavého působení s chatovacích robotů o a virtuálními asistenty, konverzí digitálních textů, jako jsou například e-knihy, do audiobooks a vylepšení navigačních systémů v klecích. V případě přirozeného Prosody jako přirozeného a jasného kloubování slov, neuronové hlasy významně omezují naslouchat únavu při interakci uživatelů se systémy AI.

Úplný seznam hlasů neuronové a regionální dostupnosti najdete v tématu [oblasti](regions.md#standard-and-neural-voices).

Národní prostředí | Jazyk | Pohlaví | Úplné mapování názvu služby | Krátký název hlasu
--------|----------|--------|---------|------------
de-DE | Němčina (Německo) | Žena | Hlasový Převod textu na řeč pro Microsoft Server Speech (de-DE, KatjaNeural) | de-DE-KatjaNeural "
en-US | English (US) | Muž | Hlasový Převod textu na řeč pro Microsoft Server Speech (EN-US, GuyNeural) | "en-US-GuyNeural"
en-US | English (US) | Žena | Hlasový Převod textu na řeč pro Microsoft Server Speech (EN-US, JessaNeural) | "en-US-JessaNeural"
IT-IT | Italština (Itálie) | Žena |Microsoft Server Speech Převod textu na řeč hlas (IT – IT, ElsaNeural) | "IT-IT-ElsaNeural"
zh-CN | Čínština (Španělsko) | Žena | "Microsoft Server Speech Převod textu na řeč Voice (zh-CN, XiaoxiaoNeural)" | "zh-CN-XiaoxiaoNeural"

> [!NOTE]
> V požadavcích na Shrnutí řeči můžete použít buď úplné mapování názvu služby, nebo krátký název hlasu.

### <a name="standard-voices"></a>Standardní hlasy

Více než 75 standardních hlasů je k dispozici ve více než 45 jazycích a národních prostředích, což vám umožní převést text na syntetizované řeč. Další informace o regionální dostupnosti najdete v tématu [oblasti](regions.md#standard-and-neural-voices).

Národní prostředí | Jazyk | Pohlaví | Úplné mapování názvu služby | Krátký název hlasu
-------|----------|---------|----------|----------
ar – například\* | arabština (Egypt) | Žena | "Microsoft Server řeči Text na řeč hlas (ar např Hoda)" | ar-EG-Hoda
ar-SA | Arabština (Saúdská Arábie) | Muž | "Microsoft serveru řeči Text na řeč hlas (ar-SA, Naayf)" | ar-SA-Naayf "
bg-BG | Bulharština | Muž | "Microsoft Server řeči převod textu na řeč hlasové (bg – BG, Ivan)" | BG-BG-Ivan
ES certifikační autority | katalánština (Španělsko) | Žena | "Microsoft Server řeči převod textu na řeč hlasové (ca-ES, HerenaRUS)" | "Ca-ES-HerenaRUS"
cs-CZ | Čeština | Muž | "Microsoft Server řeči Text na řeč hlas (cs-CZ, Jakub)" | cs-CZ-Jakub "
da-DK | dánština | Žena | "Microsoft serveru řeči Text na řeč hlas (da-DK HelleRUS)" | "da-DK-HelleRUS"
de-AT | němčina (Rakousko) | Muž | "Microsoft Server řeči Text na řeč hlas (de-AT, musí Michael)" | de-AT-Michael
de-CH | němčina (Švýcarsko) | Muž | "Microsoft serveru řeči Text na řeč hlas (de-CH, Karsten)" | de-CH-Karsten
de-DE | Němčina (Německo) | Žena | "Microsoft serveru řeči Text na řeč hlas (de-DE, Hedda)" | de-DE-Hedda "
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (de-DE, HeddaRUS)" | de-DE-HeddaRUS "
| | | Muž | "Microsoft serveru řeči Text na řeč hlas (de-DE, Stefan, Apollo)" | de-DE-Stefan-Apollo "
el-GR | Řečtina | Muž | "Microsoft serveru řeči Text na řeč hlas (el-GR, Stefanos)" | "El-GR-Stefanos"
cs AU | angličtina (Austrálie) | Žena | "Microsoft serveru řeči Text na řeč hlas (en-AU, Catherine)" | "en-AU-Catherine"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (en-AU, HayleyRUS)" | "en-AU-HayleyRUS"
cs CA | angličtina (Kanada) | Žena | "Microsoft serveru řeči Text na řeč hlas (en-CA, Lenka)" | "en-CA-Linda"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (en-CA, HeatherRUS)" | "en-CA-HeatherRUS"
en-GB | English (UK) | Žena | "Microsoft serveru řeči Text na řeč hlas (en-GB, Susan, Apollo)" | "en-GB-Zuzana-Apollo"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (en-GB, HazelRUS)" | "en-GB-HazelRUS"
| | | Muž | "Microsoft serveru řeči Text na řeč hlas (en-GB, George, Apollo)" | "en-GB-Jiří-Apollo"
cs IE | angličtina (Irsko) | Muž | "Microsoft serveru řeči Text na řeč hlas (en-IE, Sean)" | "en-IE-Novák"
en-IN | English (India) | Žena | "Microsoft serveru řeči Text na řeč hlas (en-IN, Heera, Apollo)" | "en-IN-Heera-Apollo"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (en-IN, PriyaRUS)" | "en-IN-PriyaRUS"
| | | Muž | "Microsoft serveru řeči Text na řeč hlas (en-IN, Ravi, Apollo)" | "en-IN-Ravi-Apollo"
en-US | English (US) | Žena | "Microsoft serveru řeči Text na řeč hlas (en US, ZiraRUS)" | "en-US-ZiraRUS"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (en US, JessaRUS)" | "en-US-JessaRUS"
| | | Muž | "Microsoft serveru řeči Text na řeč hlas (en US, BenjaminRUS)" | "en-US-BenjaminRUS"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (en US, Jessa24kRUS)" | "en-US-Jessa24kRUS"
| | | Muž | "Microsoft serveru řeči Text na řeč hlas (en US, Guy24kRUS)" | "en-US-Guy24kRUS"
es-ES | Španělština (Španělsko) |Žena | "Microsoft Server řeči převod textu na řeč hlasové (es-ES, Laura, Apollo)" | ES-ES-Laura-Apollo "
| | | Žena | "Microsoft Server řeči převod textu na řeč hlasové (es-ES, HelenaRUS)" | ES-ES-HelenaRUS "
| | | Muž | "Microsoft Server řeči převod textu na řeč hlasové (es-ES, tablet Pablo, Apollo)" | ES-ES-Pablo-Apollo
es-MX | Španělština (Mexiko) | Žena | "Microsoft Server řeči převod textu na řeč hlasové (es-MX, HildaRUS)" | ES-MX-HildaRUS
| | | Muž | "Microsoft Server řeči převod textu na řeč hlasové (es-MX, Raul, Apollo)" | ES-MX-Raul-Apollo
fi-FI | Finština | Žena | "Microsoft serveru řeči Text na řeč hlas (fi-FI, HeidiRUS)" | "Fi-FI-HeidiRUS"
fr-CA | Francouzština (Kanada) |Žena | "Microsoft Server řeči převod textu na řeč hlasové (fr-CA, Caroline)" | "fr-CA-Caroline"
| | | Žena | "Microsoft Server řeči převod textu na řeč hlasové (fr-CA, HarmonieRUS)" | "fr-CA-HarmonieRUS"
FR-CH | francouzština (Švýcarsko)| Muž | "Microsoft Server řeči převod textu na řeč hlasové (fr-CH, Guillaume)" | "fr-CH-Guillaume"
fr-FR | Francouzština (Francie)| Žena | "Microsoft Server řeči převod textu na řeč hlasové (fr-FR, Julie, Apollo)" | "fr-FR-Julie-Apollo"
| | | Žena | "Microsoft Server řeči převod textu na řeč hlasové (fr-FR, HortenseRUS)" | "fr-FR-HortenseRUS"
| | | Muž | "Microsoft Server řeči převod textu na řeč hlasové (fr-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo"
he-IL| hebrejština (Izrael) | Muž| "Microsoft serveru řeči Text na řeč hlas (he-IL, Asaf)" | "he-IL-Asaf"
Dobrý den – v | hindština (Indie) | Žena | "Microsoft serveru řeči Text na řeč hlas (hi v, Kalpana, Apollo)" | "Hi-IN-Kalpana-Apollo"
| | |Žena | "Microsoft serveru řeči Text na řeč hlas (hi v, Kalpana)" | "Dobrý den-Kalpana"
| | | Muž | "Microsoft serveru řeči Text na řeč hlas (hi v, Hemant)" | "Dobrý den-Hemant"
hr-HR | Chorvatština | Muž | "Microsoft Server řeči převod textu na řeč hlasové (hr-HR, Matej)" | "HR-HR-Matej"
hu-HU | Maďarština | Muž | "Microsoft serveru řeči Text na řeč hlas (hu-HU, Szabolcs)" | "hu-HU-Szabolcs"
ID ID | Indonéština| Muž | "Microsoft serveru řeči Text na řeč hlas (id-ID, Andika)" | ID-ID-Andika
IT-IT | italština | Muž | "Microsoft serveru řeči Text hlas řeči (it-IT, Cosimo, Apollo)" | "IT-IT-Cosimo-Apollo"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (it-IT, LuciaRUS)" | "IT-IT-LuciaRUS"
ja-JP | Japonština | Žena | "Microsoft Server řeči převod textu na řeč hlasové (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo"
| | | Muž | "Microsoft Server řeči převod textu na řeč hlasové (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo"
| | | Žena | "Microsoft Server řeči převod textu na řeč hlasové (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS"
ko-KR | Korejština | Žena | "Microsoft serveru řeči Text na řeč hlas (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS"
Moje MS | Malajština | Muž | "Microsoft Server řeči převod textu na řeč hlasové (ms Moje, Rizwan)" | "MS-MY-Rizwan"
nb-NO | norština | Žena | "Microsoft Server řeči převod textu na řeč hlasové (nb-NO HuldaRUS)" | "NB-NO-HuldaRUS"
NL-NL | Holandština | Žena | "Microsoft Server řeči převod textu na řeč hlasové (nl-NL, HannaRUS)" | nl-NL-HannaRUS "
pl-PL | polština | Žena | "Microsoft Server řeči převod textu na řeč hlasové (pl-PL, PaulinaRUS)" | pl-PL-PaulinaRUS "
pt-BR | Portugalština (Brazílie) | Žena | "Microsoft Server řeči převod textu na řeč hlasové (pt-BR, HeloisaRUS)" | pt-BR-HeloisaRUS
| | | Muž |"Microsoft Server řeči převod textu na řeč hlasové (pt-BR, ADAM, Apollo)" | pt-BR-Daniel-Apollo
pt-PT | Portugalština (Portugalsko) | Žena | "Microsoft Server řeči převod textu na řeč hlasové (pt-PT, HeliaRUS)" | pt-PT-HeliaRUS
ro RO | Rumunština | Muž | "Microsoft serveru řeči Text na řeč hlas (ro-RO, Andrei)" | "RO-RO-Andrei"
ru-RU |ruština| Žena | "Microsoft Server řeči převod textu na řeč hlasové (ru-RU, Irina, Apollo)" | ru-RU-Irina-Apollo "
| | | Muž | "Microsoft Server řeči převod textu na řeč hlasové (ru-RU, Pavel, Apollo)" | ru-RU-Pavel-Apollo "
| | | Žena | "Microsoft Server řeči převod textu na řeč hlasové (ru-RU, EkaterinaRUS)" | ru-RU – EkaterinaRUS
sk-SK | Slovenština | Muž | "Microsoft Server řeči převod textu na řeč hlasové (sk-SK, Filip)" | "SK-SK-Filip"
sl SI | Slovinština | Muž | "Microsoft Server řeči převod textu na řeč hlasové (sl Incidentech Lado)" | "SL-SI-lado"
sv-SE | švédština | Žena | "Microsoft serveru řeči Text na řeč hlas (sv-SE, HedvigRUS)" | "sv-SE-HedvigRUS"
ta v | tamilština (Indie) | Muž | "Microsoft serveru řeči Text na řeč hlas, (ta v, Valluvar)" | "ta-IN-Valluvar"
te v | telugština (Indie) | Žena | "Microsoft serveru řeči Text na řeč hlas (te v, Chitra)" | "te-IN-Chitra"
th TH | Thajština | Muž | "Microsoft Server řeči převod textu na řeč hlasové (th TÝ, Pattara)" | "th-TH-Pattara"
tr-TR | turečtina | Žena | "Microsoft Server řeči převod textu na řeč hlasové (tr-TR, SedaRUS)" | "tr-TR-SedaRUS"
vi-VN | Vietnamština | Muž | "Microsoft Server řeči převod textu na řeč hlasové (vi VN)" | "VI-VN-a"
zh-CN | Čínština (Španělsko) | Žena | "Microsoft serveru řeči Text na řeč hlas (zh-CN, HuihuiRUS)" | zh-CN-HuihuiRUS "
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (zh-CN, Yaoyao, Apollo)" | zh-CN-Yaoyao-Apollo "
| | | Muž | "Microsoft serveru řeči Text na řeč hlas (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo"
zh-HK | čínština (Hongkong) | Žena | "Microsoft serveru řeči Text na řeč hlas (zh-HK, Tracy, Apollo)" | "zh-HK-Tracy-Apollo"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (zh-HK, TracyRUS)" | "zh-HK-TracyRUS"
| | | Muž | "Microsoft serveru řeči Text na řeč hlas (zh-HK, Danny, Apollo)" | "zh-HK-Danny-Apollo"
zh-TW | Čínština (Tchaj-wan) | Žena | "Microsoft serveru řeči Text na řeč hlas (zh-TW, Yating, Apollo)" | "zh-TW-Yating-Apollo"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (zh-TW, HanHanRUS)" | "zh-TW-HanHanRUS"
| | | Muž | "Microsoft serveru řeči Text na řeč hlas (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo"

\* *ar – třeba podporuje moderní standardní Arabština (MSA).*

> [!NOTE]
> V požadavcích na Shrnutí řeči můžete použít buď úplné mapování názvu služby, nebo krátký název hlasu.

### <a name="customization"></a>Přizpůsobení

Přizpůsobení hlasu je k dispozici pro de-DE, en-GB, en-IN, en-US, ES-MX, fr-FR, IT-IT, pt-BR a zh-CN. Vyberte správné národní prostředí, které odpovídá školicím datům, které máte k dispozici pro výuku vlastního hlasového modelu. Například pokud jsou data nahrávání, která používáte v angličtině, v češtině, vyberte en-GB.  

> [!NOTE]
> Ve vlastním hlasu nepodporujeme školicí modely v jazyce BI, s výjimkou české jazykové verze pro čínské angličtiny. Pokud chcete naučit čínský hlas, který může mluvit i v angličtině, vyberte možnost dvojjazyčně English dvojjazyčně. Hlasové školení ve všech národních prostředích začíná sadou dat 2000 + projevy, s výjimkou en-US a zh-CN, kde můžete začít s jakoukoli velikostí školicích dat.

## <a name="speech-translation"></a>Překlad řeči

**Překlad řeči** rozhraní API podporuje různé jazyky pro překlad řeči řeči a rozpoznávání řeči na text. Zdrojový jazyk musí být vždy z tabulky jazyka Speech-to-text. Dostupné jazyky cílové závisí na tom, zda cílový překlad řeči nebo text. Může překládat příchozí řeči do více než [60 jazyků](https://www.microsoft.com/translator/business/languages/). Jsou k dispozici pro podmnožinu těchto jazyků [syntézu řeči](language-support.md#text-languages).

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
| italština      | `it`          |
| Japonština      | `ja`          |
| Svahilština      | `sw`          |
| Klingon      | `tlh`          |
| Klingon (plqaD)      | `tlh-Qaak`          |
| Korejština      | `ko`          |
| Lotyština      | `lv`          |
| Litevština      | `lt`          |
| Malgašský      | `mg`          |
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
| Tonžská      | `to`          |
| turečtina      | `tr`          |
| Ukrajinština      | `uk`          |
| Urdština      | `ur`          |
| Vietnamština      | `vi`          |
| Velština      | `cy`          |
| Yucatec Maya      | `yua`          |


## <a name="next-steps"></a>Další postup

* [Získání zkušebního předplatného služeb Speech Services](https://azure.microsoft.com/try/cognitive-services/)
* [Zjistěte, jak rozpoznávat řeč v jazyce C#](quickstart-csharp-dotnet-windows.md)
