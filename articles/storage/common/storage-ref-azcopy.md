---
title: azkopie | Dokumenty společnosti Microsoft
description: Tento článek obsahuje referenční informace pro příkaz azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 5da3a2e5d003a191bff66af6599cae4d34ab60c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74038094"
---
# <a name="azcopy"></a>azcopy

AzCopy je nástroj příkazového řádku, který přesouvá data do a z Azure Storage.

## <a name="synopsis"></a>Synopse

Obecný formát příkazů je: `azcopy [command] [arguments] --[flag-name]=[flag-value]`.

Informace o problémech nebo další informace [https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy)o nástroji naleznete v tématu .

## <a name="related-conceptual-articles"></a>Související koncepční články

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)
- [Přenos dat pomocí úložiště AzCopy a Blob](storage-use-azcopy-blobs.md)
- [Přenos dat pomocí AzCopy a ukládání souborů](storage-use-azcopy-files.md)
- [Konfigurace, optimalizace a řešení potíží s azcopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Možnosti

**--cap-mbps uint32**   Završuje přenosovou rychlost v megabitech za sekundu. Propustnost se může mírně lišit od víčka. Pokud je tato možnost nastavena na nulu nebo je vynechána, propustnost není omezena.

**-h, --pomoc** Nápověda pro azkopii
      
**--výstupní typ**  Formát výstupu příkazu. Volby zahrnují: text, json. Výchozí hodnota je text. (výchozí "text")

## <a name="see-also"></a>Viz také

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)
- [azcopy bench](storage-ref-azcopy-bench.md)
- [azcopy copy](storage-ref-azcopy-copy.md)
- [azcopy doc](storage-ref-azcopy-doc.md)
- [azcopy env](storage-ref-azcopy-env.md)
- [azcopy jobs](storage-ref-azcopy-jobs.md)
- [azcopy jobs clean](storage-ref-azcopy-jobs-clean.md)
- [azcopy jobs list](storage-ref-azcopy-jobs-list.md)
- [azcopy jobs remove](storage-ref-azcopy-jobs-remove.md)
- [azcopy jobs resume](storage-ref-azcopy-jobs-resume.md)
- [azcopy jobs show](storage-ref-azcopy-jobs-show.md)
- [azcopy list](storage-ref-azcopy-list.md)
- [azcopy login](storage-ref-azcopy-login.md)
- [azcopy logout](storage-ref-azcopy-logout.md)
- [azcopy make](storage-ref-azcopy-make.md)
- [azcopy remove](storage-ref-azcopy-remove.md)
- [azcopy sync](storage-ref-azcopy-sync.md)
