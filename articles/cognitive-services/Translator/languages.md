---
title: Podporované jazyky v rozhraní API služby Microsoft překladač | Microsoft Docs
description: Zobrazení jazyků – podpora rozhraní API služby Microsoft překladač Text.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 10/30/2017
ms.author: v-jansko
ms.openlocfilehash: f388e9e39809774f307c0d1752e29f34b041ed13
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35346005"
---
# <a name="supported-languages-in-the-microsoft-translator-text-api"></a>Podporované jazyky v rozhraní API služby Microsoft překladač textu 
Rozhraní API služby Microsoft překladač Text podporuje následující jazyky pro překlad text na text. Neuronové strojový překlad (NMT) je novým standardem pro vysoce kvalitní AI zapnutý počítač překlady a je k dispozici jako výchozí používá V3 rozhraní API Text překladač neuronové systému je k dispozici. Neuronové strojový překlad, je k dispozici v V2 pomocí kategorie "generalnn". 

[Další informace o tom, jak funguje strojový překlad](https://www.microsoft.com/translator/mt.aspx)

| Jazyk    | Typ posunutí |Kód jazyka |
|:----------- |:-------:|:-------------:|
| Afrikánština      | Statistické |`af`          |
| arabština      | Neuronové | `ar`          |
| Bengálština      | Neuronové |`bn`          |
| Bosenština (latinka)      | Statistické |`bs`          |
| Bulharština     |  Neuronové |`bg`          |
| Kantonština (tradiční)      | Statistické |`yue`          |
| Katalánština      | Statistické |`ca`          |
| Zjednodušená čínština        |  Neuronové |`zh-Hans`          | 
| Tradiční čínština        |  Neuronové |`zh-Hant`          |
| Chorvatština      | Neuronové |`hr`          |
| Čeština        |  Neuronové |`cs`          |
| dánština        |  Neuronové |`da`          |
| Holandština        |  Neuronové |`nl`          |
| Angličtina       |  Neuronové |`en`          |
| Estonština      | Neuronové |`et`          |
| Vládní      | Statistické |`fj`          |
| Filipínština      | Statistické |`fil`          |
| Finština      | Neuronové |`fi`          |
| Francouzština        |  Neuronové |`fr`          |
| Němčina       |  Neuronové |`de`          |
| Řečtina      | Neuronové |`el`          |
| Haitská kreolština      | Statistické |`ht`          |
| Hebrejština      | Neuronové |`he`          |
| Hindština        |  Neuronové |`hi`          |
| Hmong Daw      | Statistické |`mww`          |
| Maďarština      | Neuronové |`hu`          |
| Islandština      |  Neuronové |`is`           |
| Indonéština      | Statistické |`id`          |
| italština        |  Neuronové |`it`          |
| Japonština        |  Neuronové |`ja`          |
| Svahilština      | Statistické |`sw`          |
| Klingon      | Statistické |`tlh`          |
| Klingon (plqaD)      | Statistické |`tlh-Qaak`          |
| Korejština        |  Neuronové |`ko`          |
| Lotyština      | Neuronové |`lv`          |
| Litevština      | Neuronové |`lt`          |
| Madagaskarskému      | Statistické |`mg`          |
| Malajština      | Statistické |`ms`          |
| Maltština      | Statistické |`mt`          |
| norština        |  Neuronové |`nb`          |
| Perština      | Statistické |`fa`          |
| polština        |  Neuronové |`pl`          |
| Portugalština        |  Neuronové |`pt`          |
| Queretaro Otomi      | Statistické |`otq`          |
| Rumunština        |  Neuronové |`ro`          |
| ruština        |  Neuronové |`ru`          |
| Samoan      | Statistické |`sm`          |
| Srbština (cyrilice)      | Statistické |`sr-Cyrl`          |
| Srbština (latinka)      | Statistické |`sr-Latn`          |
| Slovenština     | Neuronové |`sk`          |
| Slovinština      | Neuronové |`sl`          |
| Španělština        |  Neuronové |`es`          |
| švédština        |  Neuronové |`sv`          |
| Tahitian      | Statistické |`ty`          |
| Tamilština      | Statistické |`ta`          |
| Thajština      | Neuronové |`th`          |
| Tongan      | Statistické |`to`          |
| turečtina       |  Neuronové |`tr`          |
| Ukrajinština      | Neuronové |`uk`          |
| Urdština      | Statistické |`ur`          |
| Vietnamština      | Neuronové |`vi`          |
| Velština      | Neuronové |`cy`          |
| Yucatec Maya      | Statistické |`yua`          |

## <a name="transliteration"></a>Přepis

Metoda Transliterate podporuje následující jazyky. V "do/z" "<>--" znamená, že jazyk může být přepisem z nebo na jednu z uvedených skriptů. "-->" Označuje, že jazyk můžete pouze údaj ze skriptu jeden na druhý.

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
| Hindština | Ahoj | Devanagari | <--> | Latinská |
| Japonština | ja | Japonština | <--> | Latinská |
| Kannadština | KN | Kannadština | --> | Latinská |
| Malasian | ml | Malajálamština | --> | Latinská |
| Maráthština | MR | Devanagari | --> | Latinská |
| urijština | nebo | urijština | <--> | Latinská |
| Paňdžábština | pa | Gurmukhi | <--> | Latinská  |
| Srbština (cyrilice) | SR-Cyrl | Cyrilici  | --> | Latinská |
| Srbština (latinka) | SR-Latn | Latinská | --> | Cyrilici |
| Tamilština | tových | Tamilština | --> | Latinská |
| Telugština | te | Telugština | --> | Latinská |
| Thajština | . | Thajština | <--> | Latinská |

## <a name="dictionary"></a>Slovník

Adresář podporuje tyto jazyky do nebo z angličtina pomocí metody vyhledávání a příklady. 

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

## <a name="languages-detected-by-the-detect-method"></a>Jazyky zjistil metodou rozpoznat

Následující jazyky lze zjistit metodou rozpoznat. Zjišťování může zjišťovat jazyky, které Microsoft Translator nelze přeložit. 

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
| Chinese_Simplified |
| Chinese_Traditional |
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
| Haitian_Creole |
| Hebrejština |
| Hindština |
| Maďarština |
| Islandština |
| Indonéština |
| Irština |
| italština |
| Japonština |
| Korejština |
| Kurdish_Arabic |
| Kurdish_Latin |
| Latinská |
| Lotyština |
| Litevština |
| Makedonština |
| Malajština |
| Maltština |
| norština |
| Norwegian_Nynorsk |
| Paštština |
| Perština |
| polština |
| Portugalština |
| Rumunština |
| ruština |
| Serbian_Cyrillic |
| Serbian_Latin |
| Slovenština |
| Slovinština |
| Somálština |
| Španělština |
| svahilština |
| švédština |
| Tagalogština |
| Thajština |
| turečtina |
| Ukrajinština |
| Urdština |
| Uzbek_Cyrillic |
| Uzbek_Latin |
| Vietnamština |
| Velština |
| Jidiš |

## <a name="access-the-list-programmatically"></a>Přístup k seznamu prostřednictvím kódu programu

Seznam podporovaných jazyků, programově pomocí jazyků operace rozhraní API Text V3.0 můžete přistupovat. V angličtině nebo jiné podporované jazykové můžete zobrazit v seznamu funkcí, kód jazyka, jakož i název jazyka. Tento seznam je automaticky aktualizovat pomocí služby Microsoft Translator dostupná nové jazyky.

[Zobrazit jazyky operaci referenční dokumentaci k nástroji](/reference/languages.md)

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Přístup k seznamu na webu Microsoft Translator

Pro rychlý přehled jazyky na webu Microsoft Translator zobrazí všech jazyků podporovaných překladač textu a rozhraní API pro rozpoznávání řeči. Tento seznam neobsahuje informace specifické pro vývojáře například kódy jazyků.

[Zobrazit seznam jazyků](https://www.microsoft.com/translator/languages.aspx)
