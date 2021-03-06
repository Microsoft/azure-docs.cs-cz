---
title: AzCopy úlohy vyčistit | Microsoft Docs
description: Tento článek poskytuje referenční informace o příkazu vyčistit úlohy AzCopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 01bb7e37965abacac8105689bcb5ae52c548d647
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503418"
---
# <a name="azcopy-jobs-clean"></a>azcopy jobs clean

Odebrat všechny soubory protokolů a plánů pro všechny úlohy

```
azcopy jobs clean [flags]
```

## <a name="related-conceptual-articles"></a>Související koncepční články

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)
- [Přenos dat pomocí AzCopy a BLOB Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Přenos dat s použitím AzCopy a úložiště souborů](storage-use-azcopy-files.md)

## <a name="examples"></a>Příklady

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>Možnosti

**--help**                Nápovědu pro vyčištění

**--with-stavový** řetězec odebere jenom úlohy s tímto stavem, dostupné hodnoty: `Canceled` , `Completed` , `Failed` , `InProgress` , `All` (výchozí `All` ).

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

**--Cap – MB/s float**      Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.

**--výstupní** formát řetězce výstupu příkazu. Mezi možnosti patří: text, JSON. Výchozí hodnota je "text". (výchozí "text")

**--Trusted – řetězec Microsoft-přípony** Určuje další přípony domén, kde se můžou odesílat přihlašovací tokeny Azure Active Directory.  Výchozí hodnota je *. Core.Windows.NET;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Zde uvedené jsou přidány do výchozího nastavení. Z důvodu zabezpečení byste měli sem umístit jenom Microsoft Azure domény. Více položek oddělte středníkem.

## <a name="see-also"></a>Viz také

- [azcopy jobs](storage-ref-azcopy-jobs.md)
