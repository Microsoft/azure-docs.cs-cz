---
title: zobrazení úloh AzCopy | Microsoft Docs
description: Tento článek obsahuje referenční informace o příkazu zobrazení úloh AzCopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7b5f566757dd77a61f252b123d0c9c1b74303fbe
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034125"
---
# <a name="azcopy-jobs-show"></a>azcopy jobs show

Zobrazí podrobné informace o daném ID úlohy.

## <a name="synopsis"></a>Stručný obsah

Pokud je k dispozici pouze ID úlohy bez příznaku, bude vrácena souhrn průběhu úlohy.

Počty bajtů a procento dokončení, které se zobrazí po spuštění tohoto příkazu, odrážejí pouze soubory, které jsou dokončeny v rámci úlohy. Nereflektují částečně dokončené soubory.

Pokud je nastaven příznak `with-status`, zobrazí se seznam přenosů v úloze s danou hodnotou.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Související koncepční články

- [Začínáme s AzCopy](storage-use-azcopy-v10.md)
- [Přenos dat pomocí AzCopy a BLOB Storage](storage-use-azcopy-blobs.md)
- [Přenos dat pomocí AzCopy a úložiště souborů](storage-use-azcopy-files.md)
- [Konfigurace, optimalizace a řešení potíží s AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Možnosti

|Možnost|Popis|
|--|--|
|-h,--help|Zobrazí obsah nápovědu pro příkaz Zobrazit.|
|--with-stavový řetězec|Jenom zobrazit seznam přenosů úlohy s tímto stavem, dostupné hodnoty: spuštěno, úspěch, neúspěšné|

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

|Možnost|Popis|
|---|---|
|--Cap – Mbps|Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.|
|--výstupní řetězec typu|Formát výstupu příkazu Mezi možnosti patří: text, JSON. Výchozí hodnota je "text".|

## <a name="see-also"></a>Viz také

- [AzCopy úlohy](storage-ref-azcopy-jobs.md)
