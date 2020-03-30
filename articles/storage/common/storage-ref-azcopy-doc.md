---
title: azkopie doc | Dokumenty společnosti Microsoft
description: Tento článek obsahuje referenční informace pro příkaz azcopy doc.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d071517377053e8f4f22ad00966e2be688b0d486
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74029863"
---
# <a name="azcopy-doc"></a>azcopy doc

Generuje dokumentaci pro nástroj ve formátu Markdown.

## <a name="synopsis"></a>Synopse

Generuje dokumentaci pro nástroj ve formátu Markdown a ukládá je do určeného umístění.

Ve výchozím nastavení jsou soubory uloženy ve složce s názvem "doc" uvnitř aktuálního adresáře.

```azcopy
azcopy doc [flags]
```

## <a name="related-conceptual-articles"></a>Související koncepční články

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)
- [Přenos dat pomocí úložiště AzCopy a Blob](storage-use-azcopy-blobs.md)
- [Přenos dat pomocí AzCopy a ukládání souborů](storage-use-azcopy-files.md)
- [Konfigurace, optimalizace a řešení potíží s azcopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Možnosti

|Možnost|Popis|
|--|--|
|-h, --pomoc|Zobrazí obsah nápovědy pro příkaz doc.|

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

|Možnost|Popis|
|---|---|
|--cap-mbps uint32|Završuje přenosovou rychlost v megabitech za sekundu. Propustnost se může mírně lišit od víčka. Pokud je tato možnost nastavena na nulu nebo je vynechána, propustnost není omezena.|
|--řetězec výstupního typu|Formát výstupu příkazu. Volby zahrnují: text, json. Výchozí hodnota je "text".|

## <a name="see-also"></a>Viz také

- [azkopie](storage-ref-azcopy.md)
