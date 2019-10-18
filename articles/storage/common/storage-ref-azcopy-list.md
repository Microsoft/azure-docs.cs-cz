---
title: seznam AzCopy | Microsoft Docs
description: Tento článek popisuje referenční informace o příkazu AzCopy list.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b7348e3790166e1a1aecab422e571b8f2fc7cd5f
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513517"
---
# <a name="azcopy-list"></a>azcopy list

Zobrazí seznam entit v daném prostředku.

## <a name="synopsis"></a>Stručný obsah

V aktuální verzi jsou podporovány pouze kontejnery objektů BLOB.

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="examples"></a>Příklady

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>Možnosti

|Možnost|Popis|
|--|--|
|-h,--help|Zobrazit obsah nápovědu pro příkaz list|
|--strojově čitelné|Zobrazuje seznam velikostí souborů v bajtech.|
|--megabajtů-units|Zobrazí jednotky v objednávkách od 1000 do 1024.|
|--spuštěno – se shodují|Spočítá celkový počet souborů a jejich velikosti.|

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

|Možnost|Popis|
|---|---|
|--Cap – Mbps|Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.|
|--výstupní řetězec typu|Formát výstupu příkazu Mezi možnosti patří: text, JSON. Výchozí hodnota je "text".|

## <a name="see-also"></a>Další informace najdete v tématech

- [AzCopy](storage-ref-azcopy.md)
