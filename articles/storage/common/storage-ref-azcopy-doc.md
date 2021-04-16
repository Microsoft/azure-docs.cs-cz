---
title: AzCopy doc | Microsoft Docs
description: Tento článek popisuje referenční informace o příkazu AzCopy doc.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8e88491c21948ddc28868e203ee5c8c7a52783f4
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503537"
---
# <a name="azcopy-doc"></a>azcopy doc

Vygeneruje dokumentaci pro nástroj ve formátu Markdownu.

## <a name="synopsis"></a>Stručný obsah

Generuje dokumentaci pro nástroj ve formátu Markdownu a ukládá je do určeného umístění.

Ve výchozím nastavení jsou soubory uloženy ve složce s názvem "doc" v aktuálním adresáři.

```azcopy
azcopy doc [flags]
```

## <a name="related-conceptual-articles"></a>Související koncepční články

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)
- [Přenos dat pomocí AzCopy a BLOB Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Přenos dat s použitím AzCopy a úložiště souborů](storage-use-azcopy-files.md)

## <a name="options"></a>Možnosti

|Možnost|Popis|
|--|--|
|-h,--help|Zobrazí obsah nápovědu pro příkaz doc.|

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

|Možnost|Popis|
|---|---|
|--Cap – MB/s float|Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.|
|--výstupní řetězec typu|Formát výstupu příkazu Mezi možnosti patří: text, JSON. Výchozí hodnota je "text".|
|--Trusted – řetězec Microsoft-přípony   | Určuje další přípony domén, kde se můžou odesílat přihlašovací tokeny Azure Active Directory.  Výchozí hodnota je *. Core.Windows.NET;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Zde uvedené jsou přidány do výchozího nastavení. Z důvodu zabezpečení byste měli sem umístit jenom Microsoft Azure domény. Více položek oddělte středníkem.|

## <a name="see-also"></a>Viz také

- [AzCopy](storage-ref-azcopy.md)
