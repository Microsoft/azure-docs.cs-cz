---
title: Azure Media Player lokalizace
description: Podpora více jazyků pro uživatele jiných než anglické národní prostředí.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/05/2021
ms.openlocfilehash: e78ff237fb488a995d9161814fe61590fb1c6d5b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449832"
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
