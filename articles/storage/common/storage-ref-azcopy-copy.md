---
title: AzCopy kopii | Microsoft Docs
description: Tento článek poskytuje referenční informace pro příkaz AzCopy Copy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7f55b22938bd6f18bae1576a0c64e673996d38bf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84220134"
---
# <a name="azcopy-copy"></a>azcopy copy

Zkopíruje zdrojová data do cílového umístění.

## <a name="synopsis"></a>Stručný obsah

Zkopíruje zdrojová data do cílového umístění. Podporovány jsou následující pokyny:

  - místní < – > Azure Blob (ověřování SAS nebo OAuth)
  - místní soubory Azure <-> (ověřování pomocí SAS pro sdílení/adresář)
  - místní <-> ADLS obecná 2 (ověřování SAS, OAuth nebo SharedKey)
  - Azure Blob (SAS nebo Public) – > Azure Blob (ověřování SAS nebo OAuth)
  - Azure Blob (SAS nebo Public) – > soubory Azure (SAS)
  - Soubory Azure (SAS) – > soubory Azure (SAS)
  - Soubory Azure (SAS) – > Azure Blob (ověřování SAS nebo OAuth)
  - AWS S3 (přístupový klíč) – > Azure Block BLOB (ověřování SAS nebo OAuth)

Další informace najdete v příkladech.

## <a name="related-conceptual-articles"></a>Související koncepční články

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)
- [Přenos dat pomocí AzCopy a BLOB Storage](storage-use-azcopy-blobs.md)
- [Přenos dat pomocí AzCopy a úložiště souborů](storage-use-azcopy-files.md)
- [Konfigurace, optimalizace a řešení potíží s AzCopy](storage-use-azcopy-configure.md)

## <a name="advanced"></a>Pokročilý

AzCopy automaticky detekuje typ obsahu souborů při nahrávání z místního disku na základě přípony souboru nebo obsahu (Pokud není zadáno žádné rozšíření).

Integrovaná vyhledávací tabulka je malá, ale v systému UNIX je rozšířena o soubory MIME. Types v místním systému, pokud jsou k dispozici v jednom nebo více těchto názvech:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

V systému Windows jsou z registru extrahovány typy MIME. Tuto funkci můžete vypnout pomocí příznaku. Přečtěte si část příznak.

Pokud nastavíte proměnnou prostředí pomocí příkazového řádku, bude tato proměnná čitelná v historii příkazového řádku. Zvažte vymazání proměnných, které obsahují pověření z historie příkazového řádku. Chcete-li zabránit zobrazování proměnných ve vaší historii, můžete použít skript, který uživateli vyzve k zadání přihlašovacích údajů a k nastavení proměnné prostředí.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Příklady

Nahrajte jeden soubor pomocí ověřování OAuth. Pokud jste ještě přihlášeni k AzCopy, spusťte prosím příkaz AzCopy Login před spuštěním následujícího příkazu.

- AzCopy CP "/path/to/file.txt" "https://[účet]. blob. Core. Windows. NET/[Container]/[cesta/k/BLOB]"

Stejné jako výše, ale tentokrát také vypočítat hodnotu hash MD5 obsahu souboru a uložit ji jako vlastnost content-MD5 objektu BLOB:

- AzCopy CP "/path/to/file.txt" "https://[účet]. blob. Core. Windows. NET/[Container]/[cesta/k/BLOB]"--Put-MD5

Odeslat jeden soubor pomocí tokenu SAS:

- AzCopy CP "/path/to/file.txt" "https://[účet]. blob. Core. Windows. NET/[Container]/[cesta/k/BLOB]? [SAS] "

Odeslat jeden soubor pomocí tokenu SAS a potrubního (pouze objekty blob bloku):
  
- Cat "/path/to/file.txt" | AzCopy CP "https://[účet]. blob. Core. Windows. NET/[kontejner]/[cesta/k/BLOB]? [SAS] "

Nahrajte celý adresář pomocí tokenu SAS:
  
- AzCopy CP "/path/to/dir" "https://[účet]. blob. Core. Windows. NET/[Container]/[cesta/do/adresář]? [SAS] "--rekurzivní = true

nebo

- AzCopy CP "/path/to/dir" "https://[účet]. blob. Core. Windows. NET/[Container]/[cesta/do/adresář]? [SAS] "--rekurzivní = true--Put-MD5

