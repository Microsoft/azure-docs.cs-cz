---
title: AzCopy úlohy obnovit | Microsoft Docs
description: Tento článek popisuje referenční informace o příkazu pro obnovení úloh AzCopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 9e8dbbd40259c7a71f252d0d6e93dd6f135973de
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513541"
---
# <a name="azcopy-jobs-resume"></a>azcopy jobs resume

Obnoví existující úlohu s daným ID úlohy.

## <a name="synopsis"></a>Stručný obsah

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="options"></a>Možnosti

|Možnost|Popis|
|--|--|
|--cíl-řetězec SAS|Cílové SAS cíle pro daný JobId|
|--vyloučit řetězec|Filtr: při obnovování úlohy Vylučte tyto neúspěšné přenosy. Soubory by měly být oddělené znakem '; '.|
|-h,--help|Zobrazit obsah nápovědu pro příkaz Resume.|
|--zahrnout řetězec|Filtr: při obnovování úlohy zahrňte pouze tyto neúspěšné přenosy. Soubory by měly být oddělené znakem '; '.|
|--Source-řetězec SAS |zdrojové SAS zdroje pro daný JobId|

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

|Možnost|Popis|
|---|---|
|--Cap – Mbps|Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.|
|--výstupní řetězec typu|Formát výstupu příkazu Mezi možnosti patří: text, JSON. Výchozí hodnota je "text".|

## <a name="see-also"></a>Další informace najdete v tématech

- [AzCopy úlohy](storage-ref-azcopy-jobs.md)
