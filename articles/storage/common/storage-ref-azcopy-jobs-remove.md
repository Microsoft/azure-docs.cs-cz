---
title: azcopy pracovních míst odstranit | Dokumenty společnosti Microsoft
description: Tento článek obsahuje referenční informace pro příkaz odebrat úlohy azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ba403c8d823b7ead0414521ebd51dc6f6601ccd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034162"
---
# <a name="azcopy-jobs-remove"></a>azcopy jobs remove

Odeberte všechny soubory přidružené k id dané úlohy.

> [!NOTE] 
> Můžete přizpůsobit umístění, kde jsou uloženy soubory protokolu a plánu. Další informace najdete v příkazu [azcopy env.](storage-ref-azcopy-env.md)

```
azcopy jobs remove [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Související koncepční články

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)
- [Přenos dat pomocí úložiště AzCopy a Blob](storage-use-azcopy-blobs.md)
- [Přenos dat pomocí AzCopy a ukládání souborů](storage-use-azcopy-files.md)
- [Konfigurace, optimalizace a řešení potíží s azcopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Příklady

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>Možnosti

**-h, --pomoc**                Nápověda k odstranění.

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

**--cap-mbps uint32**      Završuje přenosovou rychlost v megabitech za sekundu. Propustnost se může mírně lišit od víčka. Pokud je tato možnost nastavena na nulu nebo je vynechána, propustnost není omezena.

**--řetězec typu výstupu** Formát výstupu příkazu. Volby zahrnují: text, json. Výchozí hodnota je text. (výchozí "text")

## <a name="see-also"></a>Viz také

- [azcopy jobs](storage-ref-azcopy-jobs.md)
