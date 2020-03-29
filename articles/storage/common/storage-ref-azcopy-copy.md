---
title: azkopie| Dokumenty společnosti Microsoft
description: Tento článek obsahuje referenční informace pro příkaz azcopy copy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 431372b930269c3dfa6bdc6e8b2fe4d291a8162e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78933782"
---
# <a name="azcopy-copy"></a>azcopy copy

Zkopíruje zdrojová data do cílového umístění.

## <a name="synopsis"></a>Synopse

Zkopíruje zdrojová data do cílového umístění. Podporované pokyny jsou:

  - místní < > objektblob Azure (ověřování SAS nebo OAuth)
  - místní soubory Azure > < (ověřování SAS sdílení/adresářů)
  - místní < > ADLS Gen 2 (ověřování SAS, OAuth nebo SharedKey)
  - Objekt blob Azure (SAS nebo veřejné) -> objektblob Azure (ověřování SAS nebo OAuth)
  - Objekt blob Azure (SAS nebo veřejné) -> soubory Azure (SAS)
  - Soubory Azure (SAS) –> soubory Azure (SAS)
  - Soubory Azure (SAS) -> objekt blob Azure (ověřování SAS nebo OAuth)
  - AWS S3 (přístupový klíč) -> objektblob bloku Azure (ověřování SAS nebo OAuth)

Další informace naleznete v příkladech.

## <a name="related-conceptual-articles"></a>Související koncepční články

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)
- [Přenos dat pomocí úložiště AzCopy a Blob](storage-use-azcopy-blobs.md)
- [Přenos dat pomocí AzCopy a ukládání souborů](storage-use-azcopy-files.md)
- [Konfigurace, optimalizace a řešení potíží s azcopy](storage-use-azcopy-configure.md)

## <a name="advanced"></a>Upřesnit

AzCopy automaticky detekuje typ obsahu souborů při nahrávání z místního disku na základě přípony souboru nebo obsahu (pokud není zadána žádná přípona).

Vestavěná vyhledávací tabulka je malá, ale na Unixu je rozšířena o soubory mime.types místního systému, pokud jsou k dispozici pod jedním nebo více z těchto názvů:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

V systému Windows jsou typy MIME extrahovány z registru. Tuto funkci lze vypnout pomocí příznaku. Viz sekce s vlajkami.

Pokud nastavíte proměnnou prostředí pomocí příkazového řádku, bude tato proměnná čitelná v historii příkazového řádku. Zvažte vymazání proměnných, které obsahují pověření z historie příkazového řádku. Chcete-li zabránit zobrazení proměnných v historii, můžete pomocí skriptu vyzvat uživatele k zadání pověření a nastavit proměnnou prostředí.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Příklady

Nahrajte jeden soubor pomocí ověřování OAuth. Pokud jste se ještě nepřihlásili k AzCopy, spusťte před spuštěním následujícího příkazu příkaz přihlášení azcopy.

- azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"

Stejné jako výše, ale tentokrát také vypočítat Hash MD5 obsahu souboru a uložit jako vlastnost content-MD5 objektu blob:

- azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5

Nahrajte jeden soubor pomocí tokenu SAS:

- azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]? [SAS]"

Nahrajte jeden soubor pomocí tokenu SAS a potrubí (pouze objekty BLOB bloku):
  
- kočka "/cesta/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]? [SAS]"

Nahrajte celý adresář pomocí tokenu SAS:
  
- azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" --recursive=true

– nebo –

- azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" --recursive=true --put-md5

Nahrajte sadu souborů pomocí tokenu SAS a zástupných znaků (*):

- azcopy cp "/path/*foo/* bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]? [SAS]"

Nahrajte soubory a adresáře pomocí tokenu SAS a zástupných znaků (*):

- azcopy cp "/path/*foo/* bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" --recursive=true

Stáhněte si jeden soubor pomocí ověřování OAuth. Pokud jste se ještě nepřihlásili k AzCopy, spusťte před spuštěním následujícího příkazu příkaz přihlášení azcopy.

- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"

Stáhněte si jeden soubor pomocí tokenu SAS:

- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]? [SAS]" "/cesta/do/file.txt"

