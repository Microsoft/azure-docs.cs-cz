---
title: Podpora jazyků – Translator Text API
titleSuffix: Azure Cognitive Services
description: Seznam podporovaných rozhraní Translator Text API přirozeného jazyka.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 09/25/2018
ms.author: v-jansko
ms.openlocfilehash: 775e098eb2a067e3e0446bccc223c1c54e082347
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435448"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Podpora jazyka a oblasti pro rozhraní Translator Text API

Translator Text API podporuje následující jazyky pro překlad textu do textu. Neurální strojový překlad sítí (NMT) je novým standardem pro strojový překlad s využitím AI vysoce kvalitní a je k dispozici jako výchozí používá Translator Text API V3 neuronových sítí systému je k dispozici. Neurální strojový překlad je k dispozici ve verzi V2 prostřednictvím kategorie "generalnn".

[Další informace o tom, jak funguje strojový překlad](https://www.microsoft.com/translator/mt.aspx)

| Jazyk    | Typ posunutí |Kód jazyka |
|:----------- |:-------:|:-------------:|
| Afrikánština      | Statistické |`af`          |
| arabština      | Neuronových sítí | `ar`          |
| Arabština, Levantine    | Neuronových sítí | `apc`
| Bengálština      | Neuronových sítí |`bn`          |
| Bosenština (latinka)      | Statistické |`bs`          |
| Bulharština     |  Neuronových sítí |`bg`          |
| Kantonština (tradiční)      | Statistické |`yue`          |
| Katalánština      | Statistické |`ca`          |
| Zjednodušená čínština        |  Neuronových sítí |`zh-Hans`          |
| Tradiční čínština        |  Neuronových sítí |`zh-Hant`          |
| Chorvatština      | Neuronových sítí |`hr`          |
| Čeština        |  Neuronových sítí |`cs`          |
| dánština        |  Neuronových sítí |`da`          |
| Holandština        |  Neuronových sítí |`nl`          |
| Angličtina       |  Neuronových sítí |`en`          |
| Estonština      | Neuronových sítí |`et`          |
| Vládní      | Statistické |`fj`          |
| Filipínština      | Statistické |`fil`          |
| Finština      | Neuronových sítí |`fi`          |
| Francouzština        |  Neuronových sítí |`fr`          |
| Němčina       |  Neuronových sítí |`de`          |
| Řečtina      | Neuronových sítí |`el`          |
| Haitská kreolština      | Statistické |`ht`          |
| Hebrejština      | Neuronových sítí |`he`          |
| Hindština        |  Neuronových sítí |`hi`          |
| Hmong Daw      | Statistické |`mww`          |
| Maďarština      | Neuronových sítí |`hu`          |
| Islandština      |  Neuronových sítí |`is`           |
| Indonéština      | Statistické |`id`          |
| italština        |  Neuronových sítí |`it`          |
| Japonština        |  Neuronových sítí |`ja`          |
| Svahilština      | Statistické |`sw`          |
| Klingon      | Statistické |`tlh`          |
| Klingon (plqaD)      | Statistické |`tlh-Qaak`          |
| Korejština        |  Neuronových sítí |`ko`          |
| Lotyština      | Neuronových sítí |`lv`          |
| Litevština      | Neuronových sítí |`lt`          |
| Malgašský      | Statistické |`mg`          |
| Malajština      | Statistické |`ms`          |
| Maltština      | Statistické |`mt`          |
| norština        |  Neuronových sítí |`nb`          |
| Perština      | Statistické |`fa`          |
| polština        |  Neuronových sítí |`pl`          |
| Portugalština        |  Neuronových sítí |`pt`          |
| Queretaro Otomi      | Statistické |`otq`          |
| Rumunština        |  Neuronových sítí |`ro`          |
| ruština        |  Neuronových sítí |`ru`          |
| Samoan      | Statistické |`sm`          |
| Srbština (cyrilice)      | Statistické |`sr-Cyrl`          |
| Srbština (latinka)      | Statistické |`sr-Latn`          |
| Slovenština     | Neuronových sítí |`sk`          |
| Slovinština      | Neuronových sítí |`sl`          |
| Španělština        |  Neuronových sítí |`es`          |
| švédština        |  Neuronových sítí |`sv`          |
| Tahitian      | Statistické |`ty`          |
| Tamilština      | Statistické |`ta`          |
| Telugština   | Neuronových sítí   | `te` |
| Thajština      | Neuronových sítí |`th`          |
| Tonžská      | Statistické |`to`          |
| turečtina       |  Neuronových sítí |`tr`          |
| Ukrajinština      | Neuronových sítí |`uk`          |
| Urdština      | Statistické |`ur`          |
| Vietnamština      | Neuronových sítí |`vi`          |
| Velština      | Neuronových sítí |`cy`          |
| Yucatec Maya      | Statistické |`yua`          |

## <a name="transliteration"></a>Transkripce

Metoda Transliterate podporuje následující jazyky. V "do a z" "<> –" označuje, že jazyk může být přepisem z nebo některou z uvedených skriptů. "-->" Označuje, že jazyk můžete pouze být přepisem z jednoho skriptu do jiné.

| Jazyk    | Kód jazyka | Skript | Do/z | Skript|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| arabština | ar | arabština | <--> | Latinská |
|Bengálština  | Bn | bengálština | <--> | Latinská |
| Čínština (zjednodušená) | zh-Hans | Zjednodušená čínština | <--> | Latinská |
| Čínština (zjednodušená) | zh-Hans | Zjednodušená čínština | <--> | Tradiční čínština |
| Čínština (tradiční) | zh-Hant | Tradiční čínština | <--> | Latinská |
| Čínština (tradiční) | zh-Hant | Tradiční čínština | <--> | Zjednodušená čínština |
| Gudžarátština | gu  | Gudžarátština | --> | Latinská |
| Hebrejština | mu | Hebrejština | <--> | Latinská |
| Hindština | Ahoj | Devanágarí | <--> | Latinská |
| Japonština | ja | Japonština | <--> | Latinská |
| Kannadština | KN | Kannadština | --> | Latinská |
| Malasian | ml | Malajálamština | --> | Latinská |
| Maráthština | MR | Devanágarí | --> | Latinská |
| urijština | nebo | urijština | <--> | Latinská |
| Paňdžábština | pa | Gurmukhi | <--> | Latinská  |
| Srbština (cyrilice) | rozhraní SR-Cyrl | Cyrilice  | --> | Latinská |
| Srbština (latinka) | rozhraní SR-Latn | Latinská | --> | Cyrilice |
| Tamilština | ta | Tamilština | --> | Latinská |
| Telugština | te | Telugština | --> | Latinská |
| Thajština | . | Thajština | <--> | Latinská |

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

## <a name="languages-detected-by-the-detect-method"></a>Jazyky detekoval rozpoznat – metoda

Tyto jazyky lze zjistit pomocí metody detekce. Zjišťování může zjišťovat jazyky, které Microsoft Translator: nelze převést.

| Jazyk    |
|:----------- |
| Afrikánština |
| Albánština |
| arabština |
| Baskičtina |
| Běloruština |
| Bulharština |
| Katalánština |
| Čínština |
| Čínština (zjednodušená) |
| Čínština (tradiční) |
| Chorvatština |
| Čeština |
| dánština |
| Holandština |
| Angličtina |
| Esperanto |
| Estonština |
| Finština |
| Francouzština |
| Galicijština |
| Němčina |
| Řečtina |
| Haitská kreolština |
| Hebrejština |
| Hindština |
| Maďarština |
| Islandština |
| Indonéština |
| Irština |
| italština |
| Japonština |
| Korejština |
| Kurdština (Arabské písmo) |
| Kurdština (latinka) |
| Latinská |
| Lotyština |
| Litevština |
| Makedonština |
| Malajština |
| Maltština |
| norština |
| Norština (Nynorsk) |
| Paštština |
| Perština |
| polština |
| Portugalština |
| Rumunština |
| ruština |
| Srbština (cyrilice) |
| Srbština (latinka) |
| Slovenština |
| Slovinština |
| Somálština |
| Španělština |
| svahilština |
| švédština |
| Tagalogština |
| Telugština |
| Thajština |
| turečtina |
| Ukrajinština |
| Urdština |
| Uzbečtina (cyrilice) |
| Uzbečtina (latinka) |
| Vietnamština |
| Velština |
| Jidiš |

## <a name="access-the-list-programmatically"></a>Programový přístup k seznamu

Seznam podporovaných jazyků prostřednictvím kódu programu pomocí operace jazyků rozhraní Text API verze 3.0 můžete přistupovat. Zobrazí se seznam funkcí, kód jazyka, stejně jako název jazyka v angličtině nebo libovolného podporovaného jazyka. Tento seznam se aktualizuje automaticky pomocí služby Microsoft Translator, jakmile budou k dispozici. nové jazyky.

[Zobrazit jazyky operace referenční dokumentaci](reference/v3-0-languages.md)

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Přístup k seznamu na webu Microsoft Translatoru

Pro rychlý přehled jazyků na webu Microsoft Translatoru zobrazí všechny jazyky podporované Translator Text API a rozhraní API pro rozpoznávání řeči. Tento seznam nezahrnuje informace specifické pro vývojáře, jako je jazyk kódy.

[Zobrazit seznam jazyků](https://www.microsoft.com/translator/languages.aspx)
