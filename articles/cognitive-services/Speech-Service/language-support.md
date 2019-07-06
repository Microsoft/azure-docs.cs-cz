---
title: Podpora jazyků – hlasové služby
titleSuffix: Azure Cognitive Services
description: Hlasové služby Azure podporu mnoha jazyků pro převod řeči na text a převod textu na řeč, spolu s překladu řeči. Tento článek obsahuje úplný seznam jazykovou podporu služby.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 006b9401a3418e3b2b3803fa0b7897b28887d14a
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606609"
---
# <a name="language-and-region-support-for-the-speech-services"></a>Podpora jazyka a oblasti pro hlasové služby

Různé jazyky jsou podporovány pro různé funkce hlasové služby. Následující tabulky shrnují jazykovou podporu.

## <a name="speech-to-text"></a>Převod řeči na text

Rozpoznávání řeči Microsoft sady SDK a rozhraní REST API podporují následující jazyky (národní prostředí). Různé úrovně vlastního nastavení jsou k dispozici pro jednotlivé jazyky.

  Kód | Jazyk | [Akustické přizpůsobení](how-to-customize-acoustic-models.md) | [Přizpůsobení jazyka](how-to-customize-language-model.md) | [Výslovnost přizpůsobení](how-to-customize-pronunciation.md)
 ------|----------|---------------------|---------------------|-------------------------
 ar – například | Arabština (Egypt), moderní standard | Ne | Ano | Ne
 ES certifikační autority | Katalánština | Ne | Ne | Ne
 da-DK | Dánština (Dánsko) | Ne | Ne | Ne
 de-DE | Němčina (Německo) | Ano | Ano | Ne
 cs AU | angličtina (Austrálie) | Ne | Ano | Ano
 cs CA | angličtina (Kanada) | Ne | Ano | Ano
 en-GB | Angličtina (Spojené království) | Ne | Ano | Ano
 en-IN | English (India) | Ano | Ano | Ano
 cs NZ | angličtina (Nový Zéland) | Ne | Ano | Ano  
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
 zh-HK | Čínština (Kantonština, tradiční) | Ne | Ano | Ne
 zh-TW | Čínština (Tchaj-wanská Mandarínština) | Ne | Ano | Ne
 th TH | thajština (Thajsko) | Ne | Ne | Ne


## <a name="text-to-speech"></a>Převod textu na řeč

Převod textu na řeč rozhraní REST API podporuje tyto hlasů, z nichž každý podporuje konkrétní jazyku a dialektu, identifikovat podle národního prostředí.

> [!IMPORTANT]
> Ceny se liší pro standardní a vlastní neuronové hlasy. Navštivte prosím [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) stránky pro další informace.

### <a name="neural-voices"></a>Neurální hlasů

Převod textu na řeč neuronových sítí je nový typ syntézu řeči s využitím hloubkových neurálních sítí. Při použití neuronových sítí hlasový vstup, je téměř nerozeznatelná od lidí záznamy řečového.

Neurální hlasy slouží k interakce s chatovacích a virtuálních asistentů přirozenější a poutavější převést digitální texty např. e knihy audiobooks a vylepšit systémy navigaci. Přirozené prosody podobnou té lidské a jasné rozdělení slov Neurální hlasy výrazně snížit naslouchání reorganizovaly když uživatelé komunikují s AI systémy.

