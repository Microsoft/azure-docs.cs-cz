---
title: AzCopy synchronizaci | Microsoft Docs
description: Tento článek popisuje referenční informace o příkazu AzCopy Sync.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: dc3451a4b46a317dccda0e4292dcb1712b4171f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98878303"
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
3. Pokud `deleteDestination` je příznak nastaven na hodnotu true nebo se zobrazí výzva, synchronizace odstraní soubory a objekty BLOB v cílovém umístění, které se nenacházejí ve zdroji.

## <a name="related-conceptual-articles"></a>Související koncepční články

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)
- [Přenos dat pomocí AzCopy a BLOB Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Přenos dat s použitím AzCopy a úložiště souborů](storage-use-azcopy-files.md)
- [Konfigurace, optimalizace a řešení potíží s AzCopy](storage-use-azcopy-configure.md)

### <a name="advanced"></a>Pokročilý

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

Stejné jako výše, ale také vypočítají hash MD5 obsahu souboru a pak tuto hodnotu hash MD5 uložte jako vlastnost content-MD5 objektu BLOB. 

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Synchronizovat celý adresář včetně jeho podadresářů (Všimněte si, že ve výchozím nastavení je rekurzivní):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

nebo

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Synchronizovat pouze soubory v adresáři, ale ne podadresáře nebo soubory v podadresářích:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Synchronizace podmnožiny souborů v adresáři (například: pouze soubory jpg a PDF nebo název souboru `exactName` ):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include-pattern="*.jpg;*.pdf;exactName"
```

Synchronizuje celý adresář, ale vyloučí určité soubory z oboru (například každý soubor, který začíná foo nebo končí na panelu):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude-pattern="foo*;*bar"
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

**--Block-size-MB** float při nahrávání do Azure Storage nebo stahování z Azure Storage použít tuto velikost bloku (zadanou v souboru MIB). Výchozí hodnota se automaticky vypočítá na základě velikosti souboru. Jsou povoleny desetinné zlomky (například: `0.25` ).

**--check-MD5** řetězec Určuje, jak by měly být při stahování ověřovány STRIKTNĚ hash MD5. Tato možnost je k dispozici pouze při stahování. K dispozici jsou tyto hodnoty: `NoCheck` , `LogOnly` , `FailIfDifferent` , `FailIfDifferentOrMissing` . (výchozí `FailIfDifferent` ). (výchozí `FailIfDifferent` )

**--Delete – cílový** řetězec definuje, jestli se mají odstranit nadbytečné soubory z cílového umístění, které se nenacházejí ve zdroji. Může být nastaveno na `true` , `false` , nebo `prompt` . Pokud je nastaveno na `prompt` , uživateli se před plánováním souborů a objektů BLOB pro odstranění zobrazí dotaz. (výchozí `false` ). (výchozí `false` )

**--Exclude – řetězec atributů** (pouze Windows) vyloučí soubory, jejichž atributy se shodují se seznamem atributů. Příklad: `A;S;R`

**--Exclude vyloučení – řetězec cesty** vyloučí tyto cesty při porovnávání zdroje s cílem. Tato možnost nepodporuje zástupné znaky (*). Kontroluje předponu relativní cesty (například: `myFolder;myFolder/subDirName/file.pdf` ).

**--vyloučit-vzorové** soubory vyloučení, kde název odpovídá seznamu vzorů. Příklad: `*.jpg;*.pdf;exactName`

**–**    Help Help pro synchronizaci.

**--include-Attributes** řetězec (pouze Windows) zahrnuje pouze soubory, jejichž atributy odpovídají seznamu atributů. Příklad: `A;S;R`

**--include – řetězec vzorů** zahrnuje pouze soubory, u kterých se název shoduje se seznamem vzorů. Příklad: `*.jpg;*.pdf;exactName`

**--řetězec na úrovni protokolu** definuje podrobnost protokolu pro soubor protokolu, dostupné úrovně: `INFO` (všechny požadavky a odpovědi) `WARNING` (pomalé odezvy), `ERROR` (pouze neúspěšné žádosti) a `NONE` (žádné protokoly výstupu). (výchozí `INFO` ). 

**--Preserve-SMB-info**   Výchozí hodnota je false. Zachovává informace o vlastnostech protokolu SMB (čas posledního zápisu, čas vytvoření, bity atributů) mezi prostředky pracujícími s protokolem SMB (soubory Windows a Azure). Tento příznak platí pro soubory i složky, pokud není zadán filtr pouze souborů (například include-Pattern). Informace přenesené pro složky jsou stejné jako u souborů, s výjimkou času posledního zápisu, který není pro složky zachované.

**--Preserve-SMB-Permissions**   Výchozí hodnota je false. Zachovává seznamy řízení přístupu SMB mezi prostředky, které podporují (soubory Windows a Azure). Tento příznak platí pro soubory i složky, pokud není zadán filtr pouze souborů (například `include-pattern` ).

**--Put-MD5**     Vytvořte hodnotu hash MD5 každého souboru a uložte hodnotu hash jako vlastnost content-MD5 cílového objektu BLOB nebo souboru. (Ve výchozím nastavení není hodnota hash vytvořena.) K dispozici pouze při nahrávání.

**--rekurzivní** `True` ve výchozím nastavení se při synchronizaci mezi adresáři vyhledávají rekurzivně v podadresářích.     (výchozí `True` ). 

**--S2S-Preserve-úroveň přístupu**  Zachovat úroveň přístupu během kopírování z provozu do služby Pokud chcete zajistit, aby cílový účet úložiště podporoval nastavení úrovně přístupu, přečtěte si téma [Azure Blob Storage: horká, studená a archivní úroveň přístupu](../blobs/storage-blob-storage-tiers.md) . V případech, kdy se nastavení úrovně přístupu nepodporuje, použijte s2sPreserveAccessTier = false, aby se přenechá kopírování úrovně přístupu. (výchozí `true` ). 

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

|Možnost|Popis|
|---|---|
|--Cap – Mbps|Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.|
|--výstupní řetězec typu|Formát výstupu příkazu Mezi možnosti patří: text, JSON. Výchozí hodnota je "text".|
|--Trusted – řetězec Microsoft-přípony   |Určuje další přípony domén, kde se můžou odesílat přihlašovací tokeny Azure Active Directory.  Výchozí hodnota je *. Core.Windows.NET;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Zde uvedené jsou přidány do výchozího nastavení. Z důvodu zabezpečení byste měli sem umístit jenom Microsoft Azure domény. Více položek oddělte středníkem.|

## <a name="see-also"></a>Viz také

- [AzCopy](storage-ref-azcopy.md)
