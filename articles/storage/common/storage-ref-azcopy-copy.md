---
title: AzCopy kopii | Microsoft Docs
description: Tento článek poskytuje referenční informace pro příkaz AzCopy Copy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 03/08/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c676b92fd07c6e444aa22f25c48fdb1b1957ca7a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103493760"
---
# <a name="azcopy-copy"></a>azcopy copy

Zkopíruje zdrojová data do cílového umístění.

## <a name="synopsis"></a>Stručný obsah

Zkopíruje zdrojová data do cílového umístění. Podporovány jsou následující pokyny:

  - místní < – > Azure Blob (ověřování SAS nebo OAuth)
  - místní soubory Azure <-> (ověřování pomocí SAS pro sdílení/adresář)
  - místní <-> Azure Data Lake Storage Gen 2 (ověřování SAS, OAuth nebo sdílený klíč)
  - Azure Blob (SAS nebo Public) – > Azure Blob (ověřování SAS nebo OAuth)
  - Azure Blob (SAS nebo Public) – > soubory Azure (SAS)
  - Soubory Azure (SAS) – > soubory Azure (SAS)
  - Soubory Azure (SAS) – > Azure Blob (ověřování SAS nebo OAuth)
  - Amazon Web Services (AWS) S3 (přístupový klíč) – > Azure Block BLOB (ověřování SAS nebo OAuth)
  - Google Cloud Storage (klíč účtu služby) – > Azure Block BLOB (ověřování SAS nebo OAuth) [Preview]

Další informace najdete v části Příklady tohoto článku.

## <a name="related-conceptual-articles"></a>Související koncepční články

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)
- [Přenos dat pomocí AzCopy a BLOB Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Přenos dat s použitím AzCopy a úložiště souborů](storage-use-azcopy-files.md)
- [Konfigurace, optimalizace a řešení potíží s AzCopy](storage-use-azcopy-configure.md)

## <a name="advanced"></a>Pokročilý

AzCopy automaticky detekuje typ obsahu souborů při jejich nahrávání z místního disku. AzCopy detekuje typ obsahu na základě přípony souboru nebo obsahu (Pokud není zadané žádné rozšíření).

Integrovaná vyhledávací tabulka je malá, ale v systému UNIX je rozšířena o soubory v místním systému, `mime.types` Pokud jsou k dispozici v jednom nebo více těchto názvech:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

V systému Windows jsou z registru extrahovány typy MIME. Tuto funkci můžete vypnout pomocí příznaku. Další informace najdete v části příznak tohoto článku.

Pokud nastavíte proměnnou prostředí pomocí příkazového řádku, bude tato proměnná čitelná v historii příkazového řádku. Zvažte vymazání proměnných, které obsahují pověření z historie příkazového řádku. Chcete-li zabránit zobrazování proměnných ve vaší historii, můžete použít skript, který uživateli vyzve k zadání přihlašovacích údajů a k nastavení proměnné prostředí.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Příklady

Nahrajte jeden soubor pomocí ověřování OAuth. Pokud jste ještě přihlášeni k AzCopy, spusťte `azcopy login` příkaz před spuštěním následujícího příkazu.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```
Stejné jako výše, ale tentokrát také vypočítat hodnotu hash MD5 obsahu souboru a uložit ji jako vlastnost content-MD5 objektu BLOB:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Odeslat jeden soubor pomocí tokenu SAS:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Odeslat jeden soubor pomocí tokenu SAS a potrubního (pouze objekty blob bloku):
  
```azcopy
cat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]
```

Nahrajte celý adresář pomocí tokenu SAS:
  
```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

nebo

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive --put-md5
```

Odeslat sadu souborů pomocí tokenu SAS a znaků zástupného znaku (*):
 
```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

Nahrávání souborů a adresářů pomocí tokenu SAS a zástupných znaků (*):

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

Nahrajte soubory a adresáře do Azure Storage účtu a nastavte v objektu BLOB značky kódované řetězce dotazu. 