Úplný seznam neuronových sítí hlasy a dostupnosti v jednotlivých oblastech najdete v tématu [oblastech](regions.md#standard-and-neural-voices).

Národní prostředí | Jazyk | Pohlaví | Kompletní mapování názvů | Název krátký hlasu
--------|----------|--------|---------|------------
de-DE | Němčina (Německo) | Žena | "Microsoft serveru řeči Text na řeč hlas (de-DE, KatjaNeural)" | "de-DE-KatjaNeural"
en-US | English (US) | Muž | "Microsoft serveru řeči Text na řeč hlas (en US, GuyNeural)" | "en US-GuyNeural"
en-US | English (US) | Žena | "Microsoft serveru řeči Text na řeč hlas (en US, JessaNeural)" | "en US-JessaNeural"
IT-IT | Italština (Itálie) | Žena |"Microsoft serveru řeči Text na řeč hlas (it-IT, ElsaNeural)" | "it-IT ElsaNeural"
zh-CN | Čínština (Španělsko) | Žena | "Microsoft serveru řeči Text na řeč hlas (zh-CN, XiaoxiaoNeural)" | "zh-CN-XiaoxiaoNeural"

> [!NOTE]
> Kompletní mapování názvů nebo název krátký hlasové můžete použít v požadavcích syntézu řeči.

### <a name="standard-voices"></a>Standardní hlasů

Jsou k dispozici ve více než 45 jazyků a národních prostředí, které umožňují převod textu na řeč syntetizovaný více než 75 standardní hlasy. Další informace o dostupnosti v jednotlivých oblastech najdete v tématu [oblastech](regions.md#standard-and-neural-voices).

Národní prostředí | Jazyk | Pohlaví | Kompletní mapování názvů | Název krátký hlasu
-------|----------|---------|----------|----------
ar – například\* | arabština (Egypt) | Žena | "Microsoft Server řeči Text na řeč hlas (ar např Hoda)" | "ar – třeba-Hoda"
ar-SA | Arabština (Saúdská Arábie) | Muž | "Microsoft serveru řeči Text na řeč hlas (ar-SA, Naayf)" | "ar-SA-Naayf"
bg-BG | Bulharština | Muž | "Microsoft Server řeči převod textu na řeč hlasové (bg – BG, Ivan)" | "bg-BG-Ivan"
ES certifikační autority | katalánština (Španělsko) | Žena | "Microsoft Server řeči převod textu na řeč hlasové (ca-ES, HerenaRUS)" | "ca-ES-HerenaRUS"
cs-CZ | Čeština | Muž | "Microsoft Server řeči Text na řeč hlas (cs-CZ, Jakub)" | "cs-CZ-Jakub"
da-DK | dánština | Žena | "Microsoft serveru řeči Text na řeč hlas (da-DK HelleRUS)" | "da-DK-HelleRUS"
de-AT | němčina (Rakousko) | Muž | "Microsoft Server řeči Text na řeč hlas (de-AT, musí Michael)" | "de-AT-Michael"
de-CH | němčina (Švýcarsko) | Muž | "Microsoft serveru řeči Text na řeč hlas (de-CH, Karsten)" | "de-CH Karsten"
de-DE | Němčina (Německo) | Žena | "Microsoft serveru řeči Text na řeč hlas (de-DE, Hedda)" | "de-DE-Hedda"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (de-DE, HeddaRUS)" | "de-DE-HeddaRUS"
| | | Muž | "Microsoft serveru řeči Text na řeč hlas (de-DE, Stefan, Apollo)" | "de-DE-Stefan-Apollo"
el-GR | Řečtina | Muž | "Microsoft serveru řeči Text na řeč hlas (el-GR, Stefanos)" | "el GR Stefanos"
cs AU | angličtina (Austrálie) | Žena | "Microsoft serveru řeči Text na řeč hlas (en-AU, Catherine)" | "en-AU Catherine"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (en-AU, HayleyRUS)" | "en-AU HayleyRUS"
cs CA | angličtina (Kanada) | Žena | "Microsoft serveru řeči Text na řeč hlas (en-CA, Lenka)" | "en-CA-Lenka"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (en-CA, HeatherRUS)" | "en-CA-HeatherRUS"
en-GB | English (UK) | Žena | "Microsoft serveru řeči Text na řeč hlas (en-GB, Susan, Apollo)" | "en-GB – Susan-Apollo"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (en-GB, HazelRUS)" | "en-GB HazelRUS"
| | | Muž | "Microsoft serveru řeči Text na řeč hlas (en-GB, George, Apollo)" | "en-GB-George-Apollo"
cs IE | angličtina (Irsko) | Muž | "Microsoft serveru řeči Text na řeč hlas (en-IE, Sean)" | "en-IE Sean"
en-IN | English (India) | Žena | "Microsoft serveru řeči Text na řeč hlas (en-IN, Heera, Apollo)" | "en-IN-Heera-Apollo"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (en-IN, PriyaRUS)" | "en-IN-PriyaRUS"
| | | Muž | "Microsoft serveru řeči Text na řeč hlas (en-IN, Ravi, Apollo)" | "en-IN-Ravi-Apollo"
en-US | English (US) | Žena | "Microsoft serveru řeči Text na řeč hlas (en US, ZiraRUS)" | "en US-ZiraRUS"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (en US, JessaRUS)" | "en US-JessaRUS"
| | | Muž | "Microsoft serveru řeči Text na řeč hlas (en US, BenjaminRUS)" | "en US-BenjaminRUS"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (en US, Jessa24kRUS)" | "en US-Jessa24kRUS"
| | | Muž | "Microsoft serveru řeči Text na řeč hlas (en US, Guy24kRUS)" | "en US-Guy24kRUS"
es-ES | Španělština (Španělsko) |Žena | "Microsoft Server řeči převod textu na řeč hlasové (es-ES, Laura, Apollo)" | "es-ES-Laura-Apollo"
| | | Žena | "Microsoft Server řeči převod textu na řeč hlasové (es-ES, HelenaRUS)" | "es-ES-HelenaRUS"
| | | Muž | "Microsoft Server řeči převod textu na řeč hlasové (es-ES, tablet Pablo, Apollo)" | "es-ES-Pablo-Apollo"
es-MX | Španělština (Mexiko) | Žena | "Microsoft Server řeči převod textu na řeč hlasové (es-MX, HildaRUS)" | "es-MX-HildaRUS"
| | | Muž | "Microsoft Server řeči převod textu na řeč hlasové (es-MX, Raul, Apollo)" | "es-MX-Raul-Apollo"
fi-FI | Finština | Žena | "Microsoft serveru řeči Text na řeč hlas (fi-FI, HeidiRUS)" | "fi-FI-HeidiRUS"
fr-CA | Francouzština (Kanada) |Žena | "Microsoft Server řeči převod textu na řeč hlasové (fr-CA, Caroline)" | "fr-CA-Caroline"
| | | Žena | "Microsoft Server řeči převod textu na řeč hlasové (fr-CA, HarmonieRUS)" | "fr-CA-HarmonieRUS"
FR-CH | francouzština (Švýcarsko)| Muž | "Microsoft Server řeči převod textu na řeč hlasové (fr-CH, Guillaume)" | "fr-CH Guillaume"
fr-FR | Francouzština (Francie)| Žena | "Microsoft Server řeči převod textu na řeč hlasové (fr-FR, Julie, Apollo)" | "fr-FR-Julie-Apollo"
| | | Žena | "Microsoft Server řeči převod textu na řeč hlasové (fr-FR, HortenseRUS)" | "fr-FR-HortenseRUS"
| | | Muž | "Microsoft Server řeči převod textu na řeč hlasové (fr-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo"
he-IL| hebrejština (Izrael) | Muž| "Microsoft serveru řeči Text na řeč hlas (he-IL, Asaf)" | "he IL Asaf"
Dobrý den – v | hindština (Indie) | Žena | "Microsoft serveru řeči Text na řeč hlas (hi v, Kalpana, Apollo)" | "hi-IN-Kalpana-Apollo"
| | |Žena | "Microsoft serveru řeči Text na řeč hlas (hi v, Kalpana)" | "hi-IN-Kalpana"
| | | Muž | "Microsoft serveru řeči Text na řeč hlas (hi v, Hemant)" | "hi-IN-Hemant"
hr-HR | Chorvatština | Muž | "Microsoft Server řeči převod textu na řeč hlasové (hr-HR, Matej)" | "hr-HR-Matej"
hu-HU | Maďarština | Muž | "Microsoft serveru řeči Text na řeč hlas (hu-HU, Szabolcs)" | "hu-HU-Szabolcs"
ID ID | Indonéština| Muž | "Microsoft serveru řeči Text na řeč hlas (id-ID, Andika)" | "id-ID-Andika"
IT-IT | italština | Muž | "Microsoft serveru řeči Text hlas řeči (it-IT, Cosimo, Apollo)" | "it-IT – Cosimo-Apollo"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (it-IT, LuciaRUS)" | "it-IT LuciaRUS"
ja-JP | Japonština | Žena | "Microsoft Server řeči převod textu na řeč hlasové (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo"
| | | Muž | "Microsoft Server řeči převod textu na řeč hlasové (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo"
| | | Žena | "Microsoft Server řeči převod textu na řeč hlasové (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS"
ko-KR | Korejština | Žena | "Microsoft serveru řeči Text na řeč hlas (ko-KR, HeamiRUS)" | "ko-KR HeamiRUS"
Moje MS | Malajština | Muž | "Microsoft Server řeči převod textu na řeč hlasové (ms Moje, Rizwan)" | "ms-MY-Rizwan"
nb-NO | norština | Žena | "Microsoft Server řeči převod textu na řeč hlasové (nb-NO HuldaRUS)" | "nb-NO-HuldaRUS"
NL-NL | Holandština | Žena | "Microsoft Server řeči převod textu na řeč hlasové (nl-NL, HannaRUS)" | "nl-NL-HannaRUS"
pl-PL | polština | Žena | "Microsoft Server řeči převod textu na řeč hlasové (pl-PL, PaulinaRUS)" | "pl-PL-PaulinaRUS"
pt-BR | Portugalština (Brazílie) | Žena | "Microsoft Server řeči převod textu na řeč hlasové (pt-BR, HeloisaRUS)" | "pt-BR HeloisaRUS"
| | | Muž |"Microsoft Server řeči převod textu na řeč hlasové (pt-BR, ADAM, Apollo)" | "pt-BR – Daniel-Apollo"
pt-PT | Portugalština (Portugalsko) | Žena | "Microsoft Server řeči převod textu na řeč hlasové (pt-PT, HeliaRUS)" | "pt-PT-HeliaRUS"
ro RO | Rumunština | Muž | "Microsoft serveru řeči Text na řeč hlas (ro-RO, Andrei)" | "ro-RO-Andrei"
ru-RU |ruština| Žena | "Microsoft Server řeči převod textu na řeč hlasové (ru-RU, Irina, Apollo)" | "ru-RU-Irina-Apollo"
| | | Muž | "Microsoft Server řeči převod textu na řeč hlasové (ru-RU, Pavel, Apollo)" | "ru-RU-Pavel-Apollo"
| | | Žena | "Microsoft Server řeči převod textu na řeč hlasové (ru-RU, EkaterinaRUS)" | ru-RU EkaterinaRUS
sk-SK | Slovenština | Muž | "Microsoft Server řeči převod textu na řeč hlasové (sk-SK, Filip)" | "sk SK Filip"
sl SI | Slovinština | Muž | "Microsoft Server řeči převod textu na řeč hlasové (sl Incidentech Lado)" | "sl-SI-Lado"
sv-SE | švédština | Žena | "Microsoft serveru řeči Text na řeč hlas (sv-SE, HedvigRUS)" | "sv-SE HedvigRUS"
ta v | tamilština (Indie) | Muž | "Microsoft serveru řeči Text na řeč hlas, (ta v, Valluvar)" | "ta-IN-Valluvar"
te v | telugština (Indie) | Žena | "Microsoft serveru řeči Text na řeč hlas (te v, Chitra)" | "te-IN-Chitra"
th TH | Thajština | Muž | "Microsoft Server řeči převod textu na řeč hlasové (th TÝ, Pattara)" | "th TH Pattara"
tr-TR | turečtina | Žena | "Microsoft Server řeči převod textu na řeč hlasové (tr-TR, SedaRUS)" | "tr-TR-SedaRUS"
vi-VN | Vietnamština | Muž | "Microsoft Server řeči převod textu na řeč hlasové (vi VN)" | "vi-VN-s"
zh-CN | Čínština (Španělsko) | Žena | "Microsoft serveru řeči Text na řeč hlas (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (zh-CN, Yaoyao, Apollo)" | "zh-CN-Yaoyao-Apollo"
| | | Muž | "Microsoft serveru řeči Text na řeč hlas (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo"
zh-HK | čínština (Hongkong) | Žena | "Microsoft serveru řeči Text na řeč hlas (zh-HK, Tracy, Apollo)" | "zh-HK-Tracy-Apollo"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (zh-HK, TracyRUS)" | "zh-HK TracyRUS"
| | | Muž | "Microsoft serveru řeči Text na řeč hlas (zh-HK, Danny, Apollo)" | "zh-HK-Danny-Apollo"
zh-TW | Čínština (Tchaj-wan) | Žena | "Microsoft serveru řeči Text na řeč hlas (zh-TW, Yating, Apollo)" | "zh-TW-Yating-Apollo"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (zh-TW, HanHanRUS)" | "zh-TW HanHanRUS"
| | | Muž | "Microsoft serveru řeči Text na řeč hlas (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo"

\* *ar – třeba podporuje moderní standardní Arabština (MSA).*

> [!NOTE]
> Kompletní mapování názvů nebo název krátký hlasové můžete použít v požadavcích syntézu řeči.

### <a name="customization"></a>Přizpůsobení

Přizpůsobení hlasu je k dispozici pro de-DE, en-GB, en-IN, en US, es-MX, fr-FR, it-IT, pt-BR a zh-CN. Vyberte správný národní prostředí, která odpovídá trénovacích dat pro trénování modelu vlastní hlasové máte. Například pokud záznam dat, ke kterým máte je slyšet v angličtině s British zvýraznění, vyberte en-GB.  

> [!NOTE]
> Nepodporujeme cvičení modelu bi jazycích ve vlastní hlasové, s výjimkou bi čínština – angličtina – jazycích. Vyberte čínština angličtina dvojjazyčné, pokud chcete k trénování čínský hlas, které mohou mluvit také angličtina. Školení způsobu vyjadřování ve všech národních prostředích začíná u datové sady z 2 000 + projevy, s výjimkou en US a zh-CN, kde můžete začít s trénovací data libovolné velikosti.

## <a name="speech-translation"></a>Překlad řeči

**Překlad řeči** rozhraní API podporuje různé jazyky pro překlad řeči řeči a rozpoznávání řeči na text. Zdrojový jazyk musí být vždy z tabulky jazyka Speech to Text. Dostupné jazyky cílové závisí na tom, zda cílový překlad řeči nebo text. Může překládat příchozí řeči do více než [60 jazyků](https://www.microsoft.com/translator/business/languages/). Jsou k dispozici pro podmnožinu těchto jazyků [syntézu řeči](language-support.md#text-languages).

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
