---
title: Podporované jazyky – Translator Speech API
titlesuffix: Azure Cognitive Services
description: Zobrazit jazyky podporovanými rozhraní Translator Speech API.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: conceptual
ms.date: 3/5/2018
ms.author: v-jansko
ms.openlocfilehash: 482968af53694f7d40a7077435d33a83884c8808
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55873038"
---
# <a name="languages-supported-by-the-translator-speech-api"></a>Jazyky podporované rozhraní Translator Speech API

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Pro překlad řeči se podporují tyto jazyky. Pokud oba jazyky jsou podporovány pro překlad řeči, převod řeči na řeč a převod řeči na text je k dispozici. Pokud cílový jazyk není podporován pro překlad řeči, je k dispozici pouze převod řeči na text překladu. 

| Rozpoznávání řeči, jazyka    |
|:----------- |
| Arabština (moderní Standard)      |
| Čínština (Mandarínština)      |
| Angličtina      |
| Francouzština      |
| Němčina      |
| italština      |
| Japonština      |
| Portugalština (Brazílie)     |
| ruština      |
| Španělština      | 

Translator Speech API podporuje následující jazyky jako cílový jazyk pro převod řeči na text překladu. 

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
|Islandština|`is`          |
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

## <a name="access-the-list-programmatically"></a>Programový přístup k seznamu

Seznam podporovaných jazyků programově pomocí jazyků prostředku můžete přistupovat. V seznamu jsou uvedeny kód jazyka, jakož i název jazyka v angličtině nebo libovolného podporovaného jazyka. Tento seznam se aktualizuje automaticky službou Translator Speech, jakmile budou k dispozici. nové jazyky.

Prostředek jazyky vrátí seznam podporovaných jazyků pro řeč, text a převod textu na řeč. Jazyky prostředků nevyžaduje ověření.

[Navštivte referenční rozhraní API můžete vyzkoušet na jazyky – metoda](languages-reference.md)

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Přístup k seznamu na webu Microsoft Translatoru

Pro rychlý přehled jazyků na webu Microsoft Translatoru zobrazí všechny jazyky podporované Translator Text API a rozhraní API pro rozpoznávání řeči. Tento seznam nezahrnuje informace specifické pro vývojáře, jako je jazyk kódy.

[Zobrazit seznam jazyků](https://www.microsoft.com/translator/languages.aspx) 
