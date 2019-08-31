---
title: úlohy AzCopy | Microsoft Docs
description: Tento článek popisuje referenční informace o příkazu AzCopy Jobs.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: bd5469691f8d859bfcbca7479cdcb2e468a94e26
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195838"
---
# <a name="azcopy-jobs"></a>AzCopy úlohy

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

## <a name="see-also"></a>Viz také:

- [AzCopy](storage-ref-azcopy.md)
- [seznam úloh AzCopy](storage-ref-azcopy-jobs-list.md)
- [obnovení úloh AzCopy](storage-ref-azcopy-jobs-resume.md)
- [zobrazení úloh AzCopy](storage-ref-azcopy-jobs-show.md)
