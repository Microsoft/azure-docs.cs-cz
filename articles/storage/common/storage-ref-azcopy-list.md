---
title: seznam AzCopy | Microsoft Docs
description: Tento článek popisuje referenční informace o příkazu AzCopy list.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8c40241c49917c6db6663f346aed0ec5d3b96be7
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195942"
---
# <a name="azcopy-list"></a>seznam AzCopy

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

## <a name="see-also"></a>Viz také:

- [AzCopy](storage-ref-azcopy.md)
