---
title: Podpora jazyků – Translator Text API
titleSuffix: Azure Cognitive Services
description: Seznam podporovaných rozhraní Translator Text API přirozeného jazyka.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: article
ms.date: 09/25/2018
ms.author: v-jansko
ms.openlocfilehash: 96f73d7d1324de9e5531aefcc4ee3b9a054cf941
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228044"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Podpora jazyka a oblasti pro rozhraní Translator Text API

Translator Text API podporuje následující jazyky pro překlad textu do textu. Neurální strojový překlad sítí (NMT) je novým standardem pro strojový překlad s využitím AI vysoce kvalitní a je k dispozici jako výchozí používá Translator Text API V3 neuronových sítí systému je k dispozici. 

[Další informace o tom, jak funguje strojový překlad](https://www.microsoft.com/translator/mt.aspx)

**V2 Translator API**

> [!NOTE]
> V2 se přestala nabízet 30. dubna 2018 a bude ukončena 30. dubna 2019.

* Jenom statistické: Žádné neuronových sítí systému je k dispozici pro tento jazyk.
* Neuronových sítí k dispozici: Neurální systém k dispozici. Použijte parametr `category=generalnn` pro přístup k systému neuronových sítí.
* Neurální výchozí nastavení: Neurální je výchozí překladový systém. Použijte parametr `category=smt` pro přístup k statistické lokality pro použití v centru Microsoft Translator.
* Neurální pouze: Je k dispozici pouze Neurální překlady.

**V3 Translator API** V3 Translator API je ve výchozím nastavení neuronových sítí a statistické systémy jsou k dispozici, pouze pokud neexistuje žádný systém neuronových sítí. Vlastní Translator jde použít jenom s jazyky neuronových sítí. 

|Jazyk|  Kód jazyka|  V2 API| V3 API|
|:-----|:-----:|:-----|:-----|
|Afrikánština| `af`    |Jenom statistické|  Neuronových sítí|
|arabština|    `ar`    |Neuronových sítí k dispozici|  Neuronových sítí|
|Bengálština|    `bn`    |Neuronových sítí k dispozici|  Neuronových sítí|
|Bosenština (latinka)|   `bs`    |Neuronových sítí k dispozici|  Neuronových sítí|
|Bulharština| `bg`    |Neuronových sítí k dispozici|  Neuronových sítí|
|Kantonština (tradiční)|   `yue`   |Jenom statistické|  Statistické|
|Katalánština|   `ca`    |Jenom statistické|  Statistické|
|čínština (zjednodušená)|    `zh-Hans`   |Výchozí neuronových sítí |Neuronových sítí|
|Tradiční čínština|   `zh-Hant`   |Výchozí neuronových sítí |Neuronových sítí|
|Chorvatština|  `hr`    |Neuronových sítí k dispozici|  Neuronových sítí|
|Čeština| `cs`    |Neuronových sítí k dispozici|  Neuronových sítí|
|dánština|    `da`    |Neuronových sítí k dispozici   |Neuronových sítí|
|Holandština| `nl`    |Neuronových sítí k dispozici|  Neuronových sítí|
|Angličtina|   `en`    |Neuronových sítí k dispozici|  Neuronových sítí|
|Estonština|  `et`    |Neuronových sítí k dispozici|  Neuronových sítí|
|Vládní|    `fj`    |Jenom statistické|  Statistické|
|Filipínština|  `fil`   |Jenom statistické|  Statistické|
|Finština|   `fi`    |Neuronových sítí k dispozici|  Neuronových sítí|
|Francouzština|    `fr`    |Neuronových sítí k dispozici|  Neuronových sítí|
|Němčina|    `de`    |Neuronových sítí k dispozici|  Neuronových sítí|
|Řečtina| `el`    |Neuronových sítí k dispozici|  Neuronových sítí|
|Haitská kreolština|    `ht`    |Jenom statistické   |Statistické|
|Hebrejština |`he`   |Neuronových sítí k dispozici   |Neuronových sítí|
|Hindština| `hi`    |Výchozí neuronových sítí|    Neuronových sítí|
|Hmong Daw| `mww`   |Jenom statistické|  Statistické|
|Maďarština| `hu`    |Neuronových sítí k dispozici|  Neuronových sítí|
|Islandština| `is`    |Pouze neuronových sítí|   Neuronových sítí|
|Indonéština|    `id`    |Jenom statistické|  Statistické|
|italština|   `it`    |Neuronových sítí k dispozici|  Neuronových sítí|
|Japonština|  `ja`    |Neuronových sítí k dispozici|  Neuronových sítí|
|Svahilština| `sw`    |Jenom statistické|  Statistické|
|Klingon|   `tlh`   |Jenom statistické|  Statistické|
|Klingon (plqaD)|   `tlh-Qaak`  |Jenom statistické|  Statistické|
|Korejština |`ko`   |Neuronových sítí k dispozici|  Neuronových sítí|
|Lotyština|   `lv`    |Neuronových sítí k dispozici|  Neuronových sítí|
|Litevština|    `lt`    |Neuronových sítí k dispozici|  Neuronových sítí|
|Malgašský|  `mg`    |Jenom statistické|  Statistické|
|Malajština| `ms`    |Jenom statistické   |Statistické|
|Maltština|   `mt`    |Jenom statistické|  Statistické|
|norština| `nb`    |Neuronových sítí k dispozici|  Neuronových sítí|
|Perština|   `fa`    |Jenom statistické|  Statistické|
|polština|    `pl`    |Neuronových sítí k dispozici|  Neuronových sítí|
|Portugalština|    `pt`    |Neuronových sítí k dispozici|  Neuronových sítí|
|Queretaro Otomi|   `otq`   |Jenom statistické|  Statistické|
|Rumunština|  `ro`    |Neuronových sítí k dispozici|  Neuronových sítí|
|ruština|   `ru`    |Neuronových sítí k dispozici|  Neuronových sítí|
|Samoan|    `sm`    |Jenom statistické|  Statistické|
|Srbština (cyrilice)|    `sr-Cyrl`   |Jenom statistické|  Statistické|
|Srbština (latinka)|   `sr-Latn`   |Jenom statistické   |Statistické|
|Slovenština|    `sk`    |Neuronových sítí k dispozici|  Neuronových sítí|
|Slovinština| `sl`    |Neuronových sítí k dispozici|  Neuronových sítí|
|Španělština|   `es`    |Neuronových sítí k dispozici|  Neuronových sítí|
|švédština|   `sv`    |Neuronových sítí k dispozici   |Neuronových sítí|
|Tahitian|  `ty`    |Jenom statistické|  Statistické|
|Tamilština| `ta`    |Jenom statistické|  Statistické|
|Telugština|    `te`    |Pouze neuronových sítí|   Neuronových sítí|
|Thajština|  `th`    |Neuronových sítí k dispozici|  Neuronových sítí|
|Tonžská|    `to`    |Jenom statistické|  Statistické|
|turečtina|   `tr`    |Neuronových sítí k dispozici   |Neuronových sítí|
|Ukrajinština| `uk`    |Neuronových sítí k dispozici|  Neuronových sítí|
|Urdština|  `ur`    |Jenom statistické|  Statistické|
|Vietnamština|    `vi`    |Neuronových sítí k dispozici|  Neuronových sítí|
|Velština| `cy`    |Neuronových sítí k dispozici|  Neuronových sítí|
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
| urijština | `or` | Oriya `Orya` | <--> | Latinská `Latn` |
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
| čínština (zjednodušená)      | `zh-Hans`          |
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

## <a name="detect"></a>Zjišťování

Metoda rozpoznat podporuje následující jazyky. Zjištění může identifikovat, jazyků, které Microsoft Translator: nelze převést.

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

## <a name="access-the-translator-text-api-language-list-programmatically"></a>Programový přístup k seznamu jazyků rozhraní Translator Text API

Můžete načíst seznam podporovaných jazyků pro rozhraní Translator Text API v3.0 pomocí metody jazyky. Zobrazí se seznam funkcí, kód jazyka, stejně jako název jazyka v angličtině nebo libovolného podporovaného jazyka. Tento seznam se aktualizuje automaticky pomocí služby Microsoft Translator, jako jsou k dispozici. nové jazyky.

[Zobrazit jazyky operace referenční dokumentaci](reference/v3-0-languages.md)

## <a name="customization"></a>Přizpůsobení

Tyto jazyky jsou dostupné k použití vlastního nastavení [vlastní Translator](http://aka.ms/CustomTranslator).

| Jazyk    | Kód jazyka |
|:----------- |:-------------:|
| arabština       | `ar`          |
| Bengálština      | `bn`          |
| Bosenština (latinka)      | `bs`          |
| Bulharština      | `bg`          |
| čínština (zjednodušená)      | `zh-Hans`          |
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
| italština      | `it`          |
| Japonština      | `ja`          |
| Korejština      | `ko`          |
| Lotyština      | `lv`          |
| Litevština      | `lt`          |
| norština      | `nb`          |
| polština      | `pl`          |
| Portugalština      | `pt`          |
| Rumunština      | `ro`          |
| ruština      | `ru`          |
| Srbština (latinka)      | `sr-Latn`          |
| Slovenština     | `sk`          |
| Slovinština      | `sl`          |
| Španělština      | `es`          |
| švédština      | `sv`          |
| Thajština      | `th`          |
| turečtina      | `tr`          |
| Ukrajinština      | `uk`          |
| Vietnamština      | `vi`          |

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Přístup k seznamu na webu Microsoft Translatoru

Pro rychlý přehled jazyků na webu Microsoft Translatoru zobrazí všechny jazyky podporované Translator Text API a rozhraní API pro rozpoznávání řeči. Tento seznam nezahrnuje informace specifické pro vývojáře, jako je jazyk kódy.

[Zobrazit seznam jazyků](https://www.microsoft.com/translator/languages.aspx)
