---
title: Podpora jazyků – Translator Text API
titleSuffix: Azure Cognitive Services
description: Seznam podporovaných rozhraní Translator Text API přirozeného jazyka.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 14ab1988f28de214036fa80fa36a4006a3359f30
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67435883"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Podpora jazyka a oblasti pro rozhraní Translator Text API

Translator Text API podporuje následující jazyky pro překlad textu do textu. Neurální strojový překlad sítí (NMT) je novým standardem pro strojový překlad s využitím AI vysoce kvalitní a je k dispozici jako výchozí používá Translator Text API V3 neuronových sítí systému je k dispozici.

[Další informace o tom, jak funguje strojový překlad](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Překlad

**V2 Translator API**

> [!NOTE]
> 30. dubna 2018 se přestala nabízet v2. Migrujte prosím svoje aplikace na V3, abyste mohli využívat nové funkce je dostupná výhradně ve verzi 3.

* Jenom statistické: Žádné neuronových sítí systému je k dispozici pro tento jazyk.
* Neuronových sítí k dispozici: Neurální systém k dispozici. Použijte parametr `category=generalnn` pro přístup k systému neuronových sítí.
* Neurální výchozí nastavení: Neurální je výchozí překladový systém. Použijte parametr `category=smt` pro přístup k statistické lokality pro použití v centru Microsoft Translator.
* Neurální pouze: Je k dispozici pouze Neurální překlady.

**V3 Translator API** V3 Translator API je ve výchozím nastavení neuronových sítí a statistické systémy jsou k dispozici, pouze pokud neexistuje žádný systém neuronových sítí.

> [!NOTE]
> V současné době jsou dostupné v vlastní Translator podmnožiny jazyků neuronových sítí a postupně přidáváme další značky. [Zobrazit jazyky, které jsou aktuálně k dispozici ve vlastní Translator](#customization).

|Jazyk|  Kód jazyka|  V2 API| V3 API|
|:-----|:-----:|:-----|:-----|
|Afrikánština| `af`    |Jenom statistické|  Neurální|
|arabština|    `ar`    |Neuronových sítí k dispozici|  Neurální|
|Bengálština|    `bn`    |Neuronových sítí k dispozici|  Neurální|
|Bosenština (latinka)|   `bs`    |Neuronových sítí k dispozici|  Neurální|
|Bulharština| `bg`    |Neuronových sítí k dispozici|  Neurální|
|Kantonština (tradiční)|   `yue`   |Jenom statistické|  Statistické|
|Katalánština|   `ca`    |Jenom statistické|  Statistické|
|Zjednodušená čínština|    `zh-Hans`   |Výchozí neuronových sítí |Neurální|
|Tradiční čínština|   `zh-Hant`   |Výchozí neuronových sítí |Neurální|
|Chorvatština|  `hr`    |Neuronových sítí k dispozici|  Neurální|
|Čeština| `cs`    |Neuronových sítí k dispozici|  Neurální|
|dánština|    `da`    |Neuronových sítí k dispozici   |Neurální|
|Holandština| `nl`    |Neuronových sítí k dispozici|  Neurální|
|Angličtina|   `en`    |Neuronových sítí k dispozici|  Neurální|
|Estonština|  `et`    |Neuronových sítí k dispozici|  Neurální|
|Vládní|    `fj`    |Jenom statistické|  Statistické|
|Filipínština|  `fil`   |Jenom statistické|  Statistické|
|Finština|   `fi`    |Neuronových sítí k dispozici|  Neurální|
|Francouzština|    `fr`    |Neuronových sítí k dispozici|  Neurální|
|Němčina|    `de`    |Neuronových sítí k dispozici|  Neurální|
|Řečtina| `el`    |Neuronových sítí k dispozici|  Neurální|
|Haitská kreolština|    `ht`    |Jenom statistické   |Statistické|
|Hebrejština |`he`   |Neuronových sítí k dispozici   |Neurální|
|Hindština| `hi`    |Výchozí neuronových sítí|    Neurální|
|Hmong Daw| `mww`   |Jenom statistické|  Statistické|
|Maďarština| `hu`    |Neuronových sítí k dispozici|  Neurální|
|Islandština| `is`    |Pouze neuronových sítí|   Neurální|
|Indonéština|    `id`    |Jenom statistické|  Statistické|
|italština|   `it`    |Neuronových sítí k dispozici|  Neurální|
|Japonština|  `ja`    |Neuronových sítí k dispozici|  Neurální|
|Svahilština| `sw`    |Jenom statistické|  Statistické|
|Klingon|   `tlh`   |Jenom statistické|  Statistické|
|Klingon (plqaD)|   `tlh-Qaak`  |Jenom statistické|  Statistické|
|Korejština |`ko`   |Neuronových sítí k dispozici|  Neurální|
|Lotyština|   `lv`    |Neuronových sítí k dispozici|  Neurální|
|Litevština|    `lt`    |Neuronových sítí k dispozici|  Neurální|
|Malgašský|  `mg`    |Jenom statistické|  Statistické|
|Malajština| `ms`    |Jenom statistické   |Statistické|
|Maltština|   `mt`    |Jenom statistické|  Statistické|
|norština| `nb`    |Neuronových sítí k dispozici|  Neurální|
|Perština|   `fa`    |Jenom statistické|  Statistické|
|polština|    `pl`    |Neuronových sítí k dispozici|  Neurální|
|Portugalština|    `pt`    |Neuronových sítí k dispozici|  Neurální|
|Queretaro Otomi|   `otq`   |Jenom statistické|  Statistické|
|Rumunština|  `ro`    |Neuronových sítí k dispozici|  Neurální|
|ruština|   `ru`    |Neuronových sítí k dispozici|  Neurální|
|Samoan|    `sm`    |Jenom statistické|  Statistické|
|Srbština (cyrilice)|    `sr-Cyrl`   |Jenom statistické|  Statistické|
|Srbština (latinka)|   `sr-Latn`   |Jenom statistické   |Statistické|
|Slovenština|    `sk`    |Neuronových sítí k dispozici|  Neurální|
|Slovinština| `sl`    |Neuronových sítí k dispozici|  Neurální|
|Španělština|   `es`    |Neuronových sítí k dispozici|  Neurální|
|švédština|   `sv`    |Neuronových sítí k dispozici   |Neurální|
|Tahitian|  `ty`    |Jenom statistické|  Statistické|
|Tamilština| `ta`    |Jenom statistické|  Statistické|
|Telugština|    `te`    |Pouze neuronových sítí|   Neurální|
|Thajština|  `th`    |Neuronových sítí k dispozici|  Neurální|
|Tonžská|    `to`    |Jenom statistické|  Statistické|
|turečtina|   `tr`    |Neuronových sítí k dispozici   |Neurální|
|Ukrajinština| `uk`    |Neuronových sítí k dispozici|  Neurální|
|Urdština|  `ur`    |Jenom statistické|  Statistické|
|Vietnamština|    `vi`    |Neuronových sítí k dispozici|  Neurální|
|Velština| `cy`    |Neuronových sítí k dispozici|  Neurální|
|Yucatec Maya|  `yua`   |Jenom statistické|  Statistické|

## <a name="transliteration"></a>Transkripce

Metoda Transliterate podporuje následující jazyky. V "do a z" "<> –" označuje, že jazyk může být přepisem z nebo některou z uvedených skriptů. "-->" Označuje, že jazyk můžete pouze být přepisem z jednoho skriptu do jiné.

| Jazyk    | Kód jazyka | Skript | Do/z | Skript|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| arabština | `ar` | Arabština `Arab` | <--> | Latinská `Latn` |
|Bengálština  | `bn` | Bengálština `Beng` | <--> | Latinská `Latn` |
| Čínština (zjednodušená) | `zh-Hans` | Zjednodušená čínština `Hans`| <--> | Latinská `Latn` |
| Čínština (zjednodušená) | `zh-Hans` | Zjednodušená čínština `Hans`| <--> | Tradiční čínština `Hant`|
| Čínština (tradiční) | `zh-Hant` | Tradiční čínština `Hant`| <--> | Latinská `Latn` |
| Čínština (tradiční) | `zh-Hant` | Tradiční čínština `Hant`| <--> | Zjednodušená čínština `Hans` |
| Gudžarátština | `gu`  | Gujarati `Gujr` | --> | Latinská `Latn` |
| Hebrejština | `he` | Hebrejština `Hebr` | <--> | Latinská `Latn` |
| Hindština | `hi` | Devanágarí `Deva` | <--> | Latinská `Latn` |
| Japonština | `ja` | Japonština `Jpan` | <--> | Latinská `Latn` |
| Kannadština | `kn` | Kannada `Knda` | --> | Latinská `Latn` |
| Malajálamština | `ml` | Malajalámština `Mlym` | --> | Latinská `Latn` |
| Maráthština | `mr` | Devanágarí `Deva` | --> | Latinská `Latn` |
| Oriya | `or` | Oriya `Orya` | <--> | Latinská `Latn` |
| Paňdžábština | `pa` | Gurmukhi `Guru`  | <--> | Latinská `Latn`  |
| Srbština (cyrilice) | `sr-Cyrl` | Cyrillic `Cyrl`  | --> | Latinská `Latn` |
| Srbština (latinka) | `sr-Latn` | Latinská `Latn` | --> | Cyrillic `Cyrl`|
| Tamilština | `ta` | Tamilština `Taml` | --> | Latinská `Latn` |
| Telugština | `te` | Telugu `Telu` | --> | Latinská `Latn` |
| Thajština | `th` | Thajština `Thai` | <--> | Latinská `Latn` |

## <a name="dictionary"></a>Slovník

Slovník podporuje tyto jazyky do nebo z anglické využitím metod Lookup a Examples.

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
| Portugalština      | `pt`          |
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

## <a name="detect"></a>Detect

Translator Text API zjistí všechny jazyky, které jsou k dispozici pro překlad a přepis.


## <a name="access-the-translator-text-api-language-list-programmatically"></a>Programový přístup k seznamu jazyků rozhraní Translator Text API

Můžete načíst seznam podporovaných jazyků pro rozhraní Translator Text API v3.0 pomocí metody jazyky. Zobrazí se seznam funkcí, kód jazyka, stejně jako název jazyka v angličtině nebo libovolného podporovaného jazyka. Tento seznam se aktualizuje automaticky pomocí služby Microsoft Translator, jako jsou k dispozici. nové jazyky.

[Zobrazit jazyky operace referenční dokumentaci](reference/v3-0-languages.md)

## <a name="customization"></a>Přizpůsobení

Tyto jazyky jsou k dispozici pro přizpůsobení do nebo z anglické pomocí [vlastní Translator](https://aka.ms/CustomTranslator).

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
| italština      | `it`          |
| Japonština      | `ja`          |
|Svahilština| `sw`    |
| Korejština      | `ko`          |
| Lotyština      | `lv`          |
| Litevština      | `lt`          |
|Malgašský|  `mg`    |
| norština      | `nb`          |
| polština      | `pl`          |
| Portugalština      | `pt`          |
| Rumunština      | `ro`          |
| ruština      | `ru`          |
|Samoan|    `sm`    |
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

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Přístup k seznamu na webu Microsoft Translatoru

Pro rychlý přehled jazyků na webu Microsoft Translatoru zobrazí všechny jazyky podporované Translator Text API a rozhraní API pro rozpoznávání řeči. Tento seznam nezahrnuje informace specifické pro vývojáře, jako je jazyk kódy.

[Zobrazit seznam jazyků](https://www.microsoft.com/translator/languages.aspx)
