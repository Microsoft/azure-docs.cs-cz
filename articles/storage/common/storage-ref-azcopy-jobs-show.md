---
title: zobrazení úloh AzCopy | Microsoft Docs
description: Tento článek obsahuje referenční informace o příkazu zobrazení úloh AzCopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: a14546d8bfc50531902b5150d38ee5ce8b8b5769
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503350"
---
# <a name="azcopy-jobs-show"></a>azcopy jobs show

Zobrazí podrobné informace o daném ID úlohy.

## <a name="synopsis"></a>Stručný obsah

Pokud je k dispozici pouze ID úlohy bez příznaku, bude vrácena souhrn průběhu úlohy.

Počty bajtů a procento dokončení, které se zobrazí po spuštění tohoto příkazu, odrážejí pouze soubory, které jsou dokončeny v rámci úlohy. Nereflektují částečně dokončené soubory.

Pokud `with-status` je příznak nastaven, zobrazí se seznam přenosů v úloze s danou hodnotou.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Související koncepční články

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)
- [Přenos dat pomocí AzCopy a BLOB Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Přenos dat s použitím AzCopy a úložiště souborů](storage-use-azcopy-files.md)

## <a name="options"></a>Možnosti

|Možnost|Popis|
|--|--|
|-h,--help|Zobrazí obsah nápovědu pro příkaz Zobrazit.|
|--with-stavový řetězec|Jenom zobrazit seznam přenosů úlohy s tímto stavem, dostupné hodnoty: spuštěno, úspěch, neúspěšné|

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

|Možnost|Popis|
|---|---|
|--Cap – MB/s float|Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.|
|--výstupní řetězec typu|Formát výstupu příkazu Mezi možnosti patří: text, JSON. Výchozí hodnota je "text".|
|--Trusted – řetězec Microsoft-přípony   |Určuje další přípony domén, kde se můžou odesílat přihlašovací tokeny Azure Active Directory.  Výchozí hodnota je *. Core.Windows.NET;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Zde uvedené jsou přidány do výchozího nastavení. Z důvodu zabezpečení byste měli sem umístit jenom Microsoft Azure domény. Více položek oddělte středníkem.|

## <a name="see-also"></a>Viz také

- [azcopy jobs](storage-ref-azcopy-jobs.md)
