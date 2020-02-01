---
title: Jazyková podpora – Translator Text API
titleSuffix: Azure Cognitive Services
description: Translator Text API podporuje následující jazyky pro překlad textu na text pomocí NMT (neuronové Machine Translation).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 12/02/2019
ms.author: swmachan
ms.openlocfilehash: 2ec8d389c99ad96e59bf49d4345855fa44d6c7aa
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902000"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Podpora jazyků a oblastí pro Translator Text API

Translator Text API podporuje překlady textu na text v následujících jazycích. Neuronové Machine Translation (NMT) je nový standard pro vysoce kvalitní překlady počítačů s podporou AI a je k dispozici jako výchozí hodnota V3 Translator Text API, pokud je k dispozici systém neuronové.

[Další informace o tom, jak strojový překlad funguje](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Překlad

**Rozhraní API pro překladatele v2**

> [!NOTE]
> Verze V2 byla zastaralá od 30. dubna 2018. Pokud chcete využívat nové funkce, které jsou k dispozici výhradně v v3, migrujte prosím své aplikace na v3.

* Pouze statistická: pro tento jazyk není k dispozici žádný neuronové systém.
* Neuronové k dispozici: systém neuronové je k dispozici. Pro přístup k neuronové systému použijte parametr `category=generalnn`.
* Neuronové výchozí: výchozí překladový systém je neuronové. Použijte parametr `category=smt` pro přístup ke statistickému systému pro použití s centrem Microsoft Translator.
* Pouze neuronové: je k dispozici pouze překlad neuronové.

**Rozhraní API pro překladatele V3** Rozhraní API pro překladatele v3 je ve výchozím nastavení neuronové a statistické systémy jsou k dispozici pouze v případě, že neexistuje žádný systém neuronové.

> [!NOTE]
> V současné době je k dispozici podmnožina jazyků neuronové ve vlastním překladateli a postupně přidáváme další. [Zobrazit jazyky, které jsou aktuálně k dispozici ve vlastním překladateli](#customization).

|Jazyk|  Kód jazyka|  V3 API|
|:-----|:-----:|:-----|
|Afrikánština| `af`|   Neurální|
|arabština|    `ar`    |   Neurální|
|Bengálština|    `bn`    |   Neurální|
|Bosenština (latinka)|   `bs`    |   Neurální|
|Bulharština| `bg`    |   Neurální|
|Kantonština (tradiční)|   `yue`|  Ložený|
|Katalánština|   `ca`    |   Ložený|
|Zjednodušená čínština|    `zh-Hans`|Neurální|
|Tradiční čínština|   `zh-Hant`       |Neurální|
|Chorvatština|  `hr`    |Neurální|
|Čeština| `cs`    |   Neurální|
|dánština|    `da`        |Neurální|
|Holandština| `nl`|   Neurální|
|Angličtina|   `en`    |   Neurální|
|Estonština|  `et`    |   Neurální|
|Vládní|    `fj`    |   Ložený|
|Filipínština|  `fil`   |   Ložený|
|Finština|   `fi`    |   Neurální|
|Francouzština|    `fr`    |   Neurální|
|Němčina|    `de`    |   Neurální|
|Řečtina| `el`    |   Neurální|
|Haitská kreolština|    `ht`        |Ložený|
|Hebrejština |`he`   |Neurální
|Hindština| `hi`    |   Neurální|
|Hmong Daw| `mww`   |   Ložený|
|Maďarština| `hu`    |   Neurální|
|Islandština| `is`    |   Neurální|
|Indonéština|    `id`    |   Ložený|
|Irština | `ga`| Neurální
|italština|   `it`    |   Neurální|
|Japonština|  `ja`    |   Neurální|
|Kannadština|`kn`| Neurální
|Svahilština| `sw`    |   Ložený|
|Klingon|   `tlh`   |   Ložený|
|Klingon (plqaD)|   `tlh-Qaak`  |   Ložený|
|Korejština |`ko`   |   Neurální|
|Lotyština|   `lv`    |   Neurální|
|Litevština|    `lt`    |   Neurální|
|Malgašský|  `mg`    |   Ložený|
|Malajština| `ms`        |Ložený|
|Malajálamština| `ml` | Neurální
|Maltština|   `mt`    |   Ložený|
|Maorština| `mi`  | Neurální|
|norština| `nb`    |   Neurální|
|Perština|   `fa`    |   Neurální|
|polština|    `pl`    |   Neurální|
|Portugalština (Brazílie)|   `pt-br` |   Neurální|
|Portugalština (Portugalsko)| `pt-pt` | Neurální
|Paňdžábština|`pa`|Neurální
|Queretaro Otomi|   `otq`   |   Ložený|
|Rumunština|  `ro`    |   Neurální|
|ruština|   `ru`    |   Neurální|
|Samoan|    `sm`    |   Ložený|
|Srbština (cyrilice)|    `sr-Cyrl`|  Ložený|
|Srbština (latinka)|   `sr-Latn`       |Ložený|
|Slovenština|    `sk`    |   Neurální|
|Slovinština| `sl`    |   Neurální|
|Španělština|   `es`    |   Neurální|
|švédština|   `sv`    |Neurální|
|Tahitian|  `ty`    |Ložený|
|Tamilština| `ta`    |   Neurální|
|Telugština|    `te`    |   Neurální|
|Thajština|  `th`    |   Neurální|
|Tonžská|    `to`    |   Ložený|
|turečtina|   `tr`        |Neurální|
|Ukrajinština| `uk`    |   Neurální|
|Urdština|  `ur`    |   Ložený|
|Vietnamština|    `vi`    |   Neurální|
|Velština| `cy`    |   Neurální|
|Yucatec Maya|  `yua`   |   Ložený|

> [!NOTE]
> Kód jazyka `pt` bude ve výchozím nastavení `pt-br`, portugalština (Brazílie).

## <a name="transliteration"></a>Transkripce

Metoda přepisu podporuje následující jazyky. V "do/z", "<-->" označuje, že jazyk lze přepřepisovat z nebo na některý z uvedených skriptů. "-->" Označuje, že jazyk lze přepřepisovat pouze z jednoho skriptu na druhý.

| Jazyk    | Kód jazyka | Skript | Do/z | Skript|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| arabština | `ar` | Arabské `Arab` | <--> | `Latn` latinky |
|Bengálština  | `bn` | Bengálský `Beng` | <--> | `Latn` latinky |
| Čínština (zjednodušená) | `zh-Hans` | Zjednodušená čínština `Hans`| <--> | `Latn` latinky |
| Čínština (zjednodušená) | `zh-Hans` | Zjednodušená čínština `Hans`| <--> | Tradiční čínština `Hant`|
| Čínština (tradiční) | `zh-Hant` | Tradiční čínština `Hant`| <--> | `Latn` latinky |
| Čínština (tradiční) | `zh-Hant` | Tradiční čínština `Hant`| <--> | Zjednodušená čínština `Hans` |
| Gudžarátština | `gu`  | Gudžarátský `Gujr` | --> | `Latn` latinky |
| Hebrejština | `he` | Hebrejská `Hebr` | <--> | `Latn` latinky |
| Hindština | `hi` | `Deva` Dévanágarí | <--> | `Latn` latinky |
| Japonština | `ja` | Japonské `Jpan` | <--> | `Latn` latinky |
| Kannadština | `kn` | Kannada `Knda` | --> | `Latn` latinky |
| Malajálamština | `ml` | Malajalámština `Mlym` | --> | `Latn` latinky |
| Maráthština | `mr` | `Deva` Dévanágarí | --> | `Latn` latinky |
| Oriya | `or` | Oriya `Orya` | <--> | `Latn` latinky |
| Paňdžábština | `pa` | Gurmukhi `Guru`  | <--> | `Latn` latinky  |
| Srbština (cyrilice) | `sr-Cyrl` | `Cyrl` v cyrilici  | --> | `Latn` latinky |
| Srbština (latinka) | `sr-Latn` | `Latn` latinky | --> | `Cyrl` v cyrilici|
| Tamilština | `ta` | Kanadské `Taml` | --> | `Latn` latinky |
| Telugština | `te` | Telugské `Telu` | --> | `Latn` latinky |
| Thajština | `th` | Thajské `Thai` | <--> | `Latn` latinky |

## <a name="dictionary"></a>Slovník

Slovník podporuje následující jazyky pro nebo z angličtiny pomocí vyhledávacích a ukázkových metod.

| Jazyk    | Kód jazyka |
|:----------- |:-------------:|
| Afrikánština      | `af`          |
| arabština       | `ar`          |
| Bengálština      | `bn`          |
| Bosenština (latinka)      | `bs`          |
| Bulharština      | `bg`          |
| Katalánština      | `ca`          |
| Zjednodušená čínština      | `zh-Hans`          |
| Chorvatština      | `hr`          |
| Čeština      | `cs`          |
| dánština      | `da`          |
| Holandština      | `nl`          |
| Estonština      | `et`          |
| Finština      | `fi`          |
| Francouzština      | `fr`          |
| Němčina      | `de`          |
| Řečtina      | `el`          |
| Haitská kreolština      | `ht`          |
| Hebrejština      | `he`          |
| Hindština      | `hi`          |
| Hmong Daw      | `mww`          |
| Maďarština      | `hu`          |
| Islandština    | `is`  |
| Indonéština      | `id`          |
| italština      | `it`          |
| Japonština      | `ja`          |
| Svahilština      | `sw`          |
| Klingon      | `tlh`          |
| Korejština      | `ko`          |
| Lotyština      | `lv`          |
| Litevština      | `lt`          |
| Malajština      | `ms`          |
| Maltština      | `mt`          |
| norština      | `nb`          |
| Perština      | `fa`          |
| polština      | `pl`          |
| Portugalština (Brazílie)     | `pt-br`          |
| Rumunština      | `ro`          |
| ruština      | `ru`          |
| Srbština (latinka)      | `sr-Latn`          |
| Slovenština     | `sk`          |
| Slovinština      | `sl`          |
| Španělština      | `es`          |
| švédština      | `sv`          |
| Tamilština      | `ta`          |
| Thajština      | `th`          |
| turečtina      | `tr`          |
| Ukrajinština      | `uk`          |
| Urdština      | `ur`          |
| Vietnamština      | `vi`          |
| Velština      | `cy`          |

## <a name="detect"></a>Zjišťování

Translator Text API detekuje všechny jazyky, které jsou k dispozici pro překlad a převod.


## <a name="access-the-translator-text-api-language-list-programmatically"></a>Přístup k seznamu jazyků Translator Text API programově

Seznam podporovaných jazyků pro Translator Text API v 3.0 můžete načíst pomocí metody languages. Můžete zobrazit seznam podle funkcí, kód jazyka a také název jazyka v angličtině nebo v jakémkoli jiném podporovaném jazyce. Služba Microsoft Translator automaticky aktualizuje tento seznam, protože jsou k dispozici nové jazyky.

[Referenční dokumentace operací zobrazení jazyků](reference/v3-0-languages.md)

## <a name="customization"></a>Přizpůsobení

Následující jazyky jsou k dispozici pro přizpůsobení nebo z angličtiny pomocí [vlastního překladatele](https://aka.ms/CustomTranslator).

| Jazyk    | Kód jazyka |
|:----------- |:-------------:|
| arabština       | `ar`          |
| Bengálština      | `bn`          |
| Bosenština (latinka)      | `bs`          |
| Bulharština      | `bg`          |
| Zjednodušená čínština      | `zh-Hans`          |
|Tradiční čínština|   `zh-Hant`   |
| Chorvatština      | `hr`          |
| Čeština      | `cs`          |
| dánština      | `da`          |
| Holandština      | `nl`          |
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
| italština      | `it`          |
| Japonština      | `ja`          |
| Svahilština|    `sw`    |
| Korejština      | `ko`          |
| Lotyština      | `lv`          |
| Litevština      | `lt`          |
| Malgašský| `mg`    |
| Maorština| `mi`  |
| norština      | `nb`          |
| Perština      | `fa`          |
| polština      | `pl`          |
| Portugalština (Brazílie) | `pt-br` |
| Rumunština      | `ro`          |
| ruština      | `ru`          |
| Samoan|   `sm`    |
| Srbština (latinka)      | `sr-Latn`          |
| Slovenština     | `sk`          |
| Slovinština      | `sl`          |
| Španělština      | `es`          |
| švédština      | `sv`          |
| Thajština      | `th`          |
| turečtina      | `tr`          |
| Ukrajinština      | `uk`          |
| Vietnamština      | `vi`          |
| Velština | `cy` |

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Přístup k seznamu na webu Microsoft Translator

Chcete-li se rychle podívat na jazyky, na webu Microsoft Translator se zobrazí všechny jazyky podporované rozhraním API Translator Text a Speech. Tento seznam neobsahuje informace specifické pro vývojáře, jako jsou kódy jazyků.

[Zobrazit seznam jazyků](https://www.microsoft.com/translator/languages.aspx)
