---
title: AzCopy synchronizaci | Microsoft Docs
description: Tento článek popisuje referenční informace o příkazu AzCopy Sync.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: fb6c3b711a89ae7e4ef403a75927c4c6172523d0
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195760"
---
# <a name="azcopy-sync"></a>AzCopy synchronizaci

Replikuje zdrojové umístění do cílového umístění.

## <a name="synopsis"></a>Stručný obsah

Časy poslední změny se používají pro porovnání. Soubor se přeskočí, pokud čas poslední změny v cíli je novější.

Podporované páry jsou:

- místní < – > Azure Blob (dá se použít ověřování pomocí SAS nebo OAuth)

Příkaz synchronizovat se v několika ohledech liší od příkazu pro kopírování:

  1. Rekurzivní příznak je ve výchozím nastavení zapnutý.
  2. Zdroj a cíl by neměl obsahovat vzory (například * nebo?).
  3. Příznaky zahrnutí a vyloučení můžou být seznamem vzorů, které odpovídají názvům souborů. Ukázku najdete v části příklad.
  4. Pokud jsou soubory nebo objekty BLOB v cílovém umístění, které se nenacházejí ve zdroji, zobrazí se uživateli výzva k jejich odstranění.

     Tuto výzvu lze použít k tichému použití odpovídajících příznaků k automatickému zodpovězení otázky na odstranění.

### <a name="advanced"></a>Upřesnit

AzCopy automaticky detekuje typ obsahu souborů při nahrávání z místního disku na základě přípony souboru nebo obsahu (Pokud není zadáno žádné rozšíření).

Integrovaná vyhledávací tabulka je malá, ale v systému UNIX je rozšířena o soubory MIME. Types v místním systému, pokud jsou k dispozici v jednom nebo více těchto názvech:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

V systému Windows jsou z registru extrahovány typy MIME.

```azcopy
azcopy sync [flags]
```

## <a name="examples"></a>Příklady

Synchronizovat jeden soubor:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Stejné jako výše, ale tentokrát také vypočítat hodnotu hash MD5 obsahu souboru a uložit ji jako vlastnost content-MD5 objektu BLOB:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Synchronizovat celý adresář včetně jeho podsložek (Všimněte si, že ve výchozím nastavení je rekurzivní):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

or

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Synchronizovat pouze ty soubory, které jsou v adresáři, ale ne jeho podadresáře:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Synchronizace podmnožiny souborů v adresáři (například: pouze soubory jpg a PDF, nebo pokud je název souboru "" ""):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include="*.jpg;*.pdf;exactName"
```

Synchronizuje celý adresář, ale vyloučí určité soubory z oboru (například každý soubor, který začíná foo nebo končí na panelu):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude="foo*;*bar"
```

> [!NOTE]
> Pokud jsou společně použity příznaky include/Exclude, budou zobrazeny pouze soubory, které odpovídají vzorům zahrnutí, ale ty, které odpovídají vzorům pro vyloučení, budou vždy ignorovány.

## <a name="options"></a>Možnosti

|Možnost|Popis|
|--|--|
|--Block-size-MB float|Při nahrávání do Azure Storage nebo stahování z Azure Storage použít tuto velikost bloku (určenou v souboru MiB). Výchozí hodnota se automaticky vypočítá na základě velikosti souboru. Jsou povoleny desetinné zlomky (například: 0,25).|
|--check-MD5 – řetězec|Určuje, jak mají být při stahování ověřovány striktní hodnoty hash MD5. Tato možnost je k dispozici pouze při stahování. K dispozici jsou tyto hodnoty: Nekontrolujte, přihlaste se, FailIfDifferent, FailIfDifferentOrMissing. (výchozí "FailIfDifferent").|
|--Delete – cílový řetězec|definuje, jestli se mají odstranit nadbytečné soubory z cílového umístění, které se nenacházejí ve zdroji. Může být nastaveno na hodnotu true, false nebo prompt. Pokud se nastaví výzva, před plánováním souborů a objektů BLOB k odstranění se uživateli zobrazí dotaz. (výchozí hodnota false).|
|--vyloučit řetězec|Vyloučí soubory, u kterých se název shoduje se seznamem vzorů. Například: *. jpg;* . PDF; Exact.|
|-h,--help|Zobrazit obsah nápovědu pro příkaz synchronizovat|
|--zahrnout řetězec|Zahrňte pouze soubory, u kterých se název shoduje se seznamem vzorů. Například: *. jpg;* . PDF; Exact.|
|--řetězec na úrovni protokolu|Zadejte podrobnosti protokolu pro soubor protokolu, dostupné úrovně: INFORMACE (všechny žádosti a odpovědi), upozornění (pomalé odezvy), chyba (pouze neúspěšné žádosti) a žádné (žádné výstupní protokoly). (výchozí "informace").|
|--Put-MD5|Vytvořte hodnotu hash MD5 každého souboru a uložte hodnotu hash jako vlastnost content-MD5 cílového objektu BLOB nebo souboru. (Ve výchozím nastavení není hodnota hash vytvořena.) K dispozici pouze při nahrávání.|
|--rekurzivní|Ve výchozím nastavení platí, že při synchronizaci mezi adresáři se rekurzivně vyhledávají podadresáře. (výchozí hodnota true).|

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

|Možnost|Popis|
|---|---|
|--Cap – Mbps|Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.|
|--výstupní řetězec typu|Formát výstupu příkazu Mezi možnosti patří: text, JSON. Výchozí hodnota je "text".|

## <a name="see-also"></a>Viz také:

- [AzCopy](storage-ref-azcopy.md)
