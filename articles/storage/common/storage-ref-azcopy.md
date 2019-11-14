---
title: AzCopy | Microsoft Docs
description: Tento článek popisuje referenční informace o příkazu AzCopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 5da3a2e5d003a191bff66af6599cae4d34ab60c6
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038094"
---
# <a name="azcopy"></a>azcopy

AzCopy je nástroj příkazového řádku, který přesouvá data do Azure Storage a z nich.

## <a name="synopsis"></a>Stručný obsah

Obecný formát příkazů je: `azcopy [command] [arguments] --[flag-name]=[flag-value]`.

Chcete-li nahlásit problémy nebo získat další informace o nástroji, přečtěte si téma [https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy).

## <a name="related-conceptual-articles"></a>Související koncepční články

- [Začínáme s AzCopy](storage-use-azcopy-v10.md)
- [Přenos dat pomocí AzCopy a BLOB Storage](storage-use-azcopy-blobs.md)
- [Přenos dat pomocí AzCopy a úložiště souborů](storage-use-azcopy-files.md)
- [Konfigurace, optimalizace a řešení potíží s AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Možnosti

**--Cap – Mbps**   Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.

**-h,--help** Nápovědu pro AzCopy
      
**--výstupní typ**  Formát výstupu příkazu Mezi možnosti patří: text, JSON. Výchozí hodnota je "text". (výchozí "text")

## <a name="see-also"></a>Viz také

- [Začínáme s AzCopy](storage-use-azcopy-v10.md)
- [AzCopy](storage-ref-azcopy-bench.md)
- [AzCopy kopii](storage-ref-azcopy-copy.md)
- [AzCopy doc](storage-ref-azcopy-doc.md)
- [AzCopy ENV](storage-ref-azcopy-env.md)
- [AzCopy úlohy](storage-ref-azcopy-jobs.md)
- [vyčistit úlohy AzCopy](storage-ref-azcopy-jobs-clean.md)
- [seznam úloh AzCopy](storage-ref-azcopy-jobs-list.md)
- [Odebrání úloh AzCopy](storage-ref-azcopy-jobs-remove.md)
- [obnovení úloh AzCopy](storage-ref-azcopy-jobs-resume.md)
- [zobrazení úloh AzCopy](storage-ref-azcopy-jobs-show.md)
- [seznam AzCopy](storage-ref-azcopy-list.md)
- [přihlášení AzCopy](storage-ref-azcopy-login.md)
- [odhlášení AzCopy](storage-ref-azcopy-logout.md)
- [AzCopy vytvořit](storage-ref-azcopy-make.md)
- [odebrat AzCopy](storage-ref-azcopy-remove.md)
- [AzCopy synchronizaci](storage-ref-azcopy-sync.md)
