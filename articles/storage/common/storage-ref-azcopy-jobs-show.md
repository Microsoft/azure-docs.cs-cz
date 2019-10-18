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
ms.openlocfilehash: 42b8bde9adb6980ff2c7004d43b02fc1fdc38363
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513452"
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

## <a name="see-also"></a>Další informace najdete v tématech

- [AzCopy úlohy](storage-ref-azcopy-jobs.md)