Odeslat sadu souborů pomocí tokenu SAS a znaků zástupného znaku (*):

- AzCopy CP "/Path/*foo/* bar/*. PDF" "https://[účet]. blob. Core. Windows. NET/[Container]/[cesta/do/adresář]? [SAS] "

Nahrávání souborů a adresářů pomocí tokenu SAS a zástupných znaků (*):

- AzCopy CP "/Path/*foo/* bar *" "https://[účet]. blob. Core. Windows. NET/[Container]/[cesta/do/adresář]? [SAS] "--rekurzivní = true

Stažení jednoho souboru pomocí ověřování OAuth. Pokud jste ještě přihlášeni k AzCopy, spusťte prosím příkaz AzCopy Login před spuštěním následujícího příkazu.

- AzCopy CP "https://[účet]. blob. Core. Windows. NET/[kontejner]/[cesta/k/BLOB]" "/path/to/file.txt"

Stažení jednoho souboru pomocí tokenu SAS:

- AzCopy CP "https://[účet]. blob. Core. Windows. NET/[kontejner]/[cesta/k/BLOB]? [SAS] ""/path/to/file.txt "

Stažení jednoho souboru pomocí tokenu SAS a následného zřetězení výstupu do souboru (pouze objekty blob bloku):
  
- AzCopy CP "https://[účet]. blob. Core. Windows. NET/[kontejner]/[cesta/k/BLOB]? [SAS] ">"/path/to/file.txt "

Stáhněte si celý adresář pomocí tokenu SAS:
  
- AzCopy CP "https://[účet]. blob. Core. Windows. NET/[kontejner]/[cesta/do/adresář]? [SAS] ""/path/to/dir "--rekurzivní = true

Poznámka o použití zástupného znaku (*) v adresách URL:

Existují dva způsoby, jak použít zástupný znak v adrese URL. 

- Můžete použít jednu hned za koncovým lomítkem (/) adresy URL. Tím zkopírujete všechny soubory v adresáři přímo do cílového umístění, aniž byste je museli umístit do podadresáře.

- Můžete ji také použít v názvu kontejneru, pokud adresa URL odkazuje pouze na kontejner a nikoli na objekt BLOB. Tento přístup můžete použít k získání souborů z podmnožiny kontejnerů.

Stažení obsahu adresáře bez kopírování samotného obsahujícího adresáře.

- AzCopy CP "https://[srcaccount]. blob. Core. Windows. NET/[kontejner]/[cesta/do/složka]/*? [SAS] ""/path/to/dir "

Stáhněte si celý účet úložiště.

- AzCopy CP "https://[srcaccount]. blob. Core. Windows. NET/" "/path/to/dir"--rekurzivní

Pomocí zástupného znaku (*) v názvu kontejneru Stáhněte podmnožinu kontejnerů v rámci účtu úložiště.

- AzCopy CP "https://[srcaccount]. blob. Core. Windows. NET/[kontejner * název]" "/path/to/dir"--rekurzivní

Zkopírujte jeden objekt blob do jiného objektu BLOB pomocí tokenu SAS.

- AzCopy CP "https://[srcaccount]. blob. Core. Windows. NET/[kontejner]/[cesta/k/BLOB]? [SAS] "" https://[destaccount]. blob. Core. Windows. NET/[kontejner]/[cesta/k/BLOB]? [SAS] "

Zkopírujte jeden objekt blob do jiného objektu BLOB pomocí tokenu SAS a tokenu OAuth. Na konci adresy URL zdrojového účtu musíte použít token SAS, ale pokud se přihlašujete k AzCopy pomocí příkazu AzCopy Login, cílový účet ho nepotřebuje. 

- AzCopy CP "https://[srcaccount]. blob. Core. Windows. NET/[kontejner]/[cesta/k/BLOB]? [SAS] "" https://[destaccount]. blob. Core. Windows. NET/[kontejner]/[cesta/k/BLOB] "

Zkopírování jednoho virtuálního adresáře objektu BLOB na jiný pomocí tokenu SAS:

- AzCopy CP "https://[srcaccount]. blob. Core. Windows. NET/[kontejner]/[cesta/do/adresář]? [SAS] "" https://[destaccount]. blob. Core. Windows. NET/[kontejner]/[cesta/do/adresář]? [SAS] "--rekurzivní = true

Zkopírujte všechny kontejnery objektů blob, adresáře a objekty BLOB z účtu úložiště do jiného pomocí tokenu SAS:

