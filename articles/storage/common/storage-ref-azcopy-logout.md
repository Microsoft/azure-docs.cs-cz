---
title: azkopie odhlášení | Dokumenty společnosti Microsoft
description: Tento článek obsahuje referenční informace pro příkaz odhlášení azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 538c54c338540059baa17b9818d444cc863b7309
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034056"
---
# <a name="azcopy-logout"></a>azcopy logout

Přihlásí uživatele a ukončí přístup k prostředkům Azure Storage.

## <a name="synopsis"></a>Synopse

Tento příkaz odebere všechny přihlašovací údaje uložených v mezipaměti pro aktuálního uživatele.

```azcopy
azcopy logout [flags]
```

## <a name="related-conceptual-articles"></a>Související koncepční články

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)
- [Přenos dat pomocí úložiště AzCopy a Blob](storage-use-azcopy-blobs.md)
- [Přenos dat pomocí AzCopy a ukládání souborů](storage-use-azcopy-files.md)
- [Konfigurace, optimalizace a řešení potíží s azcopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Možnosti

|Možnost|Popis|
|--|--|
|-h, --pomoc|Zobrazí obsah nápovědy pro příkaz odhlášení.|

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

|Možnost|Popis|
|---|---|
|--cap-mbps uint32|Završuje přenosovou rychlost v megabitech za sekundu. Propustnost se může mírně lišit od víčka. Pokud je tato možnost nastavena na nulu nebo je vynechána, propustnost není omezena.|
|--řetězec výstupního typu|Formát výstupu příkazu. Volby zahrnují: text, json. Výchozí hodnota je "text".|

## <a name="see-also"></a>Viz také

- [azkopie](storage-ref-azcopy.md)
