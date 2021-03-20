---
title: Jazyková podpora – Překladatel
titleSuffix: Azure Cognitive Services
description: Cognitive Services Translator podporuje následující jazyky pro překlad textu na text pomocí NMT (neuronové Machine Translation).
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/10/2020
ms.author: lajanuar
ms.openlocfilehash: b114c4f3e6646f68e7a1068be8dd52778a26fb58
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738136"
---
# <a name="language-and-region-support-for-text-and-speech-translation"></a>Podpora jazyků a oblastí pro převod textu a řeči

Pomocí překladatele můžete překládat do a z kteréhokoli z jazyků a dialektů pro překlad textu 90. Neuronové Machine Translation (NMT) je nový standard pro vysoce kvalitní překlady počítačů s podporou AI a je k dispozici jako výchozí v případě, že je k dispozici systém neuronové.

Překladatele můžete také použít ve spojení s vlastním překladatelem k vytváření neuronové překladových systémů, které rozumí terminologii používané ve vašem podniku a průmyslu, a pomocí služby Microsoft Speech Service k přidání překladu řeči do aplikace.

[Další informace o tom, jak strojový překlad funguje](https://www.microsoft.com/translator/mt.aspx)

## <a name="text-translation"></a>překlad textu,
Překlad textu je k dispozici pomocí operace překladu do nebo z libovolného jazyka, který je k dispozici v překladateli. Rozhraní API také nabízí detekci jazyka pomocí operace zjišťování, použití operace přepřepisu a dvojjazyčné slovníky pomocí operací vyhledávání slovníku a slovníku. Dostupné jazyky pro každou z těchto operací jsou uvedeny níže. 

### <a name="translate"></a>Překlad

Překladatel podporuje pro převod textu na text následující jazyky. 

[Referenční dokumentace operace zobrazení překladu](reference/v3-0-translate.md)

| Jazyk | Kód jazyka |
|:-|:-:|
| Afrikánština | `af` |
| Albánština | `sq` |
| Amharšina | `am` |
| Arabština | `ar` |
| Arménština | `hy` |
| Ásámština | `as` |
| Ázerbájdžánština | `az` |
| Bengálština | `bn` |
| Bosenština (latinka) | `bs` |
| Bulharština | `bg` |
| Kantonština (tradiční) | `yue` |
| Katalánština | `ca` |
| Čínština (zjednodušená) | `zh-Hans` |
| Čínština (tradiční) | `zh-Hant` |
| Chorvatština | `hr` |
| Čeština | `cs` |
| Dáríština | `prs` |
| Dánština | `da` |
| Nizozemština | `nl` |
| Angličtina | `en` |
| Estonština | `et` |
| Fijian | `fj` |
| Filipino | `fil` |
| Finština | `fi` |
| Francouzština | `fr` |
| Francouzština (Kanada) | `fr-ca` |
| Němčina | `de` |
| Řečtina | `el` |
| Gudžarátština | `gu` |
| Haitská kreolština | `ht` |
| Hebrejština | `he` |
| Hindština | `hi` |
| Hmong Macek | `mww` |
| Maďarština | `hu` |
| Islandština | `is` |
| Indonéština | `id` |
| Inuktitutština | `iu` |
| Irština | `ga` |
| Italština | `it` |
| Japonština | `ja` |
| Kannadština | `kn` |
| Kazaština | `kk` |
| Khmerština | `km` |
| Klingon | `tlh-Latn` |
| Klingon (plqaD) | `tlh-Piqd` |
| Korejština | `ko` |
| Kurdština (střed) | `ku` |
| Kurdština (Severní) | `kmr` |
| Laoský | `lo` |
| Lotyština | `lv` |
| Litevština | `lt` |
| Malgašština | `mg` |
| Malajština | `ms` |
| Malajalámština | `ml` |
| Maltština | `mt` |
| Maorština | `mi` |
| Maráthština | `mr` |
| Myanmar | `my` |
| Nepálština | `ne` |
| Norština | `nb` |
| Udijština | `or` |
| Paštština | `ps` |
| Perština | `fa` |
| Polština | `pl` |
| Portugalština (Brazílie) | `pt` |
| portugalština (Portugalsko) | `pt-pt` |
| Paňdžábština | `pa` |
| Queretaro Otomi | `otq` |
| Rumunština | `ro` |
| Ruština | `ru` |
| Samoan | `sm` |
| Srbština (cyrilice) | `sr-Cyrl` |
| Srbština (latinka) | `sr-Latn` |
| Slovenština | `sk` |
| Slovinština | `sl` |
| Španělština | `es` |
| Svahilština | `sw` |
| Švédština | `sv` |
| Tahitian | `ty` |
| Tamilština | `ta` |
| Telugština | `te` |
| Thajština | `th` |
| Tigriňňa | `ti` |
| Tongánština | `to` |
| Turečtina | `tr` |
| Ukrajinština | `uk` |
| Urdština | `ur` |
| Vietnamština | `vi` |
| Velština | `cy` |
| Yucatec Maya | `yua` |

> [!NOTE]
> Kód jazyka `pt` bude výchozí `pt-br` , portugalština (Brazílie).

### <a name="detect"></a>Zjišťování

Překladatel detekuje následující jazyky pro překlad a převod.

[Referenční dokumentace k operaci zjišťování zobrazení](reference/v3-0-detect.md)

| Jazyk | Kód jazyka |
|:-|:-:|
| Afrikánština | `af` |
| Albánština | `sq` |
| Arabština | `ar` |
| Bulharština | `bg` |
| Katalánština | `ca` |
| Čínština (zjednodušená) | `zh-Hans` |
| Čínština (tradiční) | `zh-Hant` |
| Chorvatština | `hr` |
| Čeština | `cs` |
| Dánština | `da` |
| Nizozemština | `nl` |
| Angličtina | `en` |
| Estonština | `et` |
| Finština | `fi` |
| Francouzština | `fr` |
| Němčina | `de` |
| Řečtina | `el` |
| Gudžarátština | `gu` |
| Haitská kreolština | `ht` |
| Hebrejština | `he` |
| Hindština | `hi` |
| Maďarština | `hu` |
| Islandština | `is` |
| Indonéština | `id` |
| Inuktitutština | `iu` |
| Irština | `ga` |
| Italština | `it` |
| Japonština | `ja` |
| Klingon | `tlh-Latn` |
| Korejština | `ko` |
| Kurdština (střed) | `ku-Arab` |
| Lotyština | `lv` |
| Litevština | `lt` |
| Malajština | `ms` |
| Maltština | `mt` |
| Norština | `nb` |
| Paštština | `ps` |
| Perština | `fa` |
| Polština | `pl` |
| Portugalština | `pt` |
| Rumunština | `ro` |
| Ruština | `ru` |
| Srbština (cyrilice) | `sr-Cyrl` |
| Srbština (latinka) | `sr-Latn` |
| Slovenština | `sk` |
| Slovinština | `sl` |
| Španělština | `es` |
| Svahilština | `sw` |
| Švédština | `sv` |
| Tahitian | `ty` |
| Thajština | `th` |
| Turečtina | `tr` |
| Ukrajinština | `uk` |
| Urdština | `ur` |
| Vietnamština | `vi` |
| Velština | `cy` |
| Yucatec Maya | `yua` |

### <a name="transliterate"></a>Transliterace

Metoda přepisu podporuje následující jazyky. V "do/z", "<-->" označuje, že jazyk lze přepřepisovat z nebo na některý z uvedených skriptů. "-->" označuje, že jazyk lze přepřepisovat pouze z jednoho skriptu na druhý.

[Zobrazit referenční dokumentaci k operaci přepřepisu](reference/v3-0-translate.md)


| Jazyk    | Kód jazyka | Skript | Směr | Skript|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabština | `ar` | Arabština `Arab` | <--> | Znak `Latn` |
| Ásámština | `as` | Bengálština `Beng` | <--> | Znak `Latn` |
| Bengálština  | `bn` | Bengálština `Beng` | <--> | Znak `Latn` |
|Běloruština| `be` | B `Cyrl`  | <--> | Znak `Latn` |
|Bulharština| `bg` | B `Cyrl`  | <--> | Znak `Latn` |
| Čínština (zjednodušená) | `zh-Hans` | Zjednodušená čínština `Hans`| <--> | Znak `Latn` |
| Čínština (zjednodušená) | `zh-Hans` | Zjednodušená čínština `Hans`| <--> | Tradiční čínština `Hant`|
| Čínština (tradiční) | `zh-Hant` | Tradiční čínština `Hant`| <--> | Znak `Latn` |
| Čínština (tradiční) | `zh-Hant` | Tradiční čínština `Hant`| <--> | Zjednodušená čínština `Hans` |
|Řečtina| `el` | Řečtina `Grek`  | <--> | Znak `Latn` |
| Gudžarátština | `gu`  | Gudžarátština `Gujr` | <--> | Znak `Latn` |
| Hebrejština | `he` | Hebrejština `Hebr` | <--> | Znak `Latn` |
| Hindština | `hi` | Koncový `Deva` | <--> | Znak `Latn` |
| Japonština | `ja` | Japonština `Jpan` | <--> | Znak `Latn` |
| Kannadština | `kn` | Kannadština `Knda` | <--> | Znak `Latn` |
|Kazaština| `kk` | B `Cyrl`  | <--> | Znak `Latn` |
|Korejština| `ko` | Korejština `Kore`  | <--> | Znak `Latn` |
|Kyrgyzština| `ky` | B `Cyrl`  | <--> | Znak `Latn` |
|Makedonie| `mk` | B `Cyrl`  | <--> | Znak `Latn` |
| Malajalámština | `ml` | Malajalámština `Mlym` | <--> | Znak `Latn` |
| Maráthština | `mr` | Koncový `Deva` | <--> | Znak `Latn` |
|Mongolština| `mn` | B `Cyrl`  | <--> | Znak `Latn` |
| Udijština | `or` | Krí `Orya` | <--> | Znak `Latn` |
|Perština| `fa` | Arabština `Arab`  | <--> | Znak `Latn` |
| Paňdžábština | `pa` | Západní `Guru`  | <--> | Znak `Latn`  |
|Ruština| `ru` | B `Cyrl`  | <--> | Znak `Latn` |
| Srbština (cyrilice) | `sr-Cyrl` | B `Cyrl`  | --> | Znak `Latn` |
| Srbština (latinka) | `sr-Latn` | Znak `Latn` | --> | B `Cyrl`|
|Sindhština| `sd` | Arabština `Arab`  | <--> | Znak `Latn` |
|Sinhálské| `si` | Sinhálské `Sinh`  | <--> | Znak `Latn` |
|Tádžičtina| `tg` | B `Cyrl`  | <--> | Znak `Latn` |
| Tamilština | `ta` | Tamilština `Taml` | <--> | Znak `Latn` |
|Tatarština| `tt` | B `Cyrl`  | <--> | Znak `Latn` |
| Telugština | `te` | Telugština `Telu` | <--> | Znak `Latn` |
| Thajština | `th` | Thajština `Thai` | --> | Znak `Latn` |
|Ukrajinština| `uk` | B `Cyrl`  | <--> | Znak `Latn` |
|Urdština| `ur` | Arabština `Arab`  | <--> | Znak `Latn` |

### <a name="dictionary"></a>Slovník

Slovník podporuje následující jazyky pro nebo z angličtiny pomocí vyhledávacích a ukázkových metod.

Podívejte se na referenční dokumentaci pro [hledání slovníku](reference/v3-0-dictionary-lookup.md) a [Příklady operací slovníku](reference/v3-0-dictionary-examples.md) .

| Jazyk    | Kód jazyka |
|:----------- |:-------------:|
| Afrikánština      | `af`          |
| Arabština       | `ar`          |
| Bengálština      | `bn`          |
| Bosenština (latinka)      | `bs`          |
| Bulharština      | `bg`          |
| Katalánština      | `ca`          |
| Čínština (zjednodušená)      | `zh-Hans`          |
| Chorvatština      | `hr`          |
| Čeština      | `cs`          |
| Dánština      | `da`          |
| Nizozemština      | `nl`          |
| Estonština      | `et`          |
| Finština      | `fi`          |
| Francouzština      | `fr`          |
| Němčina      | `de`          |
| Řečtina      | `el`          |
| Haitská kreolština      | `ht`          |
| Hebrejština      | `he`          |
| Hindština      | `hi`          |
| Hmong Macek      | `mww`          |
| Maďarština      | `hu`          |
| Islandština    | `is`  |
| Indonéština      | `id`          |
| Italština      | `it`          |
| Japonština      | `ja`          |
| Klingon      | `tlh`          |
| Korejština      | `ko`          |
| Lotyština      | `lv`          |
| Litevština      | `lt`          |
| Malajština      | `ms`          |
| Maltština      | `mt`          |
| Norština      | `nb`          |
| Perština      | `fa`          |
| Polština      | `pl`          |
| Portugalština (Brazílie)     | `pt`          |
| Rumunština      | `ro`          |
| Ruština      | `ru`          |
| Srbština (latinka)      | `sr-Latn`          |
| Slovenština     | `sk`          |
| Slovinština      | `sl`          |
| Španělština      | `es`          |
| Svahilština      | `sw`          |
| Švédština      | `sv`          |
| Tamilština      | `ta`          |
| Thajština      | `th`          |
| Turečtina      | `tr`          |
| Ukrajinština      | `uk`          |
| Urdština      | `ur`          |
| Vietnamština      | `vi`          |
| Velština      | `cy`          |

### <a name="access-the-translator-language-list-programmatically"></a>Přístup k seznamu jazyků překladatele prostřednictvím kódu programu

Seznam podporovaných jazyků pro překladatele můžete načíst pomocí metody languages. Můžete zobrazit seznam podle funkcí, kód jazyka a také název jazyka v angličtině nebo v jakémkoli jiném podporovaném jazyce. Služba Microsoft Translator automaticky aktualizuje tento seznam, protože jsou k dispozici nové jazyky.

[Referenční dokumentace operací zobrazení jazyků](reference/v3-0-languages.md)

## <a name="customization"></a>Přizpůsobení

Následující jazyky jsou k dispozici pro přizpůsobení nebo z angličtiny pomocí [vlastního překladatele](https://aka.ms/CustomTranslator).

| Jazyk    | Kód jazyka |
|:----------- |:-------------:|
|Afrikánština| `af`|
| Arabština       | `ar`          |
| Bengálština      | `bn`          |
| Bosenština (latinka)      | `bs`          |
| Bulharština      | `bg`          |
|Katalánština|   `ca`    |
| Čínština (zjednodušená)      | `zh-Hans`          |
|Čínština (tradiční)|   `zh-Hant`   |
| Chorvatština      | `hr`          |
| Čeština      | `cs`          |
| Dánština      | `da`          |
| Nizozemština      | `nl`          |
| Angličtina    | `en`     |
| Estonština      | `et`          |
|Fijian|    `fj`    |
|Filipino|  `fil`   |
| Finština      | `fi`          |
| Francouzština      | `fr`          |
| Němčina      | `de`          |
| Řečtina      | `el`          |
| Gudžarátština| `gu`    |
| Hebrejština      | `he`          |
| Hindština      | `hi`          |
| Maďarština      | `hu`          |
| Islandština | `is` |
| Indonéština|   `id`    |
| Irština | `ga`  |
| Italština      | `it`          |
| Japonština      | `ja`          |
|Kannadština|`kn`|
| Korejština      | `ko`          |
| Lotyština      | `lv`          |
| Litevština      | `lt`          |
| Malgašština| `mg`    |
| Malajština|    `ms` |
|Maltština|   `mt`    |
| Maorština| `mi`  |
| Maráthština| `mr`  |
| Norština      | `nb`          |
| Perština      | `fa`          |
| Polština      | `pl`          |
| Portugalština (Brazílie) | `pt` |
| Paňdžábština|`pa`|
| Rumunština      | `ro`          |
| Ruština      | `ru`          |
| Samoan|   `sm`    |
| Srbština (latinka)      | `sr-Latn`          |
| Slovenština     | `sk`          |
| Slovinština      | `sl`          |
| Španělština      | `es`          |
| Svahilština|  `sw`    |
| Švédština      | `sv`          |
|Tahitian|  `ty`    |
| Thajština      | `th`          |
|Tongánština|    `to`    |
| Turečtina      | `tr`          |
| Ukrajinština      | `uk`          |
| Urdština| `ur`    |
| Vietnamština      | `vi`          |
| Velština | `cy` |

## <a name="speech-translation"></a>Překlad řeči
Překlad řeči je k dispozici pomocí překladatele se službou Cognitive Services Speech. V [dokumentaci ke službě Speech](../speech-service/index.yml) najdete další informace o použití překladu řeči a k zobrazení všech [dostupných jazykových možností](../speech-service/language-support.md).

### <a name="speech-to-text"></a>Převod řeči na text
Převod řeči na text, aby se přeložil do jazyka textu dle vašeho výběru. Převod řeči na text se používá pro převod řeči na text nebo pro překlad řeči na řeč při použití ve spojení s syntézou řeči.

| Jazyk    |
|:----------- |
|Arabština|
|Kantonština (tradiční)|
|Katalánština|
|Čínština (zjednodušená)|
|Čínština (tradiční)|
|Dánština|
|Nizozemština|
|Angličtina|
|Finština|
|Francouzština|
|Francouzština (Kanada)|
|Němčina|
|Gudžarátština|
|Hindština|
|Italština|
|Japonština|
|Korejština|
|Maráthština|
|Norština|
|Polština|
|Portugalština (Brazílie)|
|portugalština (Portugalsko)|
|Ruština|
|Španělština|
|Švédština|
|Tamilština|
|Telugština|
|Thajština|
|Turečtina|

### <a name="text-to-speech"></a>Převod textu na řeč
Převod textu na řeč Převod textu na řeč se používá k přidání zvukového výstupu výsledků překladu nebo převodu řeči na řeč při použití s převodem na text. 

| Jazyk |
|:-|
| Arabština |
| Bulharština |
| Kantonština (tradiční) |
| Katalánština |
| Čínština (zjednodušená) |
| Čínština (tradiční) |
| Chorvatština |
| Čeština |
| Dánština |
| Nizozemština |
| Angličtina |
| Finština |
| Francouzština |
| Francouzština (Kanada) |
| Němčina |
| Řečtina |
| Hebrejština |
| Hindština |
| Maďarština |
| Indonéština |
| Italština |
| Japonština |
| Korejština |
| Malajština |
| Norština |
| Polština |
| Portugalština (Brazílie) |
| portugalština (Portugalsko) |
| Rumunština |
| Ruština |
| Slovenština |
| Slovinština |
| Španělština |
| Švédština |
| Tamilština |
| Telugština |
| Thajština |
| Turečtina |
| Vietnamština |

## <a name="view-the-language-list-on-the-microsoft-translator-website"></a>Zobrazit seznam jazyků na webu Microsoft Translator

Pokud chcete rychle zobrazit jazyky, na webu Microsoft Translator se zobrazí všechny jazyky, které Překladatel podporuje pro překlad textu a službu Speech pro překlad řeči. Tento seznam neobsahuje informace specifické pro vývojáře, jako jsou kódy jazyků.

[Zobrazit seznam jazyků](https://www.microsoft.com/translator/languages.aspx)
