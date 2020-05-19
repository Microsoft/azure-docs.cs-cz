---
title: Jazyková podpora – Překladatel
titleSuffix: Azure Cognitive Services
description: Cognitive Services Translator podporuje následující jazyky pro překlad textu na text pomocí NMT (neuronové Machine Translation).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/10/2020
ms.author: swmachan
ms.openlocfilehash: 15c11a5b94b80a96c520696c45f3eec5c48b7b48
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592725"
---
# <a name="language-and-region-support-for-translator"></a>Podpora jazyků a oblastí pro překladatele

Překladatel podporuje následující jazyky pro převod textu na text. Neuronové Machine Translation (NMT) je nový standard pro vysoce kvalitní překlady počítačů s podporou AI a je k dispozici jako výchozí v případě, že je k dispozici systém neuronové.

[Další informace o tom, jak strojový překlad funguje](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Překlad

**Překladač v2**

> [!NOTE]
> Verze V2 byla zastaralá od 30. dubna 2018. Pokud chcete využívat nové funkce, které jsou k dispozici výhradně v v3, migrujte prosím své aplikace na v3.

* Pouze statistická: pro tento jazyk není k dispozici žádný neuronové systém.
* Neuronové k dispozici: systém neuronové je k dispozici. `category=generalnn`Pro přístup k systému neuronové použijte parametr.
* Neuronové výchozí: výchozí překladový systém je neuronové. Použijte parametr `category=smt` pro přístup ke statistickému systému pro použití se službou Microsoft Translator hub.
* Pouze neuronové: je k dispozici pouze překlad neuronové.

**Překladatel V3** Překladatel v3 je ve výchozím nastavení neuronové a statistické systémy jsou k dispozici pouze v případě, že neexistuje žádný systém neuronové.

> [!NOTE]
> V současné době je k dispozici podmnožina jazyků neuronové ve vlastním překladateli a postupně přidáváme další. [Zobrazit jazyky, které jsou aktuálně k dispozici ve vlastním překladateli](#customization).

|Jazyk|  Kód jazyka|  ROZHRANÍ V3 API|
|:-----|:-----:|:-----|
|Afrikánština| `af`|   Neuronové|
|Arabština|    `ar`    |   Neuronové|
|Bengálština|    `bn`    |   Neuronové|
|Bosenština (latinka)|   `bs`    |   Neuronové|
|Bulharština| `bg`    |   Neuronové|
|Kantonština (tradiční)|   `yue`|  Ložený|
|Katalánština|   `ca`    |   Ložený|
|Čínština (zjednodušená)|    `zh-Hans`|Neuronové|
|Čínština (tradiční)|   `zh-Hant`       |Neuronové|
|Chorvatština|  `hr`    |Neuronové|
|Čeština| `cs`    |   Neuronové|
|Dánština|    `da`        |Neuronové|
|Nizozemština| `nl`|   Neuronové|
|Angličtina|   `en`    |   Neuronové|
|Estonština|  `et`    |   Neuronové|
|Fijian|    `fj`    |   Ložený|
|Filipino|  `fil`   |   Ložený|
|Finština|   `fi`    |   Neuronové|
|Francouzština|    `fr`    |   Neuronové|
|Němčina|    `de`    |   Neuronové|
|Řečtina| `el`    |   Neuronové|
|Gudžarátština|  `gu`    |   Neuronové|
|Haitská kreolština|    `ht`        |Ložený|
|Hebrejština |`he`   |Neuronové
|Hindština| `hi`    |   Neuronové|
|Hmong Macek| `mww`   |   Ložený|
|Maďarština| `hu`    |   Neuronové|
|Islandština| `is`    |   Neuronové|
|Indonéština|    `id`    |   Ložený|
|Irština | `ga`| Neuronové
|Italština|   `it`    |   Neuronové|
|Japonština|  `ja`    |   Neuronové|
|Kannadština|`kn`| Neuronové
|Svahilština| `sw`    |   Ložený|
|Klingon|   `tlh`   |   Ložený|
|Klingon (plqaD)|   `tlh-Qaak`  |   Ložený|
|Korejština |`ko`   |   Neuronové|
|Lotyština|   `lv`    |   Neuronové|
|Litevština|    `lt`    |   Neuronové|
|Malgašština|  `mg`    |   Ložený|
|Malajština| `ms`        |Ložený|
|Malajalámština| `ml` | Neuronové
|Maltština|   `mt`    |   Ložený|
|Maorština| `mi`  | Neuronové|
|Maráthština| `mr`  | Neuronové|
|Norština| `nb`    |   Neuronové|
|Perština|   `fa`    |   Neuronové|
|Polština|    `pl`    |   Neuronové|
|Portugalština (Brazílie)|   `pt-br` |   Neuronové|
|portugalština (Portugalsko)| `pt-pt` | Neuronové
|Paňdžábština|`pa`|Neuronové
|Queretaro Otomi|   `otq`   |   Ložený|
|Rumunština|  `ro`    |   Neuronové|
|Ruština|   `ru`    |   Neuronové|
|Samoan|    `sm`    |   Ložený|
|Srbština (cyrilice)|    `sr-Cyrl`|  Ložený|
|Srbština (latinka)|   `sr-Latn`       |Ložený|
|Slovenština|    `sk`    |   Neuronové|
|Slovinština| `sl`    |   Neuronové|
|Španělština|   `es`    |   Neuronové|
|Švédština|   `sv`    |Neuronové|
|Tahitian|  `ty`    |Ložený|
|Tamilština| `ta`    |   Neuronové|
|Telugština|    `te`    |   Neuronové|
|Thajština|  `th`    |   Neuronové|
|Tongánština|    `to`    |   Ložený|
|Turečtina|   `tr`        |Neuronové|
|Ukrajinština| `uk`    |   Neuronové|
|Urdština|  `ur`    |   Ložený|
|Vietnamština|    `vi`    |   Neuronové|
|Velština| `cy`    |   Neuronové|
|Yucatec Maya|  `yua`   |   Ložený|

> [!NOTE]
> Kód jazyka `pt` bude výchozí `pt-br` , portugalština (Brazílie).

## <a name="transliteration"></a>Transkripci

Metoda přepisu podporuje následující jazyky. V "do/z", "<-->" označuje, že jazyk lze přepřepisovat z nebo na některý z uvedených skriptů. "-->" označuje, že jazyk lze přepřepisovat pouze z jednoho skriptu na druhý.

| Jazyk    | Kód jazyka | Skript | Směr | Skript|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabština | `ar` | Arabština`Arab` | <--> | Znak`Latn` |
|Bengálština  | `bn` | Bengálština`Beng` | <--> | Znak`Latn` |
| Čínština (zjednodušená) | `zh-Hans` | Zjednodušená čínština`Hans`| <--> | Znak`Latn` |
| Čínština (zjednodušená) | `zh-Hans` | Zjednodušená čínština`Hans`| <--> | Tradiční čínština`Hant`|
| Čínština (tradiční) | `zh-Hant` | Tradiční čínština`Hant`| <--> | Znak`Latn` |
| Čínština (tradiční) | `zh-Hant` | Tradiční čínština`Hant`| <--> | Zjednodušená čínština`Hans` |
| Gudžarátština | `gu`  | Gudžarátština`Gujr` | --> | Znak`Latn` |
| Hebrejština | `he` | Hebrejština`Hebr` | <--> | Znak`Latn` |
| Hindština | `hi` | Koncový`Deva` | <--> | Znak`Latn` |
| Japonština | `ja` | Japonština`Jpan` | <--> | Znak`Latn` |
| Kannadština | `kn` | Kannadština`Knda` | --> | Znak`Latn` |
| Malajalámština | `ml` | Malajalámština`Mlym` | --> | Znak`Latn` |
| Maráthština | `mr` | Koncový`Deva` | --> | Znak`Latn` |
| Krí | `or` | Krí`Orya` | <--> | Znak`Latn` |
| Paňdžábština | `pa` | Západní`Guru`  | <--> | Znak`Latn`  |
| Srbština (cyrilice) | `sr-Cyrl` | B`Cyrl`  | --> | Znak`Latn` |
| Srbština (latinka) | `sr-Latn` | Znak`Latn` | --> | B`Cyrl`|
| Tamilština | `ta` | Tamilština`Taml` | --> | Znak`Latn` |
| Telugština | `te` | Telugština`Telu` | --> | Znak`Latn` |
| Thajština | `th` | Thajština`Thai` | --> | Znak`Latn` |

## <a name="dictionary"></a>Slovník

Slovník podporuje následující jazyky pro nebo z angličtiny pomocí vyhledávacích a ukázkových metod.

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
| Svahilština      | `sw`          |
| Klingon      | `tlh`          |
| Korejština      | `ko`          |
| Lotyština      | `lv`          |
| Litevština      | `lt`          |
| Malajština      | `ms`          |
| Maltština      | `mt`          |
| Norština      | `nb`          |
| Perština      | `fa`          |
| Polština      | `pl`          |
| Portugalština (Brazílie)     | `pt-br`          |
| Rumunština      | `ro`          |
| Ruština      | `ru`          |
| Srbština (latinka)      | `sr-Latn`          |
| Slovenština     | `sk`          |
| Slovinština      | `sl`          |
| Španělština      | `es`          |
| Švédština      | `sv`          |
| Tamilština      | `ta`          |
| Thajština      | `th`          |
| Turečtina      | `tr`          |
| Ukrajinština      | `uk`          |
| Urdština      | `ur`          |
| Vietnamština      | `vi`          |
| Velština      | `cy`          |

## <a name="detect"></a>Zjišťování

Translator detekuje všechny jazyky, které jsou k dispozici pro překlad a převod.


## <a name="access-the-translator-language-list-programmatically"></a>Přístup k seznamu jazyků překladatele prostřednictvím kódu programu

Můžete načíst seznam podporovaných jazyků pro překladatele v 3.0 pomocí metody languages. Můžete zobrazit seznam podle funkcí, kód jazyka a také název jazyka v angličtině nebo v jakémkoli jiném podporovaném jazyce. Služba Microsoft Translator automaticky aktualizuje tento seznam, protože jsou k dispozici nové jazyky.

[Referenční dokumentace operací zobrazení jazyků](reference/v3-0-languages.md)

## <a name="customization"></a>Přizpůsobení

Následující jazyky jsou k dispozici pro přizpůsobení nebo z angličtiny pomocí [vlastního překladatele](https://aka.ms/CustomTranslator).

| Jazyk    | Kód jazyka |
|:----------- |:-------------:|
| Arabština       | `ar`          |
| Bengálština      | `bn`          |
| Bosenština (latinka)      | `bs`          |
| Bulharština      | `bg`          |
| Čínština (zjednodušená)      | `zh-Hans`          |
|Čínština (tradiční)|   `zh-Hant`   |
| Chorvatština      | `hr`          |
| Čeština      | `cs`          |
| Dánština      | `da`          |
| Nizozemština      | `nl`          |
| Angličtina    | `en`     |
| Estonština      | `et`          |
| Finština      | `fi`          |
| Francouzština      | `fr`          |
| Němčina      | `de`          |
| Řečtina      | `el`          |
| Hebrejština      | `he`          |
| Hindština      | `hi`          |
| Maďarština      | `hu`          |
| Islandština | `is` |
| Indonéština|   `id`    |
| Irština | `ga`  |
| Italština      | `it`          |
| Japonština      | `ja`          |
| Svahilština|    `sw`    |
| Korejština      | `ko`          |
| Lotyština      | `lv`          |
| Litevština      | `lt`          |
| Malgašština| `mg`    |
| Maorština| `mi`  |
| Norština      | `nb`          |
| Perština      | `fa`          |
| Polština      | `pl`          |
| Portugalština (Brazílie) | `pt-br` |
| Rumunština      | `ro`          |
| Ruština      | `ru`          |
| Samoan|   `sm`    |
| Srbština (latinka)      | `sr-Latn`          |
| Slovenština     | `sk`          |
| Slovinština      | `sl`          |
| Španělština      | `es`          |
| Švédština      | `sv`          |
| Thajština      | `th`          |
| Turečtina      | `tr`          |
| Ukrajinština      | `uk`          |
| Vietnamština      | `vi`          |
| Velština | `cy` |

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Přístup k seznamu na webu Microsoft Translator

Pokud chcete rychle zobrazit jazyky, na webu Microsoft Translator se zobrazí všechny jazyky podporované překladatelem a rozhraními API pro rozpoznávání řeči. Tento seznam neobsahuje informace specifické pro vývojáře, jako jsou kódy jazyků.

[Zobrazit seznam jazyků](https://www.microsoft.com/translator/languages.aspx)
