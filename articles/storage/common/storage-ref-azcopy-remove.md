---
title: AzCopy odebrat | Microsoft Docs
description: Tento článek poskytuje referenční informace pro příkaz AzCopy Remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 0cc366ab2cdad9c7258dca905d8f4a06472119fe
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195903"
---
# <a name="azcopy-remove"></a>odebrat AzCopy

Odstraní entity z Azure Storage Blob, souboru a Azure Data Lake Storage Gen2.

## <a name="synopsis"></a>Stručný obsah

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="examples"></a>Příklady

Odeberte jeden objekt BLOB s SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Odebrat celý virtuální adresář s SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Odeberte jenom hlavní objekty blob ve virtuálním adresáři, ale ne jejich podadresáře:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Odeberte podmnožinu objektů BLOB ve virtuálním adresáři (například pouze soubory s příponou jpg a PDF nebo pokud je název objektu BLOB "" ""):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

Odeberte celý virtuální adresář, ale vylučte z oboru určité objekty BLOB (například: každý objekt blob, který začíná foo nebo končí na panelu):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

Odebrat jeden soubor z Data Lake Storage Gen2 (include a Exclude Nepodporováno):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Odebrat jeden adresář z Data Lake Storage Gen2 (include a Exclude Nepodporováno):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Možnosti

|Možnost|Popis|
|--|--|
|--vyloučit řetězec|vyloučí soubory, u kterých se název shoduje se seznamem vzorů. Například: *. jpg;* . PDF; přesný|
|-h,--help|Zobrazit obsah nápovědu pro příkaz Remove|
|--zahrnout řetězec|Zahrnout pouze soubory, kde se název shoduje se seznamem vzorů. Například: *. jpg;* . PDF; přesný|
|--řetězec na úrovni protokolu|Zadejte podrobnosti protokolu pro soubor protokolu. Dostupné úrovně zahrnují: INFORMACE (všechny žádosti a odpovědi), upozornění (pomalé odezvy), chyba (pouze neúspěšné žádosti) a žádné (žádné výstupní protokoly). (výchozí "informace")|
|--rekurzivní|Při synchronizaci mezi adresáři se rekurzivně vyhledávají podadresáře.|

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

|Možnost|Popis|
|---|---|
|--Cap – Mbps|Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.|
|--výstupní řetězec typu|Formát výstupu příkazu Mezi možnosti patří: text, JSON. Výchozí hodnota je "text".|

## <a name="see-also"></a>Viz také:

- [AzCopy](storage-ref-azcopy.md)
