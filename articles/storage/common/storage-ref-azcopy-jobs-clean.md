---
title: AzCopy úlohy vyčistit | Microsoft Docs
description: Tento článek poskytuje referenční informace o příkazu vyčistit úlohy AzCopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 31529155ee44b2bcfad90e8634053403dfe8fc8c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518509"
---
# <a name="azcopy-jobs-clean"></a>vyčistit úlohy AzCopy

Odebrat všechny soubory protokolů a plánů pro všechny úlohy

```
azcopy jobs clean [flags]
```

## <a name="examples"></a>Příklady

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>Možnosti

**-h,--help**                Nápovědu pro vyčištění

**--with-status** řetězec odebere jenom úlohy s tímto stavem, dostupné hodnoty: Canceled, Completed, Failed, InProgress, All (výchozí hodnota All).

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

**--Cap – Mbps**      Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.

**--výstupní** formát řetězce výstupu příkazu. Mezi možnosti patří: text, JSON. Výchozí hodnota je "text". (výchozí "text")

## <a name="see-also"></a>Další informace najdete v tématech

- [AzCopy úlohy](storage-ref-azcopy-jobs.md)
