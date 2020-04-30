---
title: AzCopy synchronizaci | Microsoft Docs
description: Tento článek popisuje referenční informace o příkazu AzCopy Sync.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d855019be7f357a35a26d14e68ba3d427d984e17
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82086024"
---
# <a name="azcopy-sync"></a>azcopy sync

Replikuje zdrojové umístění do cílového umístění.

## <a name="synopsis"></a>Stručný obsah

Časy poslední změny se používají pro porovnání. Soubor se přeskočí, pokud čas poslední změny v cíli je novější.

Podporované páry jsou:

- místní < – > Azure Blob (dá se použít ověřování pomocí SAS nebo OAuth)
- Azure Blob < – > Azure Blob (zdroj musí zahrnovat SAS nebo je veřejně přístupný; pro cíl se dá použít ověřování pomocí SAS nebo OAuth)
- Soubor Azure <-> soubor Azure (zdroj musí zahrnovat SAS nebo je veřejně přístupný; Pro cíl by se mělo použít ověřování SAS.)

Příkaz synchronizovat se v několika ohledech liší od příkazu pro kopírování:

1. Ve výchozím nastavení má rekurzivní příznak hodnotu true a synchronizace kopíruje všechny podadresáře. Synchronizace pouze kopíruje soubory nejvyšší úrovně v adresáři, pokud je rekurzivní příznak false.
2. Při synchronizaci mezi virtuálními adresáři přidejte do cesty koncové lomítko (viz příklady), pokud existuje objekt BLOB se stejným názvem jako jeden z virtuálních adresářů.
3. Pokud je příznak ' deleteDestination ' nastaven na hodnotu true nebo se zobrazí výzva, synchronizace odstraní soubory a objekty BLOB v cílovém umístění, které nejsou přítomny ve zdroji.

## <a name="related-conceptual-articles"></a>Související koncepční články

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)
- [Přenos dat pomocí AzCopy a BLOB Storage](storage-use-azcopy-blobs.md)
- [Přenos dat pomocí AzCopy a úložiště souborů](storage-use-azcopy-files.md)
- [Konfigurace, optimalizace a řešení potíží s AzCopy](storage-use-azcopy-configure.md)

### <a name="advanced"></a>Upřesnit

Pokud nezadáte příponu souboru, AzCopy automaticky detekuje typ obsahu souborů při nahrávání z místního disku na základě přípony souboru nebo obsahu (Pokud není zadané žádné rozšíření).

Integrovaná vyhledávací tabulka je malá, ale v systému UNIX je rozšířena o soubory MIME. Types v místním systému, pokud jsou k dispozici v jednom nebo více těchto názvech:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

V systému Windows jsou z registru extrahovány typy MIME.

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>Příklady

Synchronizovat jeden soubor:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

> [!NOTE]
> Cílový objekt BLOB *musí* existovat. Slouží `azcopy copy` ke zkopírování jednoho souboru, který ještě v cílovém umístění neexistuje. V opačném případě dojde k následující `Cannot perform sync due to error: sync must happen between source and destination of the same type, e.g. either file <-> file, or directory/container <-> directory/container`chybě:.

Stejné jako výše, ale tentokrát také vypočítat hodnotu hash MD5 obsahu souboru a uložit ji jako vlastnost content-MD5 objektu BLOB:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Synchronizovat celý adresář včetně jeho podsložek (Všimněte si, že ve výchozím nastavení je rekurzivní):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

– nebo –

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

Synchronizace jednoho objektu BLOB:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Synchronizovat virtuální adresář:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

Synchronizovat virtuální adresář, který má stejný název jako objekt BLOB (přidejte koncové lomítko k cestě, aby bylo možné nejednoznačnost):

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Synchronizovat adresář souborů Azure (stejná syntaxe jako objekt BLOB):

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> Pokud jsou společně použity příznaky include/Exclude, budou zobrazeny pouze soubory, které odpovídají vzorům zahrnutí, ale ty, které odpovídají vzorům pro vyloučení, budou vždy ignorovány.

## <a name="options"></a>Možnosti

**--Block-size-MB** float při nahrávání do Azure Storage nebo stahování z Azure Storage použít tuto velikost bloku (zadanou v souboru MIB). Výchozí hodnota se automaticky vypočítá na základě velikosti souboru. Jsou povoleny desetinné zlomky (například: 0,25).

**--check-MD5** řetězec Určuje, jak by měly být při stahování ověřovány STRIKTNĚ hash MD5. Tato možnost je k dispozici pouze při stahování. K dispozici jsou tyto hodnoty: nezaškrtnuto, LogOned, FailIfDifferent, FailIfDifferentOrMissing. (výchozí ' FailIfDifferent '). (výchozí "FailIfDifferent")

**--Delete – cílový** řetězec definuje, jestli se mají odstranit nadbytečné soubory z cílového umístění, které se nenacházejí ve zdroji. Může být nastaveno na hodnotu true, false nebo prompt. Pokud se nastaví výzva, před plánováním souborů a objektů BLOB k odstranění se uživateli zobrazí dotaz. (výchozí hodnota false). (výchozí hodnota "NEPRAVDA")

**--Exclude – řetězec atributů** (pouze Windows) vyloučení souborů, jejichž atributy odpovídají seznamu atributů. Příklad: A; Pracují Í

**--Exclude vyloučení – řetězec cesty** vyloučí tyto cesty při kopírování. Tato možnost nepodporuje zástupné znaky (*). Kontroluje předponu relativní cesty (například: myFolder; myFolder/subDirName/File. PDF). Pokud se používá v kombinaci s procházením účtu, cesty neobsahují název kontejneru.

**--vyloučit-vzorové** soubory vyloučení, kde název odpovídá seznamu vzorů. Například: \*. jpg; \*. PDF; Exact

**-h,--** Help Help pro synchronizaci

**--include-** Attribute String (pouze Windows) obsahují pouze soubory, jejichž atributy odpovídají seznamu atributů. Příklad: A; Pracují Í

**--include – řetězec vzorů** zahrnuje pouze soubory, u kterých se název shoduje se seznamem vzorů. Například: \*. jpg; \*. PDF; Exact

**--řetězec na úrovni protokolu** definuje podrobnosti protokolu pro soubor protokolu, dostupné úrovně: informace (všechny žádosti a odpovědi), upozornění (pomalé odezvy), chybu (pouze neúspěšné žádosti) a žádné (žádné protokoly výstupu). (výchozí informace). (výchozí "informace")

**--Put-MD5**                     Vytvořte hodnotu hash MD5 každého souboru a uložte hodnotu hash jako vlastnost content-MD5 cílového objektu BLOB nebo souboru. (Ve výchozím nastavení není hodnota hash vytvořena.) K dispozici pouze při nahrávání.

**--rekurzivní**                   Ve výchozím nastavení platí, že při synchronizaci mezi adresáři se rekurzivně vyhledávají podadresáře. (výchozí hodnota true). (výchozí hodnota true)

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

|Možnost|Popis|
|---|---|
|--Cap – Mbps|Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.|
|--výstupní řetězec typu|Formát výstupu příkazu Mezi možnosti patří: text, JSON. Výchozí hodnota je "text".|

## <a name="see-also"></a>Viz také

- [azcopy](storage-ref-azcopy.md)
