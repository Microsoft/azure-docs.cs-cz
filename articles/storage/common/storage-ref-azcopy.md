---
title: AzCopy | Microsoft Docs
description: Tento článek popisuje referenční informace o příkazu AzCopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 4da9206f4500941179d781a0fe2a57ad15d7393d
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195890"
---
# <a name="azcopy"></a>AzCopy

AzCopy je nástroj příkazového řádku, který přesouvá data do Azure Storage a z nich.

## <a name="synopsis"></a>Stručný obsah

Obecný formát příkazů je: `azcopy [command] [arguments] --[flag-name]=[flag-value]`.

Chcete-li nahlásit problémy nebo získat další informace o nástroji [https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy), přečtěte si téma.

## <a name="options"></a>Možnosti

|Možnost|Popis|
|---|---|
|--Cap – Mbps|Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.|
|-h,--help|Zobrazuje obsah pro nápovědu pro AzCopy.|
|--výstupní řetězec typu|Formát výstupu příkazu Mezi možnosti patří: text, JSON. Výchozí hodnota je "text".|

## <a name="see-also"></a>Viz také:

- [Začínáme s AzCopy](storage-use-azcopy-v10.md)
- [AzCopy kopii](storage-ref-azcopy-copy.md)
- [AzCopy doc](storage-ref-azcopy-doc.md)
- [AzCopy ENV](storage-ref-azcopy-env.md)
- [AzCopy úlohy](storage-ref-azcopy-jobs.md)
- [seznam AzCopy](storage-ref-azcopy-list.md)
- [přihlášení AzCopy](storage-ref-azcopy-login.md)
- [odhlášení AzCopy](storage-ref-azcopy-logout.md)
- [AzCopy vytvořit](storage-ref-azcopy-make.md)
- [odebrat AzCopy](storage-ref-azcopy-remove.md)
- [AzCopy synchronizaci](storage-ref-azcopy-sync.md)
