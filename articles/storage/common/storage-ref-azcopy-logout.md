---
title: odhlášení AzCopy | Microsoft Docs
description: Tento článek poskytuje referenční informace k příkazu pro odhlášení AzCopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: f42e2517f8c40855e56bd062fe8bc9b22634d4dc
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195916"
---
# <a name="azcopy-logout"></a>odhlášení AzCopy

Zaznamená uživatele a ukončí přístup k prostředkům Azure Storage.

## <a name="synopsis"></a>Stručný obsah

Tento příkaz odebere všechny přihlašovací informace v mezipaměti pro aktuálního uživatele.

```azcopy
azcopy logout [flags]
```

## <a name="options"></a>Možnosti

|Možnost|Popis|
|--|--|
|-h,--help|Zobrazit obsah nápovědu pro příkaz pro odhlášení|

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

|Možnost|Popis|
|---|---|
|--Cap – Mbps|Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.|
|--výstupní řetězec typu|Formát výstupu příkazu Mezi možnosti patří: text, JSON. Výchozí hodnota je "text".|

## <a name="see-also"></a>Viz také:

- [AzCopy](storage-ref-azcopy.md)
