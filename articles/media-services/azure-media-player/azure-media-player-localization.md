---
title: Azure Media Player lokalizace
description: Podpora více jazyků pro uživatele jiných než anglické národní prostředí.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: ca4dc888af414ede270118eff72652f098d3306c
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82779038"
---
# <a name="localization"></a>Lokalizace #

Podpora více jazyků umožňuje uživatelům jiných než anglické národní prostředí nativně interagovat s přehrávačem. Azure Media Player vytvoří instanci s globálním slovníkem jazyků, který bude lokalizovat chybové zprávy na základě jazyka stránky. Vývojář může také vytvořit instanci přehrávače s vynuceným nastavením jazyka. Výchozí jazyk je angličtina (EN).

> [!NOTE]
> Tato funkce stále prochází testováním a v takovém případě může být v souladu s chybami.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

Azure Media Player aktuálně podporuje následující jazyky s odpovídajícími jazykovými kódy:

| Jazyk            | kód | Jazyk                | kód   | Jazyk                | kód         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| Angličtina {výchozí}   | en   | Chorvatština                | hr     | Rumunština                | loď           |
| Arabština              | snížen   | Maďarština               | hu     | Slovenština                  | SM           |
| Bulharština           | BG   | Indonéština              | id     | Slovene                 | SSL           |
| Katalánština             | určen   | Islandština               | is     | Srbština – cyrilice      | sr-cyrl-cs   |
| Čeština               | cs   | Italština                 | její     | Srbština – latinka         | sr-latn-rs   |
| Dánština              | &   | Japonština                | dža     | Ruština                 | ru           |
| Němčina              | &   | Kazaština                  | kk     | Švédština                 | činí           |
| Řečtina               | El   | Korejština                  | Ko     | Thajština                    | Kolik           |
| Španělština             | Ano   | Litevština              | lt     | Římské                 | TL           |
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