- K nastavení značek {Key = "bla bla", Val = "foo"} a {Key = "bla bla 2", Val = "bar"} použijte následující syntaxi: `azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`
    
- Klíče a hodnoty jsou zakódované v adrese URL a páry klíč-hodnota jsou oddělené znakem ampersand (' & ').

- Při nastavování značek u objektů BLOB existují další oprávnění (ne pro značky) v SAS, aniž by služba poskytovala chybu autorizace zpátky.

Stažení jednoho souboru pomocí ověřování OAuth. Pokud jste ještě přihlášeni k AzCopy, spusťte `azcopy login` příkaz před spuštěním následujícího příkazu.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

Stažení jednoho souboru pomocí tokenu SAS:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

Stažení jednoho souboru pomocí tokenu SAS a následného zřetězení výstupu do souboru (pouze objekty blob bloku):
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
``` 

Stáhněte si celý adresář pomocí tokenu SAS:
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive
``` 

Poznámka o použití zástupného znaku (*) v adresách URL:

Existují dva způsoby, jak použít zástupný znak v adrese URL. 

- Můžete použít jednu hned za koncovým lomítkem (/) adresy URL. Toto použití zástupného znaku kopíruje všechny soubory v adresáři přímo do cílového umístění bez jejich umístění do podadresáře. 

- Můžete také zástupný znak v názvu kontejneru, pokud adresa URL odkazuje pouze na kontejner a nikoli na objekt BLOB. Tento přístup můžete použít k získání souborů z podmnožiny kontejnerů. 

Stažení obsahu adresáře bez kopírování samotného obsahujícího adresáře.
 
```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/folder]/*?[SAS]" "/path/to/dir"
```

Stáhněte si celý účet úložiště.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/" "/path/to/dir" --recursive
```

Pomocí zástupného znaku (*) v názvu kontejneru Stáhněte podmnožinu kontejnerů v rámci účtu úložiště.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container*name]" "/path/to/dir" --recursive
```

Zkopírujte jeden objekt blob do jiného objektu BLOB pomocí tokenu SAS.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Zkopírujte jeden objekt blob do jiného objektu BLOB pomocí tokenu SAS a ověřovacího tokenu. Na konci adresy URL zdrojového účtu musíte použít token SAS, ale cílový účet ho nepotřebuje, pokud se k AzCopy přihlašujete pomocí `azcopy login` příkazu. 

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

Zkopírování jednoho virtuálního adresáře objektu BLOB na jiný pomocí tokenu SAS:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Zkopírujte všechny kontejnery objektů blob, adresáře a objekty BLOB z účtu úložiště do jiného pomocí tokenu SAS:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Zkopírování jednoho objektu do Blob Storage ze Amazon Web Services (AWS) S3 pomocí přístupového klíče a tokenu SAS. Nejprve nastavte proměnnou prostředí `AWS_ACCESS_KEY_ID` a `AWS_SECRET_ACCESS_KEY` zdroj AWS S3.
  
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Zkopírování celého adresáře do Blob Storage z AWS S3 pomocí přístupového klíče a tokenu SAS. Nejprve nastavte proměnnou prostředí `AWS_ACCESS_KEY_ID` a `AWS_SECRET_ACCESS_KEY` zdroj AWS S3.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```
    
  Pokud https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html Chcete lépe pochopit zástupný symbol [složka], přečtěte si téma.

Zkopírujte všechny intervaly do Blob Storage z Amazon Web Services (AWS) pomocí přístupového klíče a tokenu SAS. Nejprve nastavte proměnnou prostředí `AWS_ACCESS_KEY_ID` a `AWS_SECRET_ACCESS_KEY` zdroj AWS S3.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Zkopírujte všechny intervaly do Blob Storage z oblasti Amazon Web Services (AWS) pomocí přístupového klíče a tokenu SAS. Nejprve nastavte proměnnou prostředí `AWS_ACCESS_KEY_ID` a `AWS_SECRET_ACCESS_KEY` zdroj AWS S3.
 
```azcopy
- azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Zkopírujte podmnožinu kontejnerů pomocí zástupného znaku (*) v názvu kontejneru. Stejně jako v předchozích příkladech budete potřebovat přístupový klíč a token SAS. Ujistěte se, že jste nastavili proměnnou prostředí `AWS_ACCESS_KEY_ID` a `AWS_SECRET_ACCESS_KEY` zdroj AWS S3.

