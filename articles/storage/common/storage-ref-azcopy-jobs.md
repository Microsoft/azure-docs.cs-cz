---
title: úlohy AzCopy | Microsoft Docs
description: Tento článek popisuje referenční informace o příkazu AzCopy Jobs.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 2422b16eb89ef6f1a6a1eb703d88f0ff2b76422e
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514733"
---
# <a name="azcopy-jobs"></a>azcopy jobs

Dílčí příkazy související se správou úloh.

## <a name="examples"></a>Příklady

```azcopy
azcopy jobs show [jobID]
```

## <a name="options"></a>Možnosti

|Možnost|Popis|
|--|--|
|-h,--help|Zobrazit obsah nápovědu pro příkaz úlohy|

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

|Možnost|Popis|
|---|---|
|--Cap – Mbps|Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.|
|--výstupní řetězec typu|Formát výstupu příkazu Mezi možnosti patří: text, JSON. Výchozí hodnota je "text".|

## <a name="see-also"></a>Další informace najdete v tématech

- [AzCopy](storage-ref-azcopy.md)
- [seznam úloh AzCopy](storage-ref-azcopy-jobs-list.md)
- [obnovení úloh AzCopy](storage-ref-azcopy-jobs-resume.md)
- [zobrazení úloh AzCopy](storage-ref-azcopy-jobs-show.md)