- AzCopy CP "https://[srcaccount]. blob. Core. Windows. NET? [SAS] "" https://[destaccount]. blob. Core. Windows. NET? [SAS] "--rekurzivní = true

Zkopírování jednoho objektu do Blob Storage ze Amazon Web Services (AWS) S3 pomocí přístupového klíče a tokenu SAS. Nejprve nastavte proměnnou prostředí AWS_ACCESS_KEY_ID a AWS_SECRET_ACCESS_KEY pro zdroj AWS S3.
  
- AzCopy CP " https://s3.amazonaws.com/ [kontejner]/[objekt]" "https://[destaccount]. blob. Core. Windows. NET/[Container]/[cesta/k/BLOB]? [ SAS] "

Zkopírování celého adresáře do Blob Storage z AWS S3 pomocí přístupového klíče a tokenu SAS. Nejprve nastavte proměnnou prostředí AWS_ACCESS_KEY_ID a AWS_SECRET_ACCESS_KEY pro zdroj AWS S3.

- AzCopy CP " https://s3.amazonaws.com/ [kontejner]/[složka]" "https://[destaccount]. blob. Core. Windows. NET/[Container]/[cesta/do/adresář]? [ SAS] "--rekurzivní = true

Pokud https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html Chcete lépe pochopit zástupný symbol [složka], přečtěte si prosím.

Zkopírujte všechny intervaly do Blob Storage z Amazon Web Services (AWS) pomocí přístupového klíče a tokenu SAS. Nejprve nastavte proměnnou prostředí AWS_ACCESS_KEY_ID a AWS_SECRET_ACCESS_KEY pro zdroj AWS S3.

- AzCopy CP " https://s3.amazonaws.com/ " https://[destaccount]. blob. Core. Windows. NET? [ SAS] "--rekurzivní = true

Zkopírujte všechny intervaly do Blob Storage z oblasti Amazon Web Services (AWS) pomocí přístupového klíče a tokenu SAS. Nejprve nastavte proměnnou prostředí AWS_ACCESS_KEY_ID a AWS_SECRET_ACCESS_KEY pro zdroj AWS S3.

- AzCopy CP " https://s3- [region]. amazonaws. com/" "https://[destaccount]. blob. Core. Windows. NET? [ SAS] "--rekurzivní = true

Zkopírujte podmnožinu kontejnerů pomocí zástupného znaku (*) v názvu kontejneru. Stejně jako v předchozích příkladech budete potřebovat přístupový klíč a token SAS. Ujistěte se, že jste pro zdroj AWS S3 nastavili proměnnou prostředí AWS_ACCESS_KEY_ID a AWS_SECRET_ACCESS_KEY.

- AzCopy CP " https://s3.amazonaws.com/ [interval * název]/" "https://[destaccount]. blob. Core. Windows. NET? [ SAS] "--rekurzivní = true

## <a name="options"></a>Možnosti

**--záloha**                               Aktivuje Windows SeBackupPrivilege pro nahrání nebo SeRestorePrivilege pro stahování, aby AzCopy viděli čtení všech souborů bez ohledu na jejich oprávnění systému souborů a obnovení všech oprávnění. Vyžaduje, aby účet používající AzCopy již má tato oprávnění (například má oprávnění správce nebo je členem skupiny Backup Operators). Všechny tyto příznaky aktivují oprávnění, která tento účet již má.

**--typ BLOB-** String definuje typ objektu BLOB v cíli. Používá se k nahrávání objektů BLOB a při kopírování mezi účty (výchozí zjišťování). Platné hodnoty zahrnují ' Detect ', ' BlockBlob ', ' PageBlob ' a ' AppendBlob '. Při kopírování mezi účty, hodnota "Detect" způsobí, že AzCopy použije typ zdrojového objektu BLOB k určení typu cílového objektu BLOB. Když nahráváte soubor, zjistí, jestli je soubor VHD nebo VHDX soubor na základě přípony souboru. Pokud je soubor etherem VHD nebo VHDX, AzCopy soubor považuje za objekt blob stránky. (výchozí "Detect")

**--Block-objekt BLOB-vrstva** nahrávání objektů blob bloku přímo do [úrovně přístupu](../blobs/storage-blob-storage-tiers.md) dle vašeho výběru. (výchozí možnost ' None '). Platné hodnoty zahrnují "none", "Hot", "studené" a "Archive". Pokud není předána žádná nebo žádná vrstva, objekt BLOB zdědí úroveň účtu úložiště.

