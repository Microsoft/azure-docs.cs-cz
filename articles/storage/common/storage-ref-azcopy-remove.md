---
title: azkopie odstranit | Dokumenty společnosti Microsoft
description: Tento článek obsahuje referenční informace pro příkaz azcopy remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: abce1acb88e920c0de7bbb6447ec9d838f10486c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033987"
---
# <a name="azcopy-remove"></a>azcopy remove

Odstraňte objekty BLOB nebo soubory z účtu úložiště Azure.

## <a name="synopsis"></a>Synopse

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Související koncepční články

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)
- [Přenos dat pomocí úložiště AzCopy a Blob](storage-use-azcopy-blobs.md)
- [Přenos dat pomocí AzCopy a ukládání souborů](storage-use-azcopy-files.md)
- [Konfigurace, optimalizace a řešení potíží s azcopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Příklady

Odebrání jednoho objektu blob pomocí SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Odebrání celého virtuálního adresáře pomocí sas:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Odeberte pouze horní objekty BLOB uvnitř virtuálního adresáře, ale ne jeho podadresáře:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Odeberte podmnožinu objektů BLOB ve virtuálním adresáři (například: jenom soubory jpg a pdf nebo pokud je název objektu blob "exactName"):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

Odeberte celý virtuální adresář, ale vylučte určité objekty BLOB z oboru (například: každý objekt blob začínající na foo nebo končí pruhem):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

Odstraňte konkrétní objekty BLOB a virtuální adresáře umístěním jejich relativních cest (NOT URL-kódovaných) do souboru:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
file content:
  dir1/dir2
  blob1
  blob2

```

Odeberte jeden soubor z účtu úložiště objektů Blob, který má hierarchický obor názvů (zahrnutí/vyloučení není podporováno).

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Odeberte jeden adresář, účet úložiště objektů Blob, který má hierarchický obor názvů (include/exclude není podporováno):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Možnosti

**--exclude-path řetězec**      Při odebírání vylučte tyto cesty. Tato možnost nepodporuje zástupné znaky (*). Zkontroluje předponu relativní cesty. Příklad: myFolder;myFolder/subDirName/file.pdf.

**--exclude-pattern** Vylučte soubory, kde se název shoduje se seznamem vzorů. Například: *.jpg;*. pdf;exactName

**-h, --pomoc** při odstraňování

**--include-path** string Zahrnout pouze tyto cesty při odebírání. Tato možnost nepodporuje zástupné znaky (*). Zkontroluje předponu relativní cesty. Příklad: myFolder;myFolder/subDirName/file.pdf

**--include-pattern** Include only files where the name matches the pattern. Například: *.jpg;*. pdf;exactName

**--list-of-files** string Definuje umístění souboru, který obsahuje seznam souborů a adresářů, které mají být odstraněny. Relativní cesty by měly být odděleny konce řádků a cesty by neměly být kódovány adresou URL.

**--řetězec na úrovni protokolu** Definujte podrobnost protokolu pro soubor protokolu. Dostupné úrovně zahrnují: INFO (všechny požadavky/odpovědi), UPOZORNĚNÍ (pomalé odpovědi), ERROR (pouze neúspěšné požadavky) a NONE (žádné výstupní protokoly). (výchozí 'INFO') (výchozí "INFO")

**--rekurzivní**                Při synchronizaci mezi adresáři se rekurzivně podívejte do podadresářů.

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

|Možnost|Popis|
|---|---|
|--cap-mbps uint32|Završuje přenosovou rychlost v megabitech za sekundu. Propustnost se může mírně lišit od víčka. Pokud je tato možnost nastavena na nulu nebo je vynechána, propustnost není omezena.|
|--řetězec výstupního typu|Formát výstupu příkazu. Volby zahrnují: text, json. Výchozí hodnota je "text".|

## <a name="see-also"></a>Viz také

- [azkopie](storage-ref-azcopy.md)
