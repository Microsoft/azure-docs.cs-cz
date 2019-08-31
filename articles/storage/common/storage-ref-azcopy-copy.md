---
title: AzCopy kopii | Microsoft Docs
description: Tento článek poskytuje referenční informace pro příkaz AzCopy Copy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c15d188e333bea5e74fa65d2bbdf38ae7fadc246
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195734"
---
# <a name="azcopy-copy"></a>AzCopy kopii

Zkopíruje zdrojová data do cílového umístění.

## <a name="synopsis"></a>Stručný obsah

Podporovány jsou následující pokyny:

- místní < – > Azure Blob (ověřování SAS nebo OAuth)
- místní < – > soubor Azure (ověřování pomocí SAS sdílené složky/adresáře)
- místní <-> ADLS obecná 2 (ověřování SAS, OAuth nebo SharedKey)
- Azure Blob (SAS nebo Public) <-> Azure Blob (ověřování SAS nebo OAuth)
- Soubor Azure (SAS) – > Azure Block BLOB (ověřování SAS nebo OAuth)
- AWS S3 (přístupový klíč) – > Azure Block BLOB (ověřování SAS nebo OAuth)

Další informace najdete v příkladech.

### <a name="advanced"></a>Upřesnit

AzCopy automaticky detekuje typ obsahu souborů při nahrávání z místního disku na základě přípony souboru nebo obsahu (Pokud není zadáno žádné rozšíření).

Integrovaná vyhledávací tabulka je malá, ale v systému UNIX je rozšířena o soubory MIME. Types v místním systému, pokud jsou k dispozici v jednom nebo více těchto názvech:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

V systému Windows jsou z registru extrahovány typy MIME. Tuto funkci můžete vypnout pomocí příznaku. Přečtěte si část příznak.

> [!IMPORTANT]
> Pokud nastavíte proměnnou prostředí pomocí příkazového řádku, bude tato proměnná čitelná v historii příkazového řádku. Zvažte vymazání proměnných, které obsahují pověření z historie příkazového řádku. Chcete-li zabránit zobrazování proměnných ve vaší historii, můžete použít skript, který uživateli vyzve k zadání přihlašovacích údajů a k nastavení proměnné prostředí.

```azcopy
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Příklady

Nahrajte jeden soubor pomocí ověřování OAuth.

Pokud jste se ještě přihlásili k AzCopy, `azcopy login` použijte prosím příkaz před spuštěním následujícího příkazu.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Stejné jako výše, ale tentokrát také vypočítat hodnotu hash MD5 obsahu souboru a uložit ji jako vlastnost content-MD5 objektu BLOB:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Nahrajte jeden soubor s SAS:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Nahrát jeden soubor s SAS pomocí potrubního (jenom objekty blob bloku):

```azcopy
cat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Nahrajte celý adresář pomocí SAS:

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

or

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --put-md5
```

Nahrajte sadu souborů pomocí SAS pomocí zástupných znaků:

```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

Nahrávání souborů a adresářů pomocí SAS pomocí zástupných znaků:

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Pomocí ověřování OAuth Stáhněte jeden soubor.

Pokud jste se ještě přihlásili k AzCopy, `azcopy login` použijte prosím příkaz před spuštěním následujícího příkazu.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

Stažení jednoho souboru s SAS:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

Stažení jednoho souboru s SAS pomocí potrubního (jenom objekty blob bloku):

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
```

Stáhněte si celý adresář s SAS:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive=true
```

Stažení sady souborů s SAS pomocí zástupných znaků:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/foo*?[SAS]" "/path/to/dir"
```

Stažení souborů a adresářů pomocí SAS pomocí zástupných znaků:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/foo*?[SAS]" "/path/to/dir" --recursive=true
```

Zkopírujte jeden objekt BLOB s SAS do jiného objektu BLOB s SAS:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Zkopírujte jeden objekt BLOB s SAS do jiného objektu BLOB s tokenem OAuth.

Pokud jste se ještě přihlásili k AzCopy, `azcopy login` použijte prosím příkaz před spuštěním následujícího příkazu. Token OAuth se používá pro přístup k cílovému účtu úložiště.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

Zkopírujte celý adresář z virtuálního adresáře objektu BLOB s SAS do jiného virtuálního adresáře objektu BLOB s SAS:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Zkopírujte celá data účtu z účtu BLOB s SAS do jiného účtu BLOB s SAS:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

Zkopírujte jeden objekt ze S3 pomocí přístupového klíče k objektu BLOB pomocí SAS:

Nastavte proměnnou prostředí AWS_ACCESS_KEY_ID a AWS_SECRET_ACCESS_KEY pro zdroj S3.

```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Zkopírujte celý adresář ze S3 pomocí přístupového klíče do virtuálního adresáře BLOB pomocí SAS:

```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Informace https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html o tom, co [Folder] znamená pro S3, najdete v tématu. Nastavte proměnnou prostředí AWS_ACCESS_KEY_ID a AWS_SECRET_ACCESS_KEY pro zdroj S3.

Kopírovat všechny intervaly ve službě S3 pomocí přístupového klíče k účtu BLOB s SAS:

Nastavte proměnnou prostředí AWS_ACCESS_KEY_ID a AWS_SECRET_ACCESS_KEY pro zdroj S3.

```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

Zkopírujte všechny intervaly v oblasti S3 s přístupovým klíčem k účtu BLOB pomocí SAS:

```azcopy
azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

Nastavte proměnnou prostředí AWS_ACCESS_KEY_ID a AWS_SECRET_ACCESS_KEY pro zdroj S3.

## <a name="options"></a>Možnosti

