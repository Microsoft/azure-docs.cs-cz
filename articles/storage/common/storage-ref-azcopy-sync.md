---
title: azcopy synchronizace | Dokumenty společnosti Microsoft
description: Tento článek obsahuje referenční informace pro příkaz azcopy sync.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 1bff46c8584934ab8bcffce74763edc8363533d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988239"
---
# <a name="azcopy-sync"></a>azcopy sync

Replikuje zdrojové umístění do cílového umístění.

## <a name="synopsis"></a>Synopse

Poslední upravené časy se používají pro porovnání. Soubor je přeskočen, pokud je poslední změněný čas v cíli novější.

Podporované dvojice jsou:

- místní < > objektblob Azure (lze použít ověřování SAS nebo OAuth)
- Azure Blob < > objektblob Azure (Zdroj musí obsahovat SAS nebo je veřejně přístupný; ověřování SAS nebo OAuth lze použít pro cíl)
- Soubor Azure < > soubor Azure (Zdroj musí obsahovat SAS nebo je veřejně přístupný; Ověřování SAS by mělo být použito pro cíl)

Příkaz synchronizace se od příkazu copy liší několika způsoby:

1. Ve výchozím nastavení je rekurzivní příznak pravdivý a synchronizace zkopíruje všechny podadresáře. Synchronizace zkopíruje soubory nejvyšší úrovně v adresáři pouze v případě, že je rekurzivní příznak nepravdivý.
2. Při synchronizaci mezi virtuálními adresáři přidejte do cesty koncové lomítko (viz příklady), pokud existuje objekt blob se stejným názvem jako jeden z virtuálních adresářů.
3. Pokud je příznak deleteDestination nastaven na hodnotu true nebo prompt, synchronizace odstraní soubory a objekty BLOB v cílovém umístění, které nejsou k dispozici ve zdroji.

## <a name="related-conceptual-articles"></a>Související koncepční články

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)
- [Přenos dat pomocí úložiště AzCopy a Blob](storage-use-azcopy-blobs.md)
- [Přenos dat pomocí AzCopy a ukládání souborů](storage-use-azcopy-files.md)
- [Konfigurace, optimalizace a řešení potíží s azcopy](storage-use-azcopy-configure.md)

### <a name="advanced"></a>Upřesnit

Pokud nezadáte příponu souboru, AzCopy automaticky detekuje typ obsahu souborů při nahrávání z místního disku na základě přípony souboru nebo obsahu (pokud není zadána žádná přípona).

Vestavěná vyhledávací tabulka je malá, ale na Unixu je rozšířena o soubory mime.types místního systému, pokud jsou k dispozici pod jedním nebo více z těchto názvů:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

V systému Windows jsou typy MIME extrahovány z registru.

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>Příklady

Synchronizace jednoho souboru:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Stejné jako výše, ale tentokrát také vypočítat Hash MD5 obsahu souboru a uložit jako vlastnost content-MD5 objektu blob:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Synchronizace celého adresáře včetně jeho podadresářů (všimněte si, že rekurzivní je ve výchozím nastavení zapnutá):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

– nebo –

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Synchronizujte pouze horní soubory uvnitř adresáře, ale ne jeho podadresáře:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Synchronizace podmnožinu souborů v adresáři (například: pouze jpg a pdf soubory, nebo pokud je název souboru "exactName"):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include="*.jpg;*.pdf;exactName"
```

Synchronizujte celý adresář, ale vylučte určité soubory z oboru (například: každý soubor, který začíná foo nebo končí pruhem):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude="foo*;*bar"
```

Synchronizace jednoho objektu blob:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Synchronizace virtuálního adresáře:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

Synchronizace virtuálního adresáře, který má stejný název jako objekt blob (přidejte do cesty koncové lomítko, aby bylo možné se rozptýlit):

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Synchronizace adresáře souboru Azure (stejná syntaxe jako objekt Blob):

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> Pokud zahrnout nebo vyloučit příznaky jsou používány společně, pouze soubory odpovídající include vzory by se podíval na, ale ty odpovídající vzory vyloučit by být vždy ignorovány.

## <a name="options"></a>Možnosti

**--blok-size-mb** float Při nahrávání do Azure Storage nebo stahování z Azure Storage použijte tuto velikost bloku (zadanou v MiB). Výchozí nastavení se automaticky vypočítá na základě velikosti souboru. Desetinné zlomky jsou povoleny (například: 0,25).

**--check-md5** string Určuje, jak přísně mají být hashe MD5 při stahování ověřeny. Tato možnost je k dispozici pouze při stahování. Dostupné hodnoty zahrnují: NoCheck, LogOnly, FailIfDifferent, FailIfDifferentOrMissing. (výchozí 'FailIfDifferent'). (výchozí "FailIfDifferent")

**--delete-destination** string Definuje, zda má být odstraněno další soubory z cíle, které nejsou k dispozici u zdroje. Může být nastavena na true, false, nebo prompt. Pokud je nastavena výzva, uživatel bude dotázán na otázku před plánováním souborů a objektů BLOB k odstranění. (výchozí 'false'). (výchozí "false")

**--exclude-attributes** string (pouze windows) Vyloučit soubory, jejichž atributy odpovídají seznamu atributů. Například: A; S; R

**--exclude-path** řetězec Vyloučit tyto cesty při kopírování. Tato možnost nepodporuje zástupné znaky (*). Zkontroluje předponu relativní cesty(Například: myFolder;myFolder/subDirName/file.pdf). Při použití v kombinaci s průchodem účtu cesty neobsahují název kontejneru.

**--exclude-pattern** Vylučte soubory, kde se název shoduje se seznamem vzorů. Například: \*.jpg; \*.pdf;exactName

**-h, --nápověda** pro synchronizaci

**--include-attributes** string (pouze windows) Zahrnout pouze soubory, jejichž atributy odpovídají seznamu atributů. Například: A; S; R

**--include-pattern** Include only files where the name matches the pattern. Například: \*.jpg; \*.pdf;exactName

**--řetězec na úrovni protokolu** Definujte podrobnost protokolu pro soubor protokolu, dostupné úrovně: INFO(všechny požadavky a odpovědi), UPOZORNĚNÍ(pomalé odpovědi), ERROR(pouze neúspěšné požadavky) a NONE(žádné výstupní protokoly). (výchozí INFO). (výchozí "INFO")

**--put-md5**                     Vytvořte hash MD5 každého souboru a uložte hash jako Content-MD5 vlastnost cílového objektu blob nebo souboru. (Ve výchozím nastavení není hodnota hash vytvořena.) K dispozici pouze při nahrávání.

**--rekurzivní**                   Pravda ve výchozím nastavení, podívejte se do podadresářů rekurzivně při synchronizaci mezi adresáři. (výchozí hodnota true). (výchozí hodnota true)

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

|Možnost|Popis|
|---|---|
|--cap-mbps uint32|Završuje přenosovou rychlost v megabitech za sekundu. Propustnost se může mírně lišit od víčka. Pokud je tato možnost nastavena na nulu nebo je vynechána, propustnost není omezena.|
|--řetězec výstupního typu|Formát výstupu příkazu. Volby zahrnují: text, json. Výchozí hodnota je "text".|

## <a name="see-also"></a>Viz také

- [azkopie](storage-ref-azcopy.md)
