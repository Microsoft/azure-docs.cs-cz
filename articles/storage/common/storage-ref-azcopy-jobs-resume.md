---
title: azcopy pracovních míst pokračovat | Dokumenty společnosti Microsoft
description: Tento článek obsahuje referenční informace pro příkaz obnovení azkopie.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 3ee7879475801660b5200dddca88a0a81b2b6b9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034143"
---
# <a name="azcopy-jobs-resume"></a>azcopy jobs resume

Obnoví existující úlohu s ID dané úlohy.

## <a name="synopsis"></a>Synopse

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Související koncepční články

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)
- [Přenos dat pomocí úložiště AzCopy a Blob](storage-use-azcopy-blobs.md)
- [Přenos dat pomocí AzCopy a ukládání souborů](storage-use-azcopy-files.md)
- [Konfigurace, optimalizace a řešení potíží s azcopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Možnosti

|Možnost|Popis|
|--|--|
|--destination-sas string --destination-sas string --destination-sas string --destination|Cíl SAS cíle pro daný JobId.|
|--vyloučit řetězec|Filtr: Při obnovení úlohy vylučte tyto neúspěšné přenosy. Soubory by měly být odděleny ';'.|
|-h, --pomoc|Zobrazit obsah nápovědy pro příkaz pokračovat.|
|--zahrnout řetězec|Filtr: zahrnout pouze tyto neúspěšné převody při obnovení úlohy. Soubory by měly být odděleny ';'.|
|--source-sas řetězec |zdroj SAS zdroje pro daný JobId.|

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

|Možnost|Popis|
|---|---|
|--cap-mbps uint32|Završuje přenosovou rychlost v megabitech za sekundu. Propustnost se může mírně lišit od víčka. Pokud je tato možnost nastavena na nulu nebo je vynechána, propustnost není omezena.|
|--řetězec výstupního typu|Formát výstupu příkazu. Volby zahrnují: text, json. Výchozí hodnota je "text".|

## <a name="see-also"></a>Viz také

- [azcopy jobs](storage-ref-azcopy-jobs.md)