|Možnost|Popis|
|--|--|
|--blob – řetězec typu|Definuje typ objektu BLOB v cíli. Používá se k nahrávání objektů BLOB a při kopírování mezi účty (výchozí hodnota "none").|
|--Block-– řetězec vrstvy BLOB|Nahrajte objekt blob bloku pro Azure Storage pomocí této úrovně objektu BLOB. (výchozí hodnota je "none").|
|--Block-size-MB float |Při nahrávání do Azure Storage použít tuto velikost bloku (určenou v souboru MiB) a stáhnout z Azure Storage. Výchozí hodnota se automaticky vypočítá na základě velikosti souboru. Jsou povoleny desetinné zlomky (například: 0,25).|
|--řetězec řízení mezipaměti|Nastavte hlavičku Cache-Control. Vráceno při stažení.|
|--check-MD5 – řetězec|Určuje, jak mají být při stahování ověřovány striktní hodnoty hash MD5. K dispozici pouze při stahování. Dostupné možnosti: Nekontrolujte, přihlaste se, FailIfDifferent, FailIfDifferentOrMissing. (výchozí "FailIfDifferent")|
|--Dispoziční řetězec obsahu|Nastavte hlavičku Content-Disposition. Vráceno při stažení.|
|--Content-Encoding řetězec|Nastavte hlavičku kódování obsahu. Vráceno při stažení.|
|--Content-Language-řetězec|Nastaví hlavičku Content-Language. Vráceno při stažení.|
|--typ obsahu – řetězec |Určuje typ obsahu souboru. Implikuje typ No-odhad-MIME-Type. Vráceno při stažení.|
|--vyloučit řetězec|Vylučte tyto soubory při kopírování. Podpora použití *.|
|--Exclude-BLOB – řetězec typu|Volitelně určuje typ objektu BLOB (BlockBlob/PageBlob/AppendBlob), který se má vyloučit při kopírování objektů BLOB z kontejneru nebo účtu. Použití tohoto příznaku se nedá použít pro kopírování dat ze služby mimo Azure. Více než jeden objekt BLOB by měl být oddělený středníkem (;).|
|--sledovat – symbolických odkazů|Při nahrávání z místního systému souborů Sledujte symbolické odkazy.|
|--z-k řetězci|Volitelně určuje kombinaci zdrojového cíle. Příklad: LocalBlob, BlobLocal, LocalBlobFS.|
|-h,--help|Zobrazí obsah nápovědu pro příkaz Kopírovat. |
|--řetězec na úrovni protokolu|Zadejte podrobnosti protokolu pro soubor protokolu, dostupné úrovně: INFORMACE (všechny žádosti a odpovědi), upozornění (pomalé odezvy), chyba (pouze neúspěšné žádosti) a žádné (žádné výstupní protokoly). (výchozí "informace").|
|--řetězec metadat|Nahrajte do Azure Storage s těmito páry klíč-hodnota jako metadata.|
|--No-odhad-MIME-Type|Zabrání AzCopy zjištění typu obsahu založeného na příponě nebo obsahu souboru.|
|--přepsat|Pokud je tento příznak nastaven na hodnotu true, přepište konfliktní soubory nebo objekty BLOB v cíli. (výchozí hodnota true).|
|--Page-BLOB – řetězec vrstvy |Nahrajte objekt blob stránky do Azure Storage pomocí této úrovně objektu BLOB. (výchozí hodnota je "none").|
|--Preserve – čas poslední změny|K dispozici pouze v případě, že cílem je systém souborů.|
|--Put-MD5|Vytvořte hodnotu hash MD5 každého souboru a uložte hodnotu hash jako vlastnost content-MD5 cílového objektu BLOB nebo souboru. (Ve výchozím nastavení není hodnota hash vytvořena.) K dispozici pouze při nahrávání.|
|--rekurzivní|Při nahrávání z místního systému souborů se budou rekurzivně zobrazovat podadresáře.|
|--S2S-Detect-source-změněno|Zkontroluje, jestli se zdroj po vyčíslení změnil. Pro kopii S2S je jako zdroj vzdáleným prostředkem ověření, jestli se zdroj změnil o náklady na další požadavky.|
|--S2S-Handle-neplatný – řetězec metadat |Určuje způsob zpracování neplatných klíčů metadat. Dostupné možnosti: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (výchozí "ExcludeIfInvalid").|
|--S2S-Preserve-úroveň přístupu|Zachovat úroveň přístupu během kopírování z provozu do služby Pokud chcete zajistit, aby cílový účet úložiště podporoval nastavení úrovně přístupu, přečtěte si prosím [v Azure Blob Storage: horká, studená a archivní úroveň přístupu](../blobs/storage-blob-storage-tiers.md) . V případech, kdy se nastavení úrovně přístupu nepodporuje, použijte s2sPreserveAccessTier = false, aby se přenechá kopírování úrovně přístupu.  (výchozí hodnota true).|
|--S2S-Preserve-Properties|Zachovat úplné vlastnosti během kopírování Service to Service. V případě nesamostatného zdroje souborů S3 a Azure File nevrátí operace se seznamem úplné vlastnosti objektů a souborů, aby se zachovaly úplné vlastnosti, AzCopy musí odeslat jednu další žádost na objekt a soubor. (výchozí hodnota true).|

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

|Možnost|Popis|
|---|---|
|--Cap – Mbps|Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.|
|--výstupní řetězec typu|Formát výstupu příkazu Mezi možnosti patří: text, JSON. Výchozí hodnota je "text".|

## <a name="see-also"></a>Viz také:

- [AzCopy](storage-ref-azcopy.md)