```azcopy
- azcopy cp "https://s3.amazonaws.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Přenos souborů a adresářů do Azure Storage účtu a nastavení daných značek kódovaných v řetězci dotazu v objektu BLOB. 

- K nastavení značek {Key = "bla bla", Val = "foo"} a {Key = "bla bla 2", Val = "bar"} použijte následující syntaxi: `azcopy cp "https://[account].blob.core.windows.net/[source_container]/[path/to/directory]?[SAS]" "https://[account].blob.core.windows.net/[destination_container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`
        
- Klíče a hodnoty jsou zakódované v adrese URL a páry klíč-hodnota jsou oddělené znakem ampersand (' & ').
    
- Při nastavování značek u objektů BLOB existují další oprávnění (ne pro značky) v SAS, aniž by služba poskytovala chybu autorizace zpátky.

Zkopírujte jeden objekt, který se Blob Storage z Google Cloud Storage, pomocí klíče účtu služby a tokenu SAS. Nejdřív nastavte proměnnou prostředí GOOGLE_APPLICATION_CREDENTIALS pro zdroj Google Cloud Storage.
  
```azcopy
azcopy cp "https://storage.cloud.google.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Zkopírujte celý adresář pro Blob Storage z Google Cloud Storage pomocí klíče účtu služby a tokenu SAS. Nejdřív nastavte proměnnou prostředí GOOGLE_APPLICATION_CREDENTIALS pro zdroj Google Cloud Storage.
 
```azcopy
  - azcopy cp "https://storage.cloud.google.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Zkopírování celého kontejneru do Blob Storage z Google Cloud Storage pomocí klíče účtu služby a tokenu SAS. Nejdřív nastavte proměnnou prostředí GOOGLE_APPLICATION_CREDENTIALS pro zdroj Google Cloud Storage.

```azcopy 
azcopy cp "https://storage.cloud.google.com/[bucket]" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

Zkopírujte všechny množiny do Blob Storage z Google Cloud Storage pomocí klíče účtu služby a tokenu SAS. Nejprve nastavte proměnné prostředí GOOGLE_APPLICATION_CREDENTIALS a GOOGLE_CLOUD_PROJECT =<Project-ID> pro zdroj GC

```azcopy
  - azcopy cp "https://storage.cloud.google.com/" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

Zkopírujte podmnožinu sad pomocí zástupného znaku (*) v názvu kontejneru z Google Cloud Storage pomocí klíče účtu služby a tokenu SAS pro cíl. Nejdřív nastavte proměnné prostředí GOOGLE_APPLICATION_CREDENTIALS a GOOGLE_CLOUD_PROJECT =<Project-ID> pro zdroj Google Cloud Storage.
 
