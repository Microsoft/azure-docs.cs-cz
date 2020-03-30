---
title: azcopy pracovních míst show | Dokumenty společnosti Microsoft
description: Tento článek obsahuje referenční informace pro příkaz azcopy jobs show.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7b5f566757dd77a61f252b123d0c9c1b74303fbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034125"
---
# <a name="azcopy-jobs-show"></a>azcopy jobs show

Zobrazuje podrobné informace o ID dané úlohy.

## <a name="synopsis"></a>Synopse

Pokud je zadáno pouze ID úlohy bez příznaku, je vrácen souhrn průběhu úlohy.

Počet bajtů a procento dokončení, které se zobrazí při spuštění tohoto příkazu, odráží pouze soubory, které jsou dokončeny v úloze. Neodrážejí částečně dokončené soubory.

Pokud `with-status` je příznak nastaven, zobrazí se seznam převodů v úloze s danou hodnotou.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Související koncepční články

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)
- [Přenos dat pomocí úložiště AzCopy a Blob](storage-use-azcopy-blobs.md)
- [Přenos dat pomocí AzCopy a ukládání souborů](storage-use-azcopy-files.md)
- [Konfigurace, optimalizace a řešení potíží s azcopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Možnosti

|Možnost|Popis|
|--|--|
|-h, --pomoc|Zobrazí obsah nápovědy pro příkaz show.|
|--s-stavový řetězec|Vypsat pouze převody úlohy s tímto stavem, dostupnými hodnotami: Spuštěno, Úspěch, Nepodařilo se|

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

|Možnost|Popis|
|---|---|
|--cap-mbps uint32|Završuje přenosovou rychlost v megabitech za sekundu. Propustnost se může mírně lišit od víčka. Pokud je tato možnost nastavena na nulu nebo je vynechána, propustnost není omezena.|
|--řetězec výstupního typu|Formát výstupu příkazu. Volby zahrnují: text, json. Výchozí hodnota je "text".|

## <a name="see-also"></a>Viz také

- [azcopy jobs](storage-ref-azcopy-jobs.md)