**--Block-size-MB** float při nahrávání do Azure Storage použít tuto velikost bloku (zadanou v souboru MIB) a stahovat z Azure Storage. Výchozí hodnota se automaticky vypočítá na základě velikosti souboru. Jsou povoleny desetinné zlomky (například: 0,25).

**– řetězec řízení mezipaměti** nastavuje hlavičku Cache-Control. Vráceno při stažení.

**--check-Length**                         Zkontroluje délku souboru v cíli po přenosu. Pokud dojde ke neshodě mezi zdrojem a cílem, je přenos označený jako neúspěšný. (výchozí hodnota true)

**--check-MD5** řetězec Určuje, jak by měly být při stahování ověřovány STRIKTNĚ hash MD5. K dispozici pouze při stahování. Dostupné možnosti: Nekontrolovat, přihlásit se, FailIfDifferent, FailIfDifferentOrMissing. (výchozí "FailIfDifferent")

**--dispoziční řetězec obsahu** nastaví hlavičku Content-Disposition. Vráceno při stažení.

**--řetězec Content-Encoding** nastaví hlavičku Content-Encoding. Vráceno při stažení.

**--Content-Language-** řetězec nastaví hlavičku Content-Language. Vráceno při stažení.

**--typ obsahu** řetězec Určuje typ obsahu souboru. Implikuje typ No-odhad-MIME-Type. Vráceno při stažení.

**--dekomprese**                           Při stahování automaticky dekomprimovat soubory, pokud jejich obsah-kódování označuje, že jsou komprimovány. Podporované hodnoty kódování obsahu jsou "gzip" a "Deflate". Přípony souborů '. gz '/'. gzip ' nebo '. zz ' nejsou nezbytné, ale budou odebrány, je-li k dispozici.

**--Exclude – řetězec atributů** (pouze Windows) vyloučení souborů, jejichž atributy odpovídají seznamu atributů. Příklad: A; Pracují Í

**--Exclude – řetězec typu BLOB** volitelně určuje typ objektu BLOB (BlockBlob/PageBlob/AppendBlob), který se má vyloučit při kopírování objektů BLOB z kontejneru nebo účtu. Použití tohoto příznaku se nedá použít pro kopírování dat ze služby mimo Azure. Více než jeden objekt BLOB by měl být oddělený středníkem (;).

**--Exclude vyloučení – řetězec cesty** vyloučí tyto cesty při kopírování. Tato možnost nepodporuje zástupné znaky (*). Kontroluje předponu relativní cesty (například: myFolder; myFolder/subDirName/file.pdf). Pokud se používá v kombinaci s procházením účtu, cesty neobsahují název kontejneru.

**--vyloučit-vzor** řetězec vyloučí tyto soubory při kopírování. Tato možnost podporuje zástupné znaky (*).

**--sledovat – symbolických odkazů**                      Při nahrávání z místního systému souborů Sledujte symbolické odkazy.

**--řetězec z-na** volitelně Určuje kombinaci zdrojového cíle. Příklad: LocalBlob, BlobLocal, LocalBlobFS.

**-h,--** nápovědu pro kopírování

**--include – řetězec atributů** (pouze Windows) zahrnuje soubory, jejichž atributy odpovídají seznamu atributů. Příklad: A; Pracují Í

**--include-Path** řetězec zahrnuje pouze tyto cesty při kopírování. Tato možnost nepodporuje zástupné znaky (*). Kontroluje předponu relativní cesty (například: myFolder; myFolder/subDirName/file.pdf).

**--include – řetězec vzoru** zahrne při kopírování jenom tyto soubory. Tato možnost podporuje zástupné znaky (*). Oddělte soubory pomocí '; '.

**--řetězec na úrovni protokolu** definuje podrobnosti protokolu pro soubor protokolu, dostupné úrovně: informace (všechny požadavky a odpovědi), upozornění (pomalé odezvy), chyby (pouze neúspěšné žádosti) a žádné (žádné protokoly výstupu). (výchozí "informace")

**–** nahrání řetězce metadat pro Azure Storage s těmito páry klíč-hodnota jako metadata.

**--No-odhad-MIME-Type**                   Zabrání AzCopy zjištění typu obsahu založeného na příponě nebo obsahu souboru.

