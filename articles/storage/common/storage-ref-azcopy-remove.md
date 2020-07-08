---
title: AzCopy odebrat | Microsoft Docs
description: Tento článek poskytuje referenční informace pro příkaz AzCopy Remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 05/04/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ab085b9a41120a9f56c1c2e39a89def8c3893747
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84221070"
---
# <a name="azcopy-remove"></a>azcopy remove

Odstraňte objekty blob nebo soubory z účtu služby Azure Storage.

## <a name="synopsis"></a>Stručný obsah

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Související koncepční články

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)
- [Přenos dat pomocí AzCopy a BLOB Storage](storage-use-azcopy-blobs.md)
- [Přenos dat pomocí AzCopy a úložiště souborů](storage-use-azcopy-files.md)
- [Konfigurace, optimalizace a řešení potíží s AzCopy](storage-use-azcopy-configure.md)

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
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include-pattern="*.jpg;*.pdf;exactName"
```

Odeberte celý virtuální adresář, ale vylučte z oboru určité objekty BLOB (například: každý objekt blob, který začíná foo nebo končí na panelu):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude-pattern="foo*;*bar"
```

Odeberte konkrétní objekty BLOB a virtuální adresáře tak, že do souboru zadáte jejich relativní cesty (ne kódování URL):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
file content:
  dir1/dir2
  blob1
  blob2

```

Odebere jeden soubor z Blob Storage účtu, který má hierarchický obor názvů (include/Exclude není podporovaný).

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Odebrání jednoho adresáře Blob Storage účet, který má hierarchický obor názvů (include/Exclude není podporovaný):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Možnosti

**--Odstranit-snímky –** řetězec ve výchozím nastavení operace odstranění se nezdařila, pokud má objekt BLOB snímky. Zadejte include pro odebrání kořenového objektu BLOB a všech jeho snímků; případně můžete zadat pouze pro odebrání pouze snímků, ale zachovat kořenový objekt BLOB.

**--Exclude vyloučení – řetězec cesty** vyloučí tyto cesty při odebrání. Tato možnost nepodporuje zástupné znaky (*). Kontroluje předponu relativní cesty. Příklad: myFolder; myFolder/subDirName/file.pdf.

**--vyloučit-vzorové** soubory vyloučení, kde název odpovídá seznamu vzorů. Například: *. jpg;*. PDF; přesný

**--Force-if – jen pro čtení**    Při odstraňování souboru nebo složky Azure Files vynutíte odstranění, i když má stávající objekt nastaven atribut jen pro čtení.

**-h,--** nápovědu pro odebrání

**--include-Path** řetězec zahrnuje pouze tyto cesty při odebrání. Tato možnost nepodporuje zástupné znaky (*). Kontroluje předponu relativní cesty. Příklad: myFolder; myFolder/subDirName/file.pdf

**--include – řetězec vzorů** zahrnuje pouze soubory, u kterých se název shoduje se seznamem vzorů. Například: *. jpg;*. PDF; přesný

**--list** -File řetězec definuje umístění souboru, který obsahuje seznam souborů a adresářů, které mají být odstraněny. Relativní cesty by měly být odděleny zalomením řádků a cesty by neměly být zakódované pomocí adresy URL.

**--řetězec na úrovni protokolu** definuje podrobnosti protokolu pro soubor protokolu. Dostupné úrovně zahrnují: informace (všechny žádosti a odpovědi), upozornění (pomalé odezvy), chyba (pouze neúspěšné žádosti) a žádné (žádné výstupní protokoly). (výchozí informace ') (výchozí "informace")

**--rekurzivní**                Při synchronizaci mezi adresáři se rekurzivně vyhledávají podadresáře.

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

|Možnost|Description|
|---|---|
|--Cap – Mbps|Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.|
|--výstupní řetězec typu|Formát výstupu příkazu Mezi možnosti patří: text, JSON. Výchozí hodnota je "text".|
|--Trusted – řetězec Microsoft-přípony   |Určuje další přípony domén, kde se můžou odesílat přihlašovací tokeny Azure Active Directory.  Výchozí hodnota je *. Core.Windows.NET;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Zde uvedené jsou přidány do výchozího nastavení. Z důvodu zabezpečení byste měli sem umístit jenom Microsoft Azure domény. Více položek oddělte středníkem.|

## <a name="see-also"></a>Viz také

- [AzCopy](storage-ref-azcopy.md)