Stáhněte si jeden soubor pomocí tokenu SAS a potom potrubí výstup do souboru (pouze objekty BLOB bloku):
  
- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]? [SAS]" > "/cesta/do/file.txt"

Stáhněte si celý adresář pomocí tokenu SAS:
  
- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" "/cesta/do/dir" --rekurzivní=true

Poznámka o použití zástupný znak (*) v adresách URL:

Existují pouze dva podporované způsoby použití zástupný znak v adrese URL. 

- Můžete použít jeden těsně po konečném lomítko (/) adresy URL. Tím zkopírujete všechny soubory v adresáři přímo do cíle bez jejich umístění do podadresáře.

- Můžete také použít jeden v názvu kontejneru tak dlouho, dokud adresa URL odkazuje pouze na kontejner a nikoli na objekt blob. Tento přístup můžete použít k získání souborů z podmnožiny kontejnerů.

Stáhněte si obsah adresáře bez kopírování samotného obsahujícího adresáře.

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/folder]/*? [SAS]" "/cesta/do/dir"

Stáhněte si celý účet úložiště.

- azcopy cp "https://[srcaccount].blob.core.windows.net/" "/path/to/dir" --rekurzivní

Stáhněte si podmnožinu kontejnerů v rámci účtu úložiště pomocí zástupného symbolu (*) v názvu kontejneru.

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container*name]" "/path/to/dir" --rekurzivní

Zkopírujte jeden objekt blob do jiného objektu blob pomocí tokenu SAS.

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]? [SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]? [SAS]"

Zkopírujte jeden objekt blob do jiného objektu blob pomocí tokenu SAS a tokenu OAuth. Musíte použít token SAS na konci adresy URL zdrojového účtu, ale cílový účet nepotřebuje, pokud se přihlásíte do AzCopy pomocí příkazu azcopy login. 

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]? [SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"

Zkopírujte jeden virtuální adresář objektu blob do jiného pomocí tokenu SAS:

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" --recursive=true

Zkopírujte všechny kontejnery objektů blob, adresáře a objekty BLOB z účtu úložiště do jiného pomocí tokenu SAS:

- azcopy cp "https://[srcaccount].blob.core.windows.net? [SAS]" "https://[destaccount].blob.core.windows.net? [SAS]" --recursive=true

Zkopírujte jeden objekt do úložiště objektů blob z Amazon Web Services (AWS) S3 pomocí přístupového klíče a tokenu SAS. Nejprve nastavte proměnnou prostředí AWS_ACCESS_KEY_ID a AWS_SECRET_ACCESS_KEY pro zdroj AWS S3.
  
- azcopy cphttps://s3.amazonaws.com/" [bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]? [SAS]"

Zkopírujte celý adresář do úložiště objektů Blob z AWS S3 pomocí přístupového klíče a tokenu SAS. Nejprve nastavte proměnnou prostředí AWS_ACCESS_KEY_ID a AWS_SECRET_ACCESS_KEY pro zdroj AWS S3.

- azcopy cphttps://s3.amazonaws.com/" [bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" --recursive=true

Chcete-li https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html lépe porozumět zástupnému symbolu [složka], přečtěte si odkaz.

Zkopírujte všechny kontejnery do úložiště objektů Blob z Amazon Web Services (AWS) pomocí přístupového klíče a tokenu SAS. Nejprve nastavte proměnnou prostředí AWS_ACCESS_KEY_ID a AWS_SECRET_ACCESS_KEY pro zdroj AWS S3.

- azcopy cphttps://s3.amazonaws.com/" " "https://[destaccount].blob.core.windows.net? [SAS]" --recursive=true

Zkopírujte všechny kontejnery do úložiště objektů Blob z oblasti Amazon Web Services (AWS) pomocí přístupového klíče a tokenu SAS. Nejprve nastavte proměnnou prostředí AWS_ACCESS_KEY_ID a AWS_SECRET_ACCESS_KEY pro zdroj AWS S3.

- azcopy cphttps://s3-" [region].amazonaws.com/" "https://[destaccount].blob.core.windows.net? [SAS]" --recursive=true

Zkopírujte podmnožinu bloků pomocí zástupného symbolu (*) v názvu bloku. Stejně jako v předchozích příkladech budete potřebovat přístupový klíč a token SAS. Ujistěte se, že nastavení proměnné prostředí AWS_ACCESS_KEY_ID a AWS_SECRET_ACCESS_KEY pro zdroj AWS S3.

- azcopy cphttps://s3.amazonaws.com/" [bucket*name]/" "https://[destaccount].blob.core.windows.net? [SAS]" --recursive=true

## <a name="options"></a>Možnosti

**--řetězec typu objektu blob** Definuje typ objektu blob v cílovém umístění. Používá se pro nahrávání objektů BLOB a při kopírování mezi účty (výchozí "Rozpoznat"). Mezi platné hodnoty patří "Detect", 'BlockBlob', 'PageBlob' a 'AppendBlob'. Při kopírování mezi účty, hodnota 'Detect' způsobí, že AzCopy použít typ zdrojového objektu blob k určení typu cílového objektu blob. Při nahrávání souboru určuje ,Detect' zda je soubor VHD nebo VHDX soubor založený na příponě souboru. Pokud je soubor ether VHD nebo VHDX soubor, AzCopy zachází se souborem jako objekt blob stránky. (výchozí "Detect")

**--blok-blob-tier** řetězec Nahrát objekty blob bloku přímo na [úroveň přístupu](../blobs/storage-blob-storage-tiers.md) podle vašeho výběru. (výchozí "Žádné"). Mezi platné hodnoty patří "Žádné", "Horké", "Cool" a "Archivovat". Pokud je předána žádná nebo žádná úroveň, objekt blob zdědí úroveň účtu úložiště.

**--blok-size-mb** float Použijte tuto velikost bloku (zadanou v MiB) při nahrávání do Služby Azure Storage a stahování z Azure Storage. Výchozí hodnota se automaticky vypočítá na základě velikosti souboru. Desetinné zlomky jsou povoleny (například: 0,25).

**--řetězec řízení mezipaměti** Nastavte hlavičku řízení mezipaměti. Vráceno ke stažení.

**--délka šeku**                         Zkontrolujte délku souboru v cíli po přenosu. Pokud dojde k neshodě mezi zdrojem a cílem, je přenos označen jako neúspěšný. (výchozí hodnota true)

**--check-md5** string Určuje, jak přísně mají být hashe MD5 při stahování ověřeny. K dispozici pouze při stahování. Dostupné možnosti: NoCheck, LogOnly, FailIfDifferent, FailIfDifferentOrMissing. (výchozí "FailIfDifferent")

**--řetězec content-dispozice** Nastavte hlavičku content-disposition. Vráceno ke stažení.

**--řetězec kódování obsahu** Nastavte hlavičku kódování obsahu. Vráceno ke stažení.

**--řetězec jazyka obsahu** Nastavte záhlaví jazyka obsahu. Vráceno ke stažení.

**--řetězec typu obsahu** Určuje typ obsahu souboru. Implikuje ne-hádat-mim-typ. Vráceno ke stažení.

**--dekomprese**                           Automaticky dekomprimovat soubory při stahování, pokud jejich kódování obsahu označuje, že jsou komprimovány. Podporované hodnoty kódování obsahu jsou 'gzip' a 'deflate'. Přípony souborů '.gz'/'.gzip' nebo '.zz' nejsou nutné, ale budou odstraněny, pokud jsou k dispozici.

**--exclude-attributes** string (pouze windows) Vyloučit soubory, jejichž atributy odpovídají seznamu atributů. Například: A; S; R

**--exclude-blob-type** string Volitelně určuje typ objektu blob (BlockBlob/ PageBlob/ AppendBlob), který má být vyloučen při kopírování objektů BLOB z kontejneru nebo účtu. Použití tohoto příznaku se nevztahuje na kopírování dat z jiné služby Azure do služby. Více než jeden objekt blob by měl být oddělen ';'.

**--exclude-path** řetězec Vyloučit tyto cesty při kopírování. Tato možnost nepodporuje zástupné znaky (*). Zkontroluje předponu relativní cesty(Například: myFolder;myFolder/subDirName/file.pdf). Při použití v kombinaci s průchodem účtu cesty neobsahují název kontejneru.

**--exclude-pattern** Vylučte tyto soubory při kopírování. Tato možnost podporuje zástupné znaky (*)

**--follow-symlinks --follow-symlinks --follow-symlinks --follow**                      Při nahrávání z místního systému souborů postupujte podle symbolických odkazů.

**--from-to** string Volitelně určuje kombinaci zdrojového cíle. Příklad: LocalBlob, BlobLocal, LocalBlobFS.

**-h, --nápověda** ke kopírování

**--include-attributes** string (pouze windows) Zahrnout soubory, jejichž atributy odpovídají seznamu atributů. Například: A; S; R

**--include-path** string Zahrnout pouze tyto cesty při kopírování. Tato možnost nepodporuje zástupné znaky (*). Zkontroluje předponu relativní cesty (například: myFolder;myFolder/subDirName/file.pdf).

**--include-pattern** Zahrnuje při kopírování pouze tyto soubory. Tato možnost podporuje zástupné znaky (*). Oddělte soubory pomocí ';'.

**--řetězec na úrovni protokolu** Definujte podrobnost protokolu pro soubor protokolu, dostupné úrovně: INFO(všechny požadavky/odpovědi), UPOZORNĚNÍ(pomalé odpovědi), ERROR(pouze neúspěšné požadavky) a NONE(žádné výstupní protokoly). (výchozí "INFO")

**--řetězec metadat** Nahrát do Služby Azure S těmito páry klíč hodnota jako metadata.

**--no-guess-mime-type --no-guess-mime-type --no-guess-mime-type --no**                   Zabrání azCopy v detekci typu obsahu na základě rozšíření nebo obsahu souboru.

**--přepsat** řetězec Přepsat konfliktní soubory a objekty BLOB v cílovém umístění, pokud je tento příznak nastaven na hodnotu true. Možné hodnoty zahrnují 'true', 'false', 'ifSourceNewer' a 'prompt'. (výchozí "true")

**--řetězec page-blob-tier** Nahrajte objekt blob stránky do Služby Azure Storage pomocí této vrstvy objektu blob. (výchozí "Žádné")

**--zachovat-poslední-upravený-čas**          K dispozici pouze v případě, že cílem je systém souborů.

**--put-md5**                             Vytvořte hash MD5 každého souboru a uložte hash jako Content-MD5 vlastnost cílového objektu blob nebo souboru. (Ve výchozím nastavení není hodnota hash vytvořena.) K dispozici pouze při nahrávání.

**--rekurzivní**                            Při nahrávání z místního systému souborů se rekurzivně podívejte do podadresářů.

**--s2s-detect-source-changed --s2s-detect-source-changed --s2s-detect-source-changed --s**           Zkontrolujte, zda se zdroj po výčtu změnil.

**--s2s-handle-invalid-metadata** řetězec Určuje způsob zpracování neplatných klíčů metadat. Dostupné možnosti: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (výchozí "ExcludeIfInvalid")

**--s2s-preserve-access-tier**             Zachovat úroveň přístupu během služby ke službě kopie. Přečtěte si prosím [úložiště objektů Blob Azure: horké, studené a archivní úrovně přístupu,](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) abyste zajistili, že cílový účet úložiště podporuje nastavení úrovně přístupu. V případě, že nastavení úrovně přístupu není podporováno, použijte s2sPreserveAccessTier=false, abyste obejili úroveň přístupu kopírování. (výchozí hodnota true)

**--s2s-preserve-properties --s2s-preserve-properties --s2s-preserve-properties --s**              Zachovat úplné vlastnosti během služby služby kopírování. Pro AWS S3 a Azure File non-single zdroj souboru, operace seznamu nevrátí úplné vlastnosti objektů a souborů. Chcete-li zachovat úplné vlastnosti, AzCopy musí odeslat jeden další požadavek na objekt nebo soubor. (výchozí hodnota true)

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

**--cap-mbps uint32**      Završuje přenosovou rychlost v megabitech za sekundu. Propustnost se může mírně lišit od víčka. Pokud je tato možnost nastavena na nulu nebo je vynechána, propustnost není omezena.

**--řetězec typu výstupu** Formát výstupu příkazu. Volby zahrnují: text, json. Výchozí hodnota je text. (výchozí "text")

## <a name="see-also"></a>Viz také

- [azkopie](storage-ref-azcopy.md)
