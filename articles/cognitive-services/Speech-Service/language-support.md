---
title: Podpora jazyků – rozhraní API pro rozpoznávání řeči služeb
description: Seznam podporované službou Speech přirozeného jazyka.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: v-jerkin
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 09/25/2018
ms.author: v-jerkin
ms.openlocfilehash: 9a867289cffa17030e397ef170e9055451057410
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237734"
---
# <a name="language-and-region-support-for-speech-service-api"></a>Podpora jazyka a oblasti pro rozhraní API pro rozpoznávání řeči služeb

Různé jazyky jsou podporovány pro různé funkce služby řeči. Následující tabulky shrnují jazykovou podporu.

## <a name="speech-to-text"></a>Převod řeči na text

Rozpoznávání řeči Microsoft rozhraní API podporuje následující jazyky. Různé úrovně vlastního nastavení jsou k dispozici pro jednotlivé jazyky.

  Kód | Jazyk | [Akustické přizpůsobení](how-to-customize-acoustic-models.md) | [Přizpůsobení jazyka](how-to-customize-language-model.md) | [Výslovnost přizpůsobení](how-to-customize-pronunciation.md)
 ------|----------|---------------------|---------------------|-------------------------
 ar – například | Arabština (Egypt), moderní standard | Ne | Ano | Ne
 ES certifikační autority | katalánština (Španělsko) | Ne | Ne | Ne
 da-DK | Dánština (Dánsko) | Ne | Ne | Ne
 de-DE | Němčina (Německo) | Ano | Ano | Ne
 cs AU | angličtina (Austrálie) | Ne | Ano | Ano
 cs CA | angličtina (Kanada) | Ne | Ano | Ano
 en-GB | Angličtina (Spojené království) | Ne | Ano | Ano
 en-IN | English (India) | Ano | Ano | Ano
 cs NZ | angličtina (Nový Zéland) | Ne | Ano | Ano  
 cs-CZ | Angličtina (Spojené státy) | Ano | Ano | Ano
 es-ES | Španělština (Španělsko) | Ne | Ano | Ne
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
 pt-BR | Portugalština (Brazílie) | Ne | Ano | Ne
 pt-PT | Portugalština (Portugalsko) | Ne | Ano | Ne
 ru-RU | Ruština (Rusko) | Ano | Ano | Ne
 sv-SE | Švédština (Švédsko) | Ne | Ne | Ne
 zh-CN | Čínština (Mandarínština, zjednodušená čínština) | Ano | Ano | Ne
 zh-HK | Čínština (Mandarínština, tradiční) | Ne | Ano | Ne
 zh-TW | Čínština (Tchaj-wanská Mandarínština) | Ne | Ano | Ne
 th TH | thajština (Thajsko) | Ne | Ne | Ne


## <a name="text-to-speech"></a>Převod textu na řeč

Rozhraní API syntézu řeči nabízí následující hlasů, z nichž každý podporuje konkrétní jazyku a dialektu, identifikovat podle národního prostředí.

