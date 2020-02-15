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
ms.date: 02/10/2020
ms.author: swmachan
ms.openlocfilehash: a4f9833e8dd14dc7c8ec5849cb809bf2089a5dae
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2020
ms.locfileid: "77206119"
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
|Afrikánština| `af`|   Neuronové|
|arabština|    `ar`    |   Neuronové|
|Bengálština|    `bn`    |   Neuronové|
|Bosenština (latinka)|   `bs`    |   Neuronové|
|Bulharština| `bg`    |   Neuronové|
|Kantonština (tradiční)|   `yue`|  Ložený|
|Katalánština|   `ca`    |   Ložený|
|Zjednodušená čínština|    `zh-Hans`|Neuronové|
|Tradiční čínština|   `zh-Hant`       |Neuronové|
|Chorvatština|  `hr`    |Neuronové|
|Čeština| `cs`    |   Neuronové|
|dánština|    `da`        |Neuronové|
|Holandština| `nl`|   Neuronové|
|Angličtina|   `en`    |   Neuronové|
|Estonština|  `et`    |   Neuronové|
|Vládní|    `fj`    |   Ložený|
|Filipínština|  `fil`   |   Ložený|
|Finština|   `fi`    |   Neuronové|
|Francouzština|    `fr`    |   Neuronové|
|Němčina|    `de`    |   Neuronové|
|Řečtina| `el`    |   Neuronové|
|Haitská kreolština|    `ht`        |Ložený|
|Hebrejština |`he`   |Neuronové
|Hindština| `hi`    |   Neuronové|
|Hmong Daw| `mww`   |   Ložený|
|Maďarština| `hu`    |   Neuronové|
|Islandština| `is`    |   Neuronové|
|Indonéština|    `id`    |   Ložený|
|Irština | `ga`| Neuronové
|italština|   `it`    |   Neuronové|
|Japonština|  `ja`    |   Neuronové|
|Kannada|`kn`| Neuronové
|Svahilština| `sw`    |   Ložený|
|Klingon|   `tlh`   |   Ložený|
|Klingon (plqaD)|   `tlh-Qaak`  |   Ložený|
|Korejština |`ko`   |   Neuronové|
|Lotyština|   `lv`    |   Neuronové|
|Litevština|    `lt`    |   Neuronové|
|Malgašský|  `mg`    |   Ložený|
|Malajština| `ms`        |Ložený|
|Malajálamština| `ml` | Neuronové
|Maltština|   `mt`    |   Ložený|
|Maorština| `mi`  | Neuronové|
|norština| `nb`    |   Neuronové|
|Perština|   `fa`    |   Neuronové|
|polština|    `pl`    |   Neuronové|
|Portugalština (Brazílie)|   `pt-br` |   Neuronové|
|Portugalština (Portugalsko)| `pt-pt` | Neuronové
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
|švédština|   `sv`    |Neuronové|
|Tahitian|  `ty`    |Ložený|
|Tamilština| `ta`    |   Neuronové|
|Telugština|    `te`    |   Neuronové|
|Thajština|  `th`    |   Neuronové|
|Tonžská|    `to`    |   Ložený|
|turečtina|   `tr`        |Neuronové|
|Ukrajinština| `uk`    |   Neuronové|
|Urdština|  `ur`    |   Ložený|
|Vietnamština|    `vi`    |   Neuronové|
|Velština| `cy`    |   Neuronové|
|Yucatec Maya|  `yua`   |   Ložený|

> [!NOTE]
> Kód jazyka `pt` bude ve výchozím nastavení `pt-br`, portugalština (Brazílie).

## <a name="transliteration"></a>Transkripci

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
| Kannada | `kn` | Kannadské `Knda` | --> | `Latn` latinky |
| Malajálamština | `ml` | Malajalámština `Mlym` | --> | `Latn` latinky |
| Maráthština | `mr` | `Deva` Dévanágarí | --> | `Latn` latinky |
| Oriya | `or` | Uríské `Orya` | <--> | `Latn` latinky |
| Paňdžábština | `pa` | Gurmukhí `Guru`  | <--> | `Latn` latinky  |
| Srbština (cyrilice) | `sr-Cyrl` | `Cyrl` v cyrilici  | --> | `Latn` latinky |
| Srbština (latinka) | `sr-Latn` | `Latn` latinky | --> | `Cyrl` v cyrilici|
| Tamilština | `ta` | Kanadské `Taml` | --> | `Latn` latinky |
| Telugština | `te` | Telugské `Telu` | --> | `Latn` latinky |
| Thajština | `th` | Thajské `Thai` | --> | `Latn` latinky |

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
| Ruština      | `ru`          |
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

## <a name="detect"></a>Detect

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
| Ruština      | `ru`          |
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
