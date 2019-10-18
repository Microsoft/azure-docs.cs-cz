---
title: úlohy AzCopy odebrat | Microsoft Docs
description: Tento článek popisuje referenční informace o příkazu pro odebrání úloh AzCopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 82c399580322334e67c0c9c2b88d1edf6f175e0c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518288"
---
# <a name="azcopy-jobs-remove"></a>Odebrání úloh AzCopy

Odebere všechny soubory přidružené k danému ID úlohy.

> [!NOTE] 
> Můžete přizpůsobit umístění, kam se ukládají soubory protokolů a plánů. Další informace najdete v příkazu [AzCopy ENV](storage-ref-azcopy-env.md) .

```
azcopy jobs remove [jobID] [flags]
```

## <a name="examples"></a>Příklady

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>Možnosti

**-h,--help**                Nápovědu pro odebrání

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

**--Cap – Mbps**      Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.

**--výstupní** formát řetězce výstupu příkazu. Mezi možnosti patří: text, JSON. Výchozí hodnota je "text". (výchozí "text")

## <a name="see-also"></a>Další informace najdete v tématech

- [AzCopy úlohy](storage-ref-azcopy-jobs.md)