Národní prostředí | Jazyk | Pohlaví | Název mapování služby
-------|----------|---------|--------------------
ar – například\* | arabština (Egypt) | Žena | "Microsoft Server řeči Text na řeč hlas (ar např Hoda)"
ar-SA | Arabština (Saúdská Arábie) | Muž | "Microsoft serveru řeči Text na řeč hlas (ar-SA, Naayf)"
bg-BG | Bulharština | Muž | "Microsoft Server řeči převod textu na řeč hlasové (bg – BG, Ivan)"
ES certifikační autority | katalánština (Španělsko) | Žena | "Microsoft Server řeči převod textu na řeč hlasové (ca-ES, HerenaRUS)"
cs-CZ | Čeština | Muž | "Microsoft Server řeči Text na řeč hlas (cs-CZ, Jakub)"
cs-CZ | Čeština | Muž | "Microsoft Server řeči Text na řeč hlas (cs-CZ, Vit)"
da-DK | dánština | Žena | "Microsoft serveru řeči Text na řeč hlas (da-DK HelleRUS)"
de-AT | němčina (Rakousko) | Muž | "Microsoft Server řeči Text na řeč hlas (de-AT, musí Michael)"
de-CH | němčina (Švýcarsko) | Muž | "Microsoft serveru řeči Text na řeč hlas (de-CH, Karsten)"
de-DE | Němčina (Německo) | Žena | "Microsoft serveru řeči Text na řeč hlas (de-DE, Hedda)"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (de-DE, HeddaRUS)"
| | | Muž | "Microsoft serveru řeči Text na řeč hlas (de-DE, Stefan, Apollo)"
el-GR | Řečtina | Muž | "Microsoft serveru řeči Text na řeč hlas (el-GR, Stefanos)"
cs AU | angličtina (Austrálie) | Žena | "Microsoft serveru řeči Text na řeč hlas (en-AU, Catherine)"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (en-AU, HayleyRUS)"
cs CA | angličtina (Kanada) | Žena | "Microsoft serveru řeči Text na řeč hlas (en-CA, Lenka)"
| | | Žena | "Microsoft serveru řeči Text na řeč hlas (en-CA, HeatherRUS)"
en-GB | English (UK) | Žena | "Microsoft serveru řeči Text na řeč hlas (en-GB, Susan, Apollo)"
| | |Žena | "Microsoft serveru řeči Text na řeč hlas (en-GB, HazelRUS)"
| | |Muž | "Microsoft serveru řeči Text na řeč hlas (en-GB, George, Apollo)"
cs IE | angličtina (Irsko) |Muž | "Microsoft serveru řeči Text na řeč hlas (en-IE, Sean)"
cs IE | angličtina (Irsko) |Muž | "Microsoft serveru řeči Text na řeč hlas (en-IE, Shaun)"
en-IN | English (India) | Žena | "Microsoft serveru řeči Text na řeč hlas (en-IN, Heera, Apollo)"
| | |Žena | "Microsoft serveru řeči Text na řeč hlas (en-IN, PriyaRUS)"
| | |Muž | "Microsoft serveru řeči Text na řeč hlas (en-IN, Ravi, Apollo)"
cs-CZ | English (US) |Žena | "Microsoft serveru řeči Text na řeč hlas (en US, ZiraRUS)"
| | |Žena | "Microsoft serveru řeči Text na řeč hlas (en US, JessaRUS)"
| | |Muž | "Microsoft serveru řeči Text na řeč hlas (en US, BenjaminRUS)"
| | |Žena | "Microsoft serveru řeči Text na řeč hlas (en US, Jessa24kRUS)"
| | |Muž | "Microsoft serveru řeči Text na řeč hlas (en US, Guy24kRUS)"
es-ES | Španělština (Španělsko) |Žena | "Microsoft Server řeči převod textu na řeč hlasové (es-ES, Laura, Apollo)"
| | |Žena | "Microsoft Server řeči převod textu na řeč hlasové (es-ES, HelenaRUS)"
| | |Muž | "Microsoft Server řeči převod textu na řeč hlasové (es-ES, tablet Pablo, Apollo)"
es-MX | Španělština (Mexiko) | Žena | "Microsoft Server řeči převod textu na řeč hlasové (es-MX, HildaRUS)"
| | | Muž | "Microsoft Server řeči převod textu na řeč hlasové (es-MX, Raul, Apollo)"
fi-FI | Finština | Žena | "Microsoft serveru řeči Text na řeč hlas (fi-FI, HeidiRUS)"
fr-CA | Francouzština (Kanada) |Žena | "Microsoft Server řeči převod textu na řeč hlasové (fr-CA, Caroline)"
| | | Žena | "Microsoft Server řeči převod textu na řeč hlasové (fr-CA, HarmonieRUS)"
FR-CH | francouzština (Švýcarsko)|Muž | "Microsoft Server řeči převod textu na řeč hlasové (fr-CH, Guillaume)"
fr-FR | Francouzština (Francie)|Žena | "Microsoft Server řeči převod textu na řeč hlasové (fr-FR, Julie, Apollo)"
| | |Žena | "Microsoft Server řeči převod textu na řeč hlasové (fr-FR, HortenseRUS)"
| | |Muž | "Microsoft Server řeči převod textu na řeč hlasové (fr-FR, Paul, Apollo)"
he-IL| hebrejština (Izrael) | Muž| "Microsoft serveru řeči Text na řeč hlas (he-IL, Asaf)"
Dobrý den – v | hindština (Indie) | Žena | "Microsoft serveru řeči Text na řeč hlas (hi v, Kalpana, Apollo)"
| | |Žena | "Microsoft serveru řeči Text na řeč hlas (hi v, Kalpana)"
| | | Muž | "Microsoft serveru řeči Text na řeč hlas (hi v, Hemant)"
hr-HR | Chorvatština | Muž | "Microsoft Server řeči převod textu na řeč hlasové (hr-HR, Matej)"
hu-HU | Maďarština | Muž | "Microsoft serveru řeči Text na řeč hlas (hu-HU, Szabolcs)"
ID ID | Indonéština| Muž | "Microsoft serveru řeči Text na řeč hlas (id-ID, Andika)"
IT-IT | italština |Muž | "Microsoft serveru řeči Text hlas řeči (it-IT, Cosimo, Apollo)"
| | |Žena | "Microsoft serveru řeči Text na řeč hlas (it-IT, LuciaRUS)"
ja-JP | Japonština |Žena | "Microsoft Server řeči převod textu na řeč hlasové (ja-JP, Ayumi, Apollo)"
| | |Muž | "Microsoft Server řeči převod textu na řeč hlasové (ja-JP, Ichiro, Apollo)"
| | |Žena | "Microsoft Server řeči převod textu na řeč hlasové (ja-JP, HarukaRUS)"
ko-KR | Korejština |Žena | "Microsoft serveru řeči Text na řeč hlas (ko-KR, HeamiRUS)"
Moje MS | Malajština | Muž | "Microsoft Server řeči převod textu na řeč hlasové (ms Moje, Rizwan)"
nb-NO | norština | Žena | "Microsoft Server řeči převod textu na řeč hlasové (nb-NO HuldaRUS)"
NL-NL | Holandština | Žena | "Microsoft Server řeči převod textu na řeč hlasové (nl-NL, HannaRUS)"
pl-PL | polština | Žena | "Microsoft Server řeči převod textu na řeč hlasové (pl-PL, PaulinaRUS)"
pt-BR | Portugalština (Brazílie) | Žena | "Microsoft Server řeči převod textu na řeč hlasové (pt-BR, HeloisaRUS)"
| | |Muž | "Microsoft Server řeči převod textu na řeč hlasové (pt-BR, ADAM, Apollo)"
pt-PT | Portugalština (Portugalsko) | Žena | "Microsoft Server řeči převod textu na řeč hlasové (pt-PT, HeliaRUS)"
ro RO | Rumunština | Muž | "Microsoft serveru řeči Text na řeč hlas (ro-RO, Andrei)"
ru-RU |ruština| Žena | "Microsoft Server řeči převod textu na řeč hlasové (ru-RU, Irina, Apollo)"
| | |Muž | "Microsoft Server řeči převod textu na řeč hlasové (ru-RU, Pavel, Apollo)"
| | |Žena | "Microsoft Server řeči převod textu na řeč hlasové (ru-RU, EkaterinaRUS)"
sk-SK | Slovenština|Muž | "Microsoft Server řeči převod textu na řeč hlasové (sk-SK, Filip)"
sl SI | Slovinština|Muž | "Microsoft Server řeči převod textu na řeč hlasové (sl Incidentech Lado)"
sv-SE | švédština|Žena | "Microsoft serveru řeči Text na řeč hlas (sv-SE, HedvigRUS)"
ta v | tamilština (Indie) |Muž | "Microsoft serveru řeči Text na řeč hlas, (ta v, Valluvar)"
te v | telugština (Indie) |Žena | "Microsoft serveru řeči Text na řeč hlas (te v, Chitra)"
th TH | Thajština|Muž | "Microsoft Server řeči převod textu na řeč hlasové (th TÝ, Pattara)"
tr-TR |turečtina| Žena | "Microsoft Server řeči převod textu na řeč hlasové (tr-TR, SedaRUS)"
vi-VN | Vietnamština|Muž | "Microsoft Server řeči převod textu na řeč hlasové (vi VN)"
zh-CN | Čínština (Španělsko)|Žena | "Microsoft serveru řeči Text na řeč hlas (zh-CN, HuihuiRUS)"
| | |Žena | "Microsoft serveru řeči Text na řeč hlas (zh-CN, Yaoyao, Apollo)"
| | |Muž | "Microsoft serveru řeči Text na řeč hlas (zh-CN, Kangkang, Apollo)"
zh-HK | čínština (Hongkong)|Žena | "Microsoft serveru řeči Text na řeč hlas (zh-HK, Tracy, Apollo)"
| | |Žena | "Microsoft serveru řeči Text na řeč hlas (zh-HK, TracyRUS)"
| || Muž | "Microsoft serveru řeči Text na řeč hlas (zh-HK, Danny, Apollo)"
zh-TW | Čínština (Tchaj-wan)|Žena | "Microsoft serveru řeči Text na řeč hlas (zh-TW, Yating, Apollo)"
| || Žena | "Microsoft serveru řeči Text na řeč hlas (zh-TW, HanHanRUS)"
| || Muž | "Microsoft serveru řeči Text na řeč hlas (zh-TW, Zhiwei, Apollo)"

