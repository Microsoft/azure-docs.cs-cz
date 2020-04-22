---
title: Lokalizace přehrávače médií Azure
description: Podpora více jazyků pro uživatele neanglických národních prostředí.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: 34805c4eaae5d969fc2338c24f9f92404e065d15
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727174"
---
# <a name="localization"></a>Lokalizace #

Podpora více jazyků umožňuje uživatelům neanglických národních prostředí nativně komunikovat s přehrávačem. Azure Media Player vytvoří instanci pomocí globálního slovníku jazyků, který bude lokalizovat chybové zprávy na základě jazyka stránky. Vývojář může také vytvořit instanci přehrávače s vynuceným jazykem sady. Výchozím jazykem je angličtina (en).

> [!NOTE]
> Tato funkce stále prochází některými testy a jako taková podléhá chybám.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

Azure Media Player v současné době podporuje následující jazyky s odpovídajícími kódy jazyků:

| Jazyk            | kód | Jazyk                | kód   | Jazyk                | kód         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| Angličtina {výchozí}   | en   | Chorvatština                | hr     | Rumunština                | Ro           |
| Arabština              | ar   | Maďarština               | Hu     | Slovenština                  | Sk           |
| Bulharština           | Bg   | Indonéština              | id     | Slovinštině                 | Sl           |
| Katalánština             | Ca   | Islandština               | is     | Srbština - cyrilice      | sr-cyrl-cs   |
| Čeština               | Cs   | Italština                 | je to     | Srbština - latina         | sr-latn-rs   |
| Dánština              | Da   | Japonština                | ja     | Ruština                 | Ru           |
| Němčina              | De   | Kazaština                  | Kk     | Švédština                 | sv           |
| Řečtina               | El   | Korejština                  | Ko     | Thajština                    | Th           |
| Španělština             | Ano   | Litevština              | lt     | Tagalog                 | Tl           |
| Estonština            | Et   | Lotyština                 | Lv     | Turečtina                 | Tr           |
| Baskičtina              | Eu   | Malajské               | Paní     | Ukrajinština               | Uk           |
| Perština               | Dm   | Norština - BokmÃ¥l     | Nb     | Urdština                    | vaše           |
| Finština             | ﬁ   | Nizozemština                   | Nl     | Vietnamština              | Vi           |
| Francouzština              | Fr   | Norština - Nynorsk     | nn     | Čínština - zjednodušená    | zh-hans      |
| Galicijština            | Gl   | Polština                  | Pl     | Čínština - tradiční   | zh-hant      |
| Hebrejština              | on   | Portugalština – Brazílie     | pt-br  |                         |              |
| Hindština               | Hu   | Portugalština – Portugalsko   | pt-pt  |                         |              |


> [!NOTE]
> Pokud nechcete, aby došlo k nějaké lokalizaci, musíte vynutit jazyk do angličtiny

## <a name="next-steps"></a>Další kroky ##

- [Rychlý start přehrávače médií Azure](azure-media-player-quickstart.md)