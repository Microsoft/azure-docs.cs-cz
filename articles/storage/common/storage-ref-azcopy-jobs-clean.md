---
title: azcopy pracovních míst čisté | Dokumenty společnosti Microsoft
description: Tento článek obsahuje referenční informace pro azcopy úlohy clean příkaz.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7ae14c3606dfe6bffa8481682843f3f2e85c2131
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033727"
---
# <a name="azcopy-jobs-clean"></a>azcopy jobs clean

Odebrání všech souborů protokolu a plánování pro všechny úlohy

```
azcopy jobs clean [flags]
```

## <a name="related-conceptual-articles"></a>Související koncepční články

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)
- [Přenos dat pomocí úložiště AzCopy a Blob](storage-use-azcopy-blobs.md)
- [Přenos dat pomocí AzCopy a ukládání souborů](storage-use-azcopy-files.md)
- [Konfigurace, optimalizace a řešení potíží s azcopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Příklady

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>Možnosti

**-h, --pomoc**                Pomoc pro čisté.

**--with-status** string Pouze odeberte úlohy s tímto stavem, dostupné hodnoty: Zrušeno, Dokončeno, Nezdařilo se, Probíhá, Vše (výchozí "Vše")

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

**--cap-mbps uint32**      Završuje přenosovou rychlost v megabitech za sekundu. Propustnost se může mírně lišit od víčka. Pokud je tato možnost nastavena na nulu nebo je vynechána, propustnost není omezena.

**--řetězec typu výstupu** Formát výstupu příkazu. Volby zahrnují: text, json. Výchozí hodnota je text. (výchozí "text")

## <a name="see-also"></a>Viz také

- [azcopy jobs](storage-ref-azcopy-jobs.md)
