---
title: AzCopy doc | Microsoft Docs
description: Tento článek popisuje referenční informace o příkazu AzCopy doc.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b11d831be847716d4edfac4f9a0725e07a60c52a
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513869"
---
# <a name="azcopy-doc"></a>azcopy doc

Vygeneruje dokumentaci pro nástroj ve formátu Markdownu.

## <a name="synopsis"></a>Stručný obsah

Generuje dokumentaci pro nástroj ve formátu Markdownu a ukládá je do určeného umístění.

Ve výchozím nastavení jsou soubory uloženy ve složce s názvem "doc" v aktuálním adresáři.

```azcopy
azcopy doc [flags]
```

## <a name="options"></a>Možnosti

|Možnost|Popis|
|--|--|
|-h,--help|Zobrazí obsah nápovědu pro příkaz doc.|

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

|Možnost|Popis|
|---|---|
|--Cap – Mbps|Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.|
|--výstupní řetězec typu|Formát výstupu příkazu Mezi možnosti patří: text, JSON. Výchozí hodnota je "text".|

## <a name="see-also"></a>Další informace najdete v tématech

- [AzCopy](storage-ref-azcopy.md)
