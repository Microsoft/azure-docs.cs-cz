---
title: zobrazení úloh AzCopy | Microsoft Docs
description: Tento článek obsahuje referenční informace o příkazu zobrazení úloh AzCopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d4369bd0c986ee20a0796436fea47509a711de4f
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195955"
---
# <a name="azcopy-jobs-show"></a>zobrazení úloh AzCopy

Zobrazí podrobné informace o daném ID úlohy.

## <a name="synopsis"></a>Stručný obsah

Pokud je k dispozici pouze ID úlohy bez příznaku, bude vrácena souhrn průběhu úlohy.

Pokud je `with-status` příznak nastaven, zobrazí se seznam přenosů v úloze s danou hodnotou.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="options"></a>Možnosti

|Možnost|Popis|
|--|--|
|-h,--help|Zobrazí obsah nápovědu pro příkaz Zobrazit.|
|--with-stavový řetězec|Jenom zobrazit seznam přenosů úlohy s tímto stavem, dostupné hodnoty: Spuštěno, úspěch, selhalo|

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

|Možnost|Popis|
|---|---|
|--Cap – Mbps|Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.|
|--výstupní řetězec typu|Formát výstupu příkazu Mezi možnosti patří: text, JSON. Výchozí hodnota je "text".|

## <a name="see-also"></a>Viz také:

- [AzCopy úlohy](storage-ref-azcopy-jobs.md)