**--přepsání** řetězce přepíše konfliktní soubory a objekty BLOB v cílovém umístění, pokud je tento příznak nastaven na hodnotu true. Možné hodnoty zahrnují ' true ', ' false ', ' ifSourceNewer ' a ' prompt '. (výchozí hodnota true)

**--Page-BLOB-** Page nahrání objektu blob stránky pro Azure Storage pomocí této úrovně objektu BLOB. (výchozí hodnota "none")

**--Preserve – čas poslední změny**          K dispozici pouze v případě, že cílem je systém souborů.

**--Preserve-SMB-oprávnění** ve výchozím nastavení false. Zachovává seznamy řízení přístupu SMB mezi prostředky, které podporují (soubory Windows a Azure). Pro soubory ke stažení budete také muset pomocí `--backup` příznaku obnovit oprávnění, kde nový vlastník nebude uživatel, na kterém běží AzCopy. Tento příznak platí pro soubory i složky, pokud není zadán filtr pouze souborů (např. `include-pattern` ).

**--Preserve-SMB-info** String false ve výchozím nastavení. Zachovává informace o vlastnostech protokolu SMB (čas posledního zápisu, čas vytvoření, bity atributů) mezi prostředky pracujícími s protokolem SMB (soubory Windows a Azure). Přenesou se jenom bity atributů podporované soubory Azure. ostatní budou ignorovány. Tento příznak platí pro soubory i složky, pokud není zadán filtr pouze souborů (např. include-Pattern). Přenesené informace pro složky jsou stejné jako u souborů, s výjimkou času posledního zápisu, který se nikdy nezachovává pro složky.

**--Preserve – vlastník**                       Má vliv jenom na to, kdy se stahují data, a jenom v případě, že `--preserve-smb-permissions` se používá. Pokud je hodnota true (výchozí nastavení), vlastník a skupina souborů se při stahování zachovají. Pokud je tento příznak nastaven na hodnotu false, `--preserve-smb-permissions` bude stále zachovat seznamy ACL, ale vlastník a skupina budou založené na uživateli, na kterém je spuštěný AzCopy.

**--Put-MD5**                             Vytvořte hodnotu hash MD5 každého souboru a uložte hodnotu hash jako vlastnost content-MD5 cílového objektu BLOB nebo souboru. (Ve výchozím nastavení není hodnota hash vytvořena.) K dispozici pouze při nahrávání.

**--rekurzivní**                            Při nahrávání z místního systému souborů se budou rekurzivně zobrazovat podadresáře.

**--S2S-Detect-source-změněno**           Zkontroluje, jestli se zdroj po vyčíslení změnil.

**--S2S-Handle-neplatný-řetězec metadat** určuje, jak se zpracovávají neplatné klíče metadat. Dostupné možnosti: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (výchozí "ExcludeIfInvalid")

**--S2S-Preserve-úroveň přístupu**             Zachovat úroveň přístupu během kopírování z provozu do služby Pokud chcete zajistit, aby cílový účet úložiště podporoval nastavení úrovně přístupu, přečtěte si prosím [v Azure Blob Storage: horká, studená a archivní úroveň přístupu](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) . V případech, kdy se nastavení úrovně přístupu nepodporuje, použijte s2sPreserveAccessTier = false, aby se přenechá kopírování úrovně přístupu. (výchozí hodnota true)

**--S2S-Preserve-Properties**              Zachovat úplné vlastnosti během kopírování Service to Service. V případě nesamostatného zdroje souborů AWS S3 a Azure File nevrátí operace list úplné vlastnosti objektů a souborů. Aby bylo možné zachovat úplné vlastnosti, AzCopy potřebuje odeslat jednu další žádost na jeden objekt nebo soubor. (výchozí hodnota true)

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

**--Cap – Mbps**      Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.

**--výstupní** formát řetězce výstupu příkazu. Mezi možnosti patří: text, JSON. Výchozí hodnota je "text". (výchozí "text")

**--Trusted – řetězec Microsoft-přípony** Určuje další přípony domén, kde se můžou odesílat přihlašovací tokeny Azure Active Directory.  Výchozí hodnota je *. Core.Windows.NET;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Zde uvedené jsou přidány do výchozího nastavení. Z důvodu zabezpečení byste měli sem umístit jenom Microsoft Azure domény. Více položek oddělte středníkem.

## <a name="see-also"></a>Viz také

- [AzCopy](storage-ref-azcopy.md)
