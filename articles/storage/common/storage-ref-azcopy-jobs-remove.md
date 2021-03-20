---
title: úlohy AzCopy odebrat | Microsoft Docs
description: Tento článek popisuje referenční informace o příkazu pro odebrání úloh AzCopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 2744c2a082b5321fb671de08301981fd17396640
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879084"
---
# <a name="azcopy-jobs-remove"></a>azcopy jobs remove

Odebere všechny soubory přidružené k danému ID úlohy.

> [!NOTE] 
> Můžete přizpůsobit umístění, kam se ukládají soubory protokolů a plánů. Další informace najdete v příkazu [AzCopy ENV](storage-ref-azcopy-env.md) .

```
azcopy jobs remove [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Související koncepční články

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)
- [Přenos dat pomocí AzCopy a BLOB Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Přenos dat s použitím AzCopy a úložiště souborů](storage-use-azcopy-files.md)
- [Konfigurace, optimalizace a řešení potíží s AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Příklady

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>Možnosti

**--help**                Nápovědu pro odebrání

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

**--Cap – MB/s float**      Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.

**--výstupní** formát řetězce výstupu příkazu. Mezi možnosti patří: text, JSON. Výchozí hodnota je `text`. (výchozí `text` )

**--Trusted – řetězec Microsoft-přípony** Určuje další přípony domén, kde se můžou odesílat přihlašovací tokeny Azure Active Directory.  Výchozí hodnota je *. Core.Windows.NET;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Zde uvedené jsou přidány do výchozího nastavení. Z důvodu zabezpečení byste měli sem umístit jenom Microsoft Azure domény. Více položek oddělte středníkem.

## <a name="see-also"></a>Viz také

- [azcopy jobs](storage-ref-azcopy-jobs.md)
