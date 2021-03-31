---
title: Azure Media Player lokalizace
description: Podpora více jazyků pro uživatele jiných než anglické národní prostředí.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: 8a459d1fba4353b4b3e092e83a759314cc455ead
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87087284"
---
# <a name="azure-media-player-localization"></a>Azure Media Player lokalizace #

Podpora více jazyků umožňuje uživatelům jiných než anglické národní prostředí nativně interagovat s přehrávačem. Azure Media Player vytvoří instanci s globálním slovníkem jazyků, který bude lokalizovat chybové zprávy na základě jazyka stránky. Vývojář může také vytvořit instanci přehrávače s vynuceným nastavením jazyka. Výchozí jazyk je angličtina (EN).

> [!NOTE]
> Tato funkce stále prochází testováním a v takovém případě může být v souladu s chybami.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

Azure Media Player aktuálně podporuje následující jazyky s odpovídajícími jazykovými kódy:

| Jazyk            | Kód | Jazyk                | Kód   | Jazyk                | Kód         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| Angličtina {výchozí}   | en   | Chorvatština                | hod     | Rumunština                | loď           |
| Arabština              | snížen   | Maďarština               | hu     | Slovenština                  | SM           |
| Bulharština           | BG   | Indonéština              | id     | Slovene                 | SSL           |
| Katalánština             | určen   | Islandština               | is     | Srbština – cyrilice      | sr-cyrl-cs   |
| Čeština               | cs   | Italština                 | její     | Srbština – latinka         | sr-latn-rs   |
| Dánština              | &   | Japonština                | dža     | Ruština                 | ru           |
| Němčina              | &   | Kazaština                  | kk     | Švédština                 | činí           |
| Řečtina               | El   | Korejština                  | Ko     | Thajština                    | Kolik           |
| španělština             | es   | Litevština              | lt     | Římské                 | TL           |
| Estonština            | et   | Lotyština                 | Lotyšsko     | Turečtina                 | recenzent           |
| Baskičtina              | Balijšitna   | Malajsii               | Arial     | Ukrajinština               | Velká Británie           |
| Fársíjština               | položek   | Norština – BokmÃa y l     | NB     | Urdština                    | poèítaè           |
| Finština             | WiFi   | Nizozemština                   | belgick     | Vietnamština              | InterDev           |
| Francouzština              | FR   | Norština – Nynorsk     | nn     | Čínština (zjednodušená)    | zh – Hans      |
| Galicijština            | GL   | Polština                  | pl     | Čínština (tradiční)   | zh – Hant      |
| Hebrejština              | uvede   | Portugalština – Brazílie     | pt-br  |                         |              |
| Hindština               | Ahoj   | Portugalština – Portugalsko   | pt-pt  |                         |              |


> [!NOTE]
> Pokud nechcete, aby došlo k lokalizaci, musíte vynutit jazyk pro angličtinu.

## <a name="next-steps"></a>Další kroky ##

- [Rychlý Start Azure Media Player](azure-media-player-quickstart.md)
