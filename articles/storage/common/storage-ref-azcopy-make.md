---
title: AzCopy vytvořit | Microsoft Docs
description: Tento článek popisuje referenční informace o příkazu AzCopy make.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ebed5c8dbe5001e9beab17bdbff41610277143b2
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514692"
---
# <a name="azcopy-make"></a>azcopy make

Vytvoří kontejner nebo sdílenou složku souborů.

## <a name="synopsis"></a>Stručný obsah

Vytvořte kontejner nebo sdílenou složku reprezentované danou adresou URL prostředku.

```azcopy
azcopy make [resourceURL] [flags]
```

## <a name="examples"></a>Příklady

```azcopy
azcopy make "https://[account-name].[blob,file,dfs].core.windows.net/[top-level-resource-name]"
```

## <a name="options"></a>Možnosti

|Možnost|Popis|
|--|--|
|-h,--help|Zobrazit obsah nápovědu pro příkaz make. |
|--kvóta-GB UInt32|Určuje maximální velikost sdílené složky v gigabajtech (GiB), 0 znamená, že přijmete výchozí kvótu souborové služby.|

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

|Možnost|Popis|
|---|---|
|--Cap – Mbps|Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.|
|--výstupní řetězec typu|Formát výstupu příkazu Mezi možnosti patří: text, JSON. Výchozí hodnota je "text".|

## <a name="see-also"></a>Další informace najdete v tématech

- [AzCopy](storage-ref-azcopy.md)