\* *ar – třeba podporuje moderní standardní Arabština (MSA).*

### <a name="customization"></a>Přizpůsobení

Přizpůsobení hlasu je k dispozici pro Americkou angličtinu (en US), Španělsko čínština (zh-CN) a italština (it-IT).

> [!NOTE]
> Školení italsky začíná u datové sady z 2 000 + projevy. Dvojjazyčné modely čínština – angličtina jsou také podporovány s počáteční sadu 2 000 + projevy data.

## <a name="speech-translation"></a>Překlad řeči

**Překlad řeči** rozhraní API podporuje různé jazyky pro překlad řeči řeči a rozpoznávání řeči na text. Zdrojový jazyk musí být vždy z následující tabulky jazyka řeči. Dostupné jazyky cílové závisí na tom, zda cílový překlad řeči nebo text.

### <a name="speech-languages"></a>Jazyků pro řeč

| Rozpoznávání řeči, jazyka   | Kód jazyka |
|:----------- |-|
| Arabština (moderní Standard)      | `ar` |
| Čínština (Mandarínština)      | `zh` |
| Angličtina      | `en` |
| Francouzština      | `fr` |
| Němčina      | `de` |
| italština      | `it` |
| Japonština      | `jp` |
| Portugalština (Brazílie)     | `pt` |
| ruština      | `ru` |
| Španělština      |  `es` |

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

* [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Zjistěte, jak rozpoznávat řeč v jazyce C#](quickstart-csharp-dotnet-windows.md)
