---
title: Jazyková podpora - Translator Text API
titleSuffix: Azure Cognitive Services
description: Překladač text api podporuje následující jazyky pro překlad textu na text pomocí neural strojový překlad (NMT).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/10/2020
ms.author: swmachan
ms.openlocfilehash: 0ecde5acb7dc57ed9e5802c1589d5813a9206643
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684831"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Jazyková a regionální podpora rozhraní Translator Text API

Překladač text api podporuje následující jazyky pro překlad textu na text. Neurální strojový překlad (NMT) je nový standard pro vysoce kvalitní strojový překlad s umělou ai a je k dispozici jako výchozí použití V3 translator text api, když je k dispozici nervový systém.

[Další informace o tom, jak funguje strojový překlad](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Překlad

**Rozhraní API překladače V2**

> [!NOTE]
> 30. dubna 2018 byl Zastaral. Migrujte své aplikace do V3, abyste mohli využívat nové funkce dostupné výhradně ve V3.

* Pouze statistické: Pro tento jazyk není k dispozici žádný nervový systém.
* Nervové k dispozici: Nervový systém je k dispozici. Použijte parametr `category=generalnn` pro přístup k nervovému systému.
* Neurální výchozí: Neural je výchozí překladový systém. Pomocí parametru `category=smt` můžete získat přístup ke statistickému systému pro použití v centru Microsoft Translator Hub.
* Pouze neurální: K dispozici je pouze neurální překlad.

**Rozhraní API překladače V3** V3 Translator API je neurální ve výchozím nastavení a statistické systémy jsou k dispozici pouze v případě, že neexistuje žádný nervový systém.

> [!NOTE]
> V současné době je podmnožina neurálních jazyků k dispozici v custom translatoru a postupně přidáváme další. [Zobrazení jazyků, které jsou aktuálně k dispozici v okně Vlastní překladač](#customization).

|Jazyk|  Kód jazyka|  V3 API|
|:-----|:-----:|:-----|
|Afrikánština| `af`|   Neuronové|
|Arabština|    `ar`    |   Neuronové|
|Bangla|    `bn`    |   Neuronové|
|Bosenština (latinka)|   `bs`    |   Neuronové|
|Bulharština| `bg`    |   Neuronové|
|Kantonská (tradiční)|   `yue`|  Statistické|
|Katalánština|   `ca`    |   Statistické|
|Čínština (zjednodušená)|    `zh-Hans`|Neuronové|
|Čínština (tradiční)|   `zh-Hant`       |Neuronové|
|Chorvatština|  `hr`    |Neuronové|
|Čeština| `cs`    |   Neuronové|
|Dánština|    `da`        |Neuronové|
|Nizozemština| `nl`|   Neuronové|
|Angličtina|   `en`    |   Neuronové|
|Estonština|  `et`    |   Neuronové|
|Fidžijská|    `fj`    |   Statistické|
|Filipino|  `fil`   |   Statistické|
|Finština|   `fi`    |   Neuronové|
|Francouzština|    `fr`    |   Neuronové|
|Němčina|    `de`    |   Neuronové|
|Řečtina| `el`    |   Neuronové|
|Gudžarátština|  `gu`    |   Neuronové|
|Haitská kreolština|    `ht`        |Statistické|
|Hebrejština |`he`   |Neuronové
|Hindština| `hi`    |   Neuronové|
|Hmong Daw| `mww`   |   Statistické|
|Maďarština| `hu`    |   Neuronové|
|Islandština| `is`    |   Neuronové|
|Indonéština|    `id`    |   Statistické|
|Irština | `ga`| Neuronové
|Italština|   `it`    |   Neuronové|
|Japonština|  `ja`    |   Neuronové|
|Kannadština|`kn`| Neuronové
|Kiswahili| `sw`    |   Statistické|
|Klingonské|   `tlh`   |   Statistické|
|Klingon (plqaD)|   `tlh-Qaak`  |   Statistické|
|Korejština |`ko`   |   Neuronové|
|Lotyština|   `lv`    |   Neuronové|
|Litevština|    `lt`    |   Neuronové|
|Malagasy|  `mg`    |   Statistické|
|Malajština| `ms`        |Statistické|
|Malajalámština| `ml` | Neuronové
|Maltština|   `mt`    |   Statistické|
|Maorština| `mi`  | Neuronové|
|Maráthština| `mr`  | Neuronové|
|Norština| `nb`    |   Neuronové|
|Perština|   `fa`    |   Neuronové|
|Polština|    `pl`    |   Neuronové|
|Portugalština (Brazílie)|   `pt-br` |   Neuronové|
|portugalština (Portugalsko)| `pt-pt` | Neuronové
|Paňdžábština|`pa`|Neuronové
|Queretaro Otomi|   `otq`   |   Statistické|
|Rumunština|  `ro`    |   Neuronové|
|Ruština|   `ru`    |   Neuronové|
|Samoan|    `sm`    |   Statistické|
|Srbština (cyrilice)|    `sr-Cyrl`|  Statistické|
|Srbština (latinka)|   `sr-Latn`       |Statistické|
|Slovenština|    `sk`    |   Neuronové|
|Slovinština| `sl`    |   Neuronové|
|Španělština|   `es`    |   Neuronové|
|Švédština|   `sv`    |Neuronové|
|Tahitian|  `ty`    |Statistické|
|Tamilština| `ta`    |   Neuronové|
|Telugština|    `te`    |   Neuronové|
|Thajština|  `th`    |   Neuronové|
|Tongan|    `to`    |   Statistické|
|Turečtina|   `tr`        |Neuronové|
|Ukrajinština| `uk`    |   Neuronové|
|Urdština|  `ur`    |   Statistické|
|Vietnamština|    `vi`    |   Neuronové|
|Velština| `cy`    |   Neuronové|
|Yucatec Maya|  `yua`   |   Statistické|

> [!NOTE]
> Kód `pt` jazyka bude `pt-br`ve výchozím nastavení , portugalština (Brazílie).

## <a name="transliteration"></a>Přepis

Transliterate metoda podporuje následující jazyky. V "To/From", "<-->" označuje, že jazyk může být přepsán z nebo na některý z uvedených skriptů. "-- >" označuje, že jazyk lze překládat pouze z jednoho skriptu do druhého.

| Jazyk    | Kód jazyka | Skript | Směr | Skript|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabština | `ar` | Arabština`Arab` | <--> | Latinské`Latn` |
|Bangla  | `bn` | Bengálština`Beng` | <--> | Latinské`Latn` |
| Čínština (zjednodušená) | `zh-Hans` | Zjednodušená čínština`Hans`| <--> | Latinské`Latn` |
| Čínština (zjednodušená) | `zh-Hans` | Zjednodušená čínština`Hans`| <--> | Tradiční čínština`Hant`|
| Čínština (tradiční) | `zh-Hant` | Tradiční čínština`Hant`| <--> | Latinské`Latn` |
| Čínština (tradiční) | `zh-Hant` | Tradiční čínština`Hant`| <--> | Zjednodušená čínština`Hans` |
| Gudžarátština | `gu`  | Gudžarátština`Gujr` | --> | Latinské`Latn` |
| Hebrejština | `he` | Hebrejština`Hebr` | <--> | Latinské`Latn` |
| Hindština | `hi` | Devanagari`Deva` | <--> | Latinské`Latn` |
| Japonština | `ja` | Japonština`Jpan` | <--> | Latinské`Latn` |
| Kannadština | `kn` | Kannadština`Knda` | --> | Latinské`Latn` |
| Malajalámština | `ml` | Malajalámština`Mlym` | --> | Latinské`Latn` |
| Maráthština | `mr` | Devanagari`Deva` | --> | Latinské`Latn` |
| Uríská | `or` | Uríská`Orya` | <--> | Latinské`Latn` |
| Paňdžábština | `pa` | Gurmukhi`Guru`  | <--> | Latinské`Latn`  |
| Srbština (cyrilice) | `sr-Cyrl` | Cyrilice`Cyrl`  | --> | Latinské`Latn` |
| Srbština (latinka) | `sr-Latn` | Latinské`Latn` | --> | Cyrilice`Cyrl`|
| Tamilština | `ta` | Tamilština`Taml` | --> | Latinské`Latn` |
| Telugština | `te` | Telugština`Telu` | --> | Latinské`Latn` |
| Thajština | `th` | Thajština`Thai` | --> | Latinské`Latn` |

## <a name="dictionary"></a>Slovník

Slovník podporuje následující jazyky do nebo z angličtiny pomocí metody Vyhledávání a Příklady.

| Jazyk    | Kód jazyka |
|:----------- |:-------------:|
| Afrikánština      | `af`          |
| Arabština       | `ar`          |
| Bangla      | `bn`          |
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
| Hmong Daw      | `mww`          |
| Maďarština      | `hu`          |
| Islandština    | `is`  |
| Indonéština      | `id`          |
| Italština      | `it`          |
| Japonština      | `ja`          |
| Kiswahili      | `sw`          |
| Klingonské      | `tlh`          |
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

Překladač Text API detekuje všechny jazyky, které jsou k dispozici pro překlad a přepis.


## <a name="access-the-translator-text-api-language-list-programmatically"></a>Programový přístup k seznamu jazyků rozhraní TRANSLATOR Text API

Pomocí metody Languages můžete načíst seznam podporovaných jazyků pro překladač textového rozhraní API v3.0. Seznam můžete zobrazit podle funkce, kódu jazyka a názvu jazyka v angličtině nebo jiném podporovaném jazyce. Tento seznam je automaticky aktualizován službou Microsoft Translator, jakmile jsou k dispozici nové jazyky.

[Zobrazit referenční dokumentaci k operaci Jazyky](reference/v3-0-languages.md)

## <a name="customization"></a>Přizpůsobení

Následující jazyky jsou k dispozici pro vlastní nastavení do nebo z angličtiny pomocí [vlastní překladač](https://aka.ms/CustomTranslator).

| Jazyk    | Kód jazyka |
|:----------- |:-------------:|
| Arabština       | `ar`          |
| Bangla      | `bn`          |
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
| Kiswahili|    `sw`    |
| Korejština      | `ko`          |
| Lotyština      | `lv`          |
| Litevština      | `lt`          |
| Malagasy| `mg`    |
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

Pro rychlý pohled na jazyky, Microsoft Translator webové stránky zobrazuje všechny jazyky podporované překladač text a řeč api. Tento seznam neobsahuje informace specifické pro vývojáře, jako jsou kódy jazyků.

[Zobrazit seznam jazyků](https://www.microsoft.com/translator/languages.aspx)