```azcopy
azcopy cp "https://storage.cloud.google.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

## <a name="options"></a>Možnosti

**--záloha** Aktivuje systém Windows "SeBackupPrivilege for loades" nebo SeRestorePrivilege for downloads, aby bylo možné AzCopy zobrazit a číst všechny soubory bez ohledu na jejich oprávnění systému souborů a obnovit všechna oprávnění. Vyžaduje, aby účet používající AzCopy již má tato oprávnění (například má oprávnění správce nebo je členem `Backup Operators` skupiny). Tento příznak aktivuje oprávnění, která už účet má.

**--BLOB-Tags** Tagy řetězcové sady objektů BLOB pro kategorizaci dat v účtu úložiště.

**--typ BLOB-** String definuje typ objektu BLOB v cíli. Používá se k nahrávání objektů BLOB a při kopírování mezi účty (výchozí `Detect` ). Platné hodnoty zahrnují `Detect` , `BlockBlob` , `PageBlob` a `AppendBlob` . Při kopírování mezi účty hodnota způsobí, že `Detect` AzCopy použije typ zdrojového objektu BLOB k určení typu cílového objektu BLOB. Při nahrávání souboru určí, `Detect` jestli se jedná o soubor VHD nebo VHDX na základě přípony souboru. Pokud je soubor etherem VHD nebo VHDX, AzCopy soubor považuje za objekt blob stránky. (výchozí "Detect")

**--Block-objekt** blob bloku nahrává objekt blob pro Azure Storage pomocí této úrovně objektu BLOB. (výchozí hodnota "none")

**--Block-size-MB** float při nahrávání do Azure Storage použít tuto velikost bloku (zadanou v souboru MIB) a stahovat z Azure Storage. Výchozí hodnota se automaticky vypočítá na základě velikosti souboru. Jsou povoleny desetinné zlomky (například: 0,25).

**– řetězec řízení mezipaměti** nastavuje hlavičku Cache-Control. Vráceno při stažení.

**--check-Length** Zkontroluje délku souboru v cíli po přenosu. Pokud dojde ke neshodě mezi zdrojem a cílem, je přenos označený jako neúspěšný. (výchozí hodnota je `true` )

**--check-MD5** řetězec Určuje, jak by měly být při stahování ověřovány STRIKTNĚ hash MD5. K dispozici pouze při stahování. Dostupné možnosti: `NoCheck` , `LogOnly` , `FailIfDifferent` , `FailIfDifferentOrMissing` . (výchozí `FailIfDifferent` ) (výchozí "FailIfDifferent")

**--dispoziční řetězec obsahu** nastaví hlavičku Content-Disposition. Vráceno při stažení.

**--řetězec Content-Encoding** nastaví hlavičku Content-Encoding. Vráceno při stažení.

**--Content-Language-** řetězec nastaví hlavičku Content-Language. Vráceno při stažení.

**--typ obsahu** řetězec Určuje typ obsahu souboru. Implikuje typ No-odhad-MIME-Type. Vráceno při stažení.

**--dekomprese** Při stahování automaticky dekomprimovat soubory, pokud jejich obsah-kódování označuje, že jsou komprimovány. Podporované hodnoty kódování obsahu jsou `gzip` a `deflate` . Přípony souborů `.gz` / `.gzip` nebo `.zz` nejsou nezbytné, ale budou odebrány, pokud jsou k dispozici.

**--Exclude – řetězec atributů** (pouze Windows) vyloučí soubory, jejichž atributy se shodují se seznamem atributů. Příklad: A; Pracují Í

**--Exclude – řetězec typu BLOB** volitelně určuje typ objektu BLOB ( `BlockBlob` /  `PageBlob` /  `AppendBlob` ), který se má vyloučit při kopírování objektů BLOB z kontejneru nebo účtu. Použití tohoto příznaku není použitelné pro kopírování dat ze služby mimo Azure do služby. Více než jeden objekt BLOB by měl být oddělený `;` . 

**--Exclude vyloučení – řetězec cesty** vyloučí tyto cesty při kopírování. Tato možnost nepodporuje zástupné znaky (*). Kontroluje předponu relativní cesty (například: `myFolder;myFolder/subDirName/file.pdf` ). Pokud se používá v kombinaci s procházením účtu, cesty neobsahují název kontejneru.

**--vyloučit-vzor** řetězec vyloučí tyto soubory při kopírování. Tato možnost podporuje zástupné znaky (*).

**--sledovat – symbolických odkazů**  Při nahrávání z místního systému souborů Sledujte symbolické odkazy.

**--Force-if – jen pro čtení** Když přepíšete existující soubor ve Windows nebo v souborech Azure, vynutíte přepsání, aby fungovalo i v případě, že je u stávajícího souboru nastavený atribut jen pro čtení.

**--řetězec z-na** volitelně Určuje kombinaci zdrojového cíle. Například: `LocalBlob` , `BlobLocal` , `LocalBlobFS` .

**--**  nápovědu pro kopírování.

**--include-After** String zahrnuje pouze soubory změněné za dané datum a čas nebo po něm. Hodnota by měla být ve formátu ISO8601. Pokud není zadané žádné časové pásmo, předpokládá se, že hodnota je v místním časovém pásmu počítače, na kterém běží AzCopy. například `2020-08-19T15:04:00Z` pro čas UTC nebo `2020-08-19` pro půlnoc (00:00) v místním časovém pásmu. Jako u AzCopy 10,5 se tento příznak vztahuje pouze na soubory, nikoli na složky, takže vlastnosti složky nebudou zkopírovány při použití tohoto příznaku v `--preserve-smb-info` nebo `--preserve-smb-permissions` .

 **--include-před** řetězec obsahuje pouze soubory změněné před nebo podle daného data a času. Hodnota by měla být ve formátu ISO8601. Pokud není zadané žádné časové pásmo, předpokládá se, že hodnota je v místním časovém pásmu počítače, na kterém běží AzCopy. Například `2020-08-19T15:04:00Z` pro čas UTC nebo `2020-08-19` pro půlnoc (00:00) v místním časovém pásmu. Od AzCopy 10,7 se tento příznak vztahuje pouze na soubory, nikoli na složky, takže vlastnosti složky nebudou zkopírovány při použití tohoto příznaku v `--preserve-smb-info` nebo `--preserve-smb-permissions` .

**--include-** Attribute String (pouze Windows) obsahuje soubory, jejichž atributy se shodují se seznamem atributů. Příklad: A; Pracují Í

**--include-Path** řetězec zahrnuje pouze tyto cesty při kopírování. Tato možnost nepodporuje zástupné znaky (*). Kontroluje předponu relativní cesty (například: `myFolder;myFolder/subDirName/file.pdf` ).

**--include – řetězec vzoru** zahrne při kopírování jenom tyto soubory. Tato možnost podporuje zástupné znaky (*). Oddělte soubory pomocí `;` .

**--list-of-** Versions určuje soubor, ve kterém je každé ID verze uvedené na samostatném řádku. Ujistěte se, že zdroj musí ukazovat na jeden objekt BLOB a že všechna ID verzí zadaná v souboru, který tento příznak používá, musí patřit pouze do zdrojového objektu BLOB. AzCopy stáhne zadané verze do zadané cílové složky. Další informace najdete v tématu [stažení předchozích verzí objektu BLOB](./storage-use-azcopy-v10.md#transfer-data).

**--řetězec na úrovni protokolu** definuje podrobnosti protokolu pro soubor protokolu, dostupné úrovně: informace (všechny požadavky a odpovědi), upozornění (pomalé odezvy), chyby (pouze neúspěšné žádosti) a žádné (žádné protokoly výstupu). (výchozí `INFO` ). 

**–** nahrání řetězce metadat pro Azure Storage s těmito páry klíč-hodnota jako metadata.

**--No-odhad-MIME-Type**  Zabrání AzCopy zjištění typu obsahu založeného na příponě nebo obsahu souboru.

**--přepsání** řetězce přepíše konfliktní soubory a objekty BLOB v cílovém umístění, pokud je tento příznak nastaven na hodnotu true. (výchozí `true` ) Možné hodnoty zahrnují `true` , `false` , `prompt` a `ifSourceNewer` . U cílových umístění, které podporují složky, budou konfliktní vlastnosti na úrovni složky přepsány, `true` nebo pokud je výzva k zadání kladné odezvy k příkazovému řádku k dispozici. (výchozí hodnota true)

**--Page-BLOB-** Page nahrání objektu blob stránky pro Azure Storage pomocí této úrovně objektu BLOB. (výchozí `None` ). (výchozí hodnota "none")

**--Preserve – čas poslední změny**  K dispozici pouze v případě, že cílem je systém souborů.

**--Preserve – vlastník**    Má vliv jenom na stažení a jenom v případě, kdy `--preserve-smb-permissions` se používá. Pokud je hodnota true (výchozí nastavení), vlastník a skupina souborů se při stahování zachovají. Pokud je hodnota nastavena na false, zůstane `--preserve-smb-permissions` zachovány seznamy ACL, ale vlastník a skupina budou založené na uživateli, na kterém běží AzCopy (výchozí hodnota true).

**--Preserve-SMB-info**   Výchozí hodnota je false. Zachovává informace o vlastnostech protokolu SMB (čas posledního zápisu, čas vytvoření, bity atributů) mezi prostředky pracujícími s protokolem SMB (soubory Windows a Azure). Přenesou se jenom bity atributů podporované soubory Azure. ostatní budou ignorovány. Tento příznak platí pro soubory i složky, pokud není zadán filtr pouze souborů (například include-Pattern). Informace přenesené pro složky jsou stejné jako u souborů, s výjimkou času posledního zápisu, který není nikdy zachované pro složky.

**--Preserve-SMB-Permissions**   Výchozí hodnota je false. Zachovává seznamy řízení přístupu SMB mezi prostředky, které podporují (soubory Windows a Azure). Pro soubory ke stažení budete také potřebovat `--backup` příznak pro obnovení oprávnění, kde nový vlastník nebude uživatel s AzCopy. Tento příznak platí pro soubory i složky, pokud není zadán filtr pouze souborů (například `include-pattern` ).

**--Put-MD5**    Vytvořte hodnotu hash MD5 každého souboru a uložte hodnotu hash jako vlastnost content-MD5 cílového objektu BLOB nebo souboru. (Ve výchozím nastavení není hodnota hash vytvořena.) K dispozici pouze při nahrávání.

**--rekurzivní**    Při nahrávání z místního systému souborů se mají rekurzivně Hledat podadresáře.

**--S2S-Detect-source-změněno**   Zjišťuje, jestli se zdrojový soubor nebo objekt BLOB během čtení mění. (Tento parametr se vztahuje pouze na kopie od služby po služby, protože odpovídající kontroler je trvale povolen pro nahrávání a stahování.)

**--S2S-Handle-neplatný-řetězec metadat** určuje, jak se zpracovávají neplatné klíče metadat. Dostupné možnosti: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (výchozí `ExcludeIfInvalid` ). (výchozí "ExcludeIfInvalid")

**--S2S-Preserve-úroveň přístupu**   Zachovat úroveň přístupu během kopírování z provozu do služby Pokud chcete zajistit, aby cílový účet úložiště podporoval nastavení úrovně přístupu, přečtěte si téma [Azure Blob Storage: horká, studená a archivní úroveň přístupu](../blobs/storage-blob-storage-tiers.md) . V případech, kdy nastavení úrovně přístupu není podporované, použijte s2sPreserveAccessTier = false, aby se vynechá kopírování úrovně přístupu. (výchozí `true` ).  (výchozí hodnota true)

**--S2S-Preserve-Properties**   Zachovat úplné vlastnosti během kopírování Service to Service. V případě nesamostatného zdroje souborů AWS S3 a Azure File nevrátí operace list úplné vlastnosti objektů a souborů. Aby bylo možné zachovat úplné vlastnosti, AzCopy potřebuje odeslat jednu další žádost na jeden objekt nebo soubor. (výchozí hodnota true)

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

**--Cap – MB/s float**   Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.

**--výstupní** formát řetězce výstupu příkazu. Mezi možnosti patří: text, JSON. Výchozí hodnota je `text`. (výchozí "text")

**--Trusted – řetězec Microsoft-přípony** Určuje další přípony domén, kde se můžou odesílat přihlašovací tokeny Azure Active Directory.  Výchozí formát je `*.core.windows.net;*.core.chinacloudapi.cn;*.core.cloudapi.de;*.core.usgovcloudapi.net`. Zde uvedené jsou přidány do výchozího nastavení. Z důvodu zabezpečení byste měli sem umístit jenom Microsoft Azure domény. Více položek oddělte středníkem.

## <a name="see-also"></a>Viz také

- [AzCopy](storage-ref-azcopy.md)
