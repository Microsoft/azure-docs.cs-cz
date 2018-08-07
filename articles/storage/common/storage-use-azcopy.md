---
title: Kopírování nebo přesun dat do služby Azure Storage pomocí AzCopy ve Windows | Dokumentace Microsoftu
description: Použijte AzCopy na nástroj Windows Pokud chcete přesunout nebo kopírovat data do nebo z objektu blob, tabulky a obsahu souboru. Kopírování dat do služby Azure Storage z místních souborů nebo kopírování dat v rámci nebo mezi účty úložiště. Snadno migrate data do služby Azure Storage.
services: storage
author: seguler
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: seguler
ms.component: common
ms.openlocfilehash: aaae191baaa7b712c77d93303ded777afe97c249
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39530873"
---
# <a name="transfer-data-with-the-azcopy-on-windows"></a>Přenos dat pomocí AzCopy ve Windows
AzCopy je nástroj příkazového řádku určený pro kopírování dat do a z Microsoft Azure Blob, File a Table storage, pomocí jednoduchých příkazů, které jsou navržené pro zajištění optimálního výkonu. Data můžete kopírovat mezi systémem souborů a účtem úložiště nebo mezi účty úložiště.  

Existují dvě verze AzCopy, který si můžete stáhnout. AzCopy ve Windows nabízí možnosti příkazového řádku Windows style. [AzCopy v Linuxu](storage-use-azcopy-linux.md) cílí na platformy Linuxu nabízí možnosti příkazového řádku stylu POSIX. Tento článek se týká AzCopy ve Windows.

## <a name="download-and-install-azcopy-on-windows"></a>Stáhněte a nainstalujte nástroje AzCopy ve Windows

### <a name="latest-preview-version-v800"></a>Nejnovější verze Preview (v8.0.0)
Stáhněte si [nejnovější verzi preview AzCopy ve Windows](https://aka.ms/downloadazcopypr). Tato verze Preview nabízí výrazné zlepšení výkonu a balíčky .NET Core v instalaci.

#### <a name="azcopy-on-windows-80-preview-release-notes"></a>AzCopy ve Windows zpráva k vydání verze 8.0 ve verzi Preview
- Služba tabulek je již nejsou podporovány v nejnovější verzi. Pokud používáte funkci tabulky exportu, stáhněte si stabilní verzi.
- Sestavován .NET Core 2.1 a všechny závislosti .NET Core jsou teď součástí v instalaci.
- Výrazné zlepšení výkonu pro obě nahrávání a stahování scénáře

### <a name="latest-stable-version-v710"></a>Nejnovější stabilní verzi (v7.1.0)
Stáhněte si [nejnovější stabilní verze AzCopy ve Windows](https://aka.ms/downloadazcopy).

### <a name="post-installation-step"></a>Krok po instalaci

Po instalaci nástroje AzCopy ve Windows pomocí instalačního programu, otevřete okno příkazového řádku a přejděte do instalačního adresáře nástroje AzCopy ve vašem počítači – kde `AzCopy.exe` spustitelný soubor se nachází. V případě potřeby můžete přidat umístění instalace nástroje AzCopy do systémové cesty. Ve výchozím nastavení je nástroj AzCopy nainstalovaný na `%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy` nebo `%ProgramFiles%\Microsoft SDKs\Azure\AzCopy`.

## <a name="writing-your-first-azcopy-command"></a>Zápis prvního příkazu AzCopy

Základní syntaxe pro příkazy AzCopy je:

```azcopy
AzCopy /Source:<source> /Dest:<destination> [Options]
```

Následující příklady ukazují různé scénáře pro kopírování dat do a z Microsoft Azure Blobs, soubory a tabulky. Odkazovat [AzCopy parametry](#azcopy-parameters) části Podrobné vysvětlení parametrů použitých v každém vzorku.

## <a name="download-blobs-from-blob-storage"></a>Objekty BLOB můžete stáhnout z úložiště objektů Blob

Podívejme se na několik způsobů, jak stahovat objekty BLOB pomocí AzCopy.

### <a name="download-a-single-blob"></a>Stáhněte si jeden objekt blob

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"
```

Všimněte si, že pokud složka `C:\myfolder` buď neexistuje, vytvoří AzCopy ho a stažení `abc.txt ` do nové složky.

### <a name="download-a-single-blob-from-the-secondary-region"></a>Stáhněte si jeden objekt blob ze sekundární oblasti

```azcopy
AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"
```

Všimněte si, že musí mít povolený přístup k sekundární oblasti geograficky redundantní úložiště jen pro čtení.

### <a name="download-all-blobs-in-a-container"></a>Stáhnout všechny objekty BLOB v kontejneru

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S
```

Předpokládejme, že následující objekty BLOB jsou umístěny v zadaném kontejneru:  

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

Po provedení této operace stažení adresáři `C:\myfolder` zahrnuje následující soubory:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\vd1\a.txt
    C:\myfolder\vd1\abcd.txt

Pokud nezadáte možnost `/S`, objekty BLOB nebyly staženy.

### <a name="download-blobs-with-a-specific-prefix"></a>Stažení objektů BLOB s určitou předponou

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S
```

Předpokládejme, že následující objekty BLOB jsou umístěny v zadaném kontejneru. Všechny objekty BLOB začínající předponou `a` staženo:

    abc.txt
    abc1.txt
    abc2.txt
    xyz.txt
    vd1\a.txt
    vd1\abcd.txt

Po provedení této operace stažení složce `C:\myfolder` zahrnuje následující soubory:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

Předpona, která se vztahuje na virtuální adresář, který tvoří první část názvu objektu blob. Ve výše uvedeném příkladu virtuálního adresáře neodpovídá zadané předpony, proto se nestáhne. Kromě toho pokud možnost `/S` není zadán, AzCopy nestáhne žádné objekty BLOB.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Nastavit čas poslední změny exportované soubory na stejnou hodnotu jako objekty BLOB zdroje

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT
```

Objekty BLOB můžete také vyloučit z operace stahování na základě času jejich poslední úpravy. Pokud budete chtít vyloučit objekty BLOB, jehož poslední změny času je například stejnou nebo novější, než cílový soubor, přidejte `/XN` možnost:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN
```

Pokud budete chtít vyloučit objekty BLOB, čas poslední změny je na stejné nebo starší než cílový soubor, přidejte `/XO` možnost:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO
```

## <a name="upload-blobs-to-blob-storage"></a>Nahrání objektů BLOB do služby Blob storage

Podívejme se na několik způsobů, jak nahrát objektů BLOB pomocí nástroje AzCopy.

### <a name="upload-a-single-blob"></a>Nahrání jednoho objektu blob

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:"abc.txt"
```

Pokud zadaný cílový kontejner neexistuje, AzCopy ho vytvoří a soubor do něj nahraje.

### <a name="upload-a-single-blob-to-a-virtual-directory"></a>Nahrání jednoho objektu blob do virtuálního adresáře

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt
```

Pokud zadaný virtuální adresář neexistuje, AzCopy nahraje soubor, který má obsahovat virtuální adresář ve svém názvu (*například*, `vd/abc.txt` v předchozím příkladu).

### <a name="upload-all-blobs-in-a-folder"></a>Nahrát všechny objekty BLOB ve složce

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S
```

Zadání možnosti `/S` nahraje do objektu Blob úložiště rekurzivně, což znamená, že všechny podsložky a jejich soubory jsou odeslány také obsah zadaného adresáře. Například předpokládejme následující soubory jsou umístěny ve složce `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Po provedení této operace nahrávání kontejneru zahrnuje následující soubory:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Pokud nezadáte možnost `/S`, AzCopy ještě nenahrají rekurzivně. Po provedení této operace nahrávání kontejneru zahrnuje následující soubory:

    abc.txt
    abc1.txt
    abc2.txt

### <a name="upload-blobs-matching-a-specific-pattern"></a>Nahrání objektů BLOB odpovídající vzorku

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S
```

Předpokládejme následující soubory jsou umístěny ve složce `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\xyz.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Po provedení této operace nahrávání kontejneru zahrnuje následující soubory:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Pokud nezadáte možnost `/S`, AzCopy nahraje pouze objekty BLOB, které nejsou umístěny ve virtuálním adresáři:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Zadejte typ obsahu MIME cílový objekt blob

Ve výchozím nastavení, AzCopy nastaví typ obsahu cílový objekt blob do `application/octet-stream`. Počínaje verzí 3.1.0, můžete explicitně určit typ obsahu prostřednictvím možnost `/SetContentType:[content-type]`. Tato syntaxe nastaví typ obsahu pro všechny objekty BLOB v rámci operace nahrávání.

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType:video/mp4
```

Pokud zadáte `/SetContentType` bez hodnoty, AzCopy nastaví každý objekt blob nebo typ obsahu souboru podle jeho přípony souboru.

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType
```

## <a name="copy-blobs-in-blob-storage"></a>Kopírování objektů BLOB v úložišti objektů Blob

Podívejme se na několik způsobů, jak kopírovat objekt BLOB z jednoho umístění do druhého pomocí AzCopy.

### <a name="copy-a-single-blob-from-one-container-to-another-within-the-same-storage-account"></a>Zkopírujte jeden objekt blob z jednoho kontejneru do jiného v rámci stejného účtu úložiště 

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt
```

Při kopírování objektu blob v účtu úložiště, [kopírování na straně serveru](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) proběhlo.

### <a name="copy-a-single-blob-from-one-storage-account-to-another"></a>Zkopírujte jeden objekt blob z jednoho účtu úložiště do jiného

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
```

Při kopírování objektu blob mezi účty úložiště [kopírování na straně serveru](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) proběhlo.

### <a name="copy-a-single-blob-from-the-secondary-region-to-the-primary-region"></a>Zkopírujte jeden objekt blob ze sekundární oblasti do primární oblasti

```azcopy
AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
```

Všimněte si, že musí mít povolený přístup k sekundární úložiště geograficky redundantní úložiště jen pro čtení.

### <a name="copy-a-single-blob-and-its-snapshots-from-one-storage-account-to-another"></a>Zkopírujte jeden objekt blob a jeho snímky z jednoho účtu úložiště do jiného

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot
```

Po provedení této operace kopírování obsahuje cílový kontejner objektu blob a jeho snímků. Za předpokladu, že má dvěma snímky objektů blob v předchozím příkladu, kontejner zahrnuje následující objektů blob a snímky:

    abc.txt
    abc (2013-02-25 080757).txt
    abc (2014-02-21 150331).txt

### <a name="copy-all-blobs-in-a-container-to-another-storage-account"></a>Kopírovat všechny objekty BLOB v kontejneru do jiného účtu úložiště 

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 
/Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /S
```

Zadání možnosti /S odešle obsah rekurzivně zadaného kontejneru. Zobrazit [nahrát všechny objekty BLOB ve složce](#upload-all-blobs-in-a-folder) Další informace a příklad.

### <a name="synchronously-copy-blobs-from-one-storage-account-to-another"></a>Synchronně zkopírujte objekty BLOB z jednoho účtu úložiště do jiného

AzCopy ve výchozím nastavení asynchronně kopíruje data mezi dva koncové body služby úložiště. Proto spustí operace kopírování v pozadí využití kapacity přebytečné šířky pásma, který nemá žádné smlouvy SLA na základě jak rychle se zkopíruje objekt blob, a AzCopy pravidelně kontroluje stav kopírování, dokud se kopírování je dokončená nebo neúspěšná.

`/SyncCopy` Možnost zajišťuje, že operace kopírování získá konzistentní rychlost. AzCopy provádí synchronní kopie stažením přes bloby až po kopírování ze zadaného zdroje do místní paměti, a pak se nahrají do cíle úložiště objektů Blob.

```azcopy
AzCopy /Source:https://myaccount1.blob.core.windows.net/myContainer/ /Dest:https://myaccount2.blob.core.windows.net/myContainer/ /SourceKey:key1 /DestKey:key2 /Pattern:ab /SyncCopy
```

`/SyncCopy` může generovat náklady na další výchozí přenos dat ve srovnání s asynchronní kopírování, doporučený postup je ve Virtuálním počítači Azure, který je ve stejné oblasti jako váš účet úložiště zdroje, aby se zabránilo náklady na výchozí přenos dat pomocí této možnosti.

## <a name="download-files-from-file-storage"></a>Stažení souborů ze služby File storage

Podívejme se na několik způsobů, jak stáhnout soubory pomocí nástroje AzCopy.

### <a name="download-a-single-file"></a>Stáhněte si jeden soubor

```azcopy
AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt
```

Pokud je zadaný zdroj sdílené složky Azure, pak buď musíte zadat přesný název souboru, (*například* `abc.txt`) ke stažení jednoho souboru nebo možnost `/S` ke stažení všechny soubory ve sdílené složce rekurzivně. Pokus zadat vzor souborů i možnost `/S` společně dojde k chybě.

### <a name="download-all-files-in-a-directory"></a>Stáhnout všechny soubory v adresáři

```azcopy
AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S
```

Všimněte si, že se nestáhnou prázdné složky.

## <a name="upload-files-to-an-azure-file-share"></a>Nahrání souborů do sdílené složky Azure

Podívejme se na několik způsobů, jak nahrát soubory pomocí nástroje AzCopy.

### <a name="upload-a-single-file"></a>Nahrát jeden soubor

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:abc.txt
```

### <a name="upload-all-files-in-a-folder"></a>Nahrání všech souborů ve složce

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S
```

Všimněte si, že nejsou uloženy prázdné složky.

### <a name="upload-files-matching-a-specific-pattern"></a>Nahrát soubory odpovídající vzorku

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S
```

## <a name="copy-files-in-file-storage"></a>Kopírování souborů ve službě File storage

Podívejme se na několik způsobů, jak kopírovat soubory do sdílené složky Azure pomocí nástroje AzCopy.

### <a name="copy-from-one-file-share-to-another"></a>Kopírování z jedné sdílené složky do jiného

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S
```
Při kopírování souboru ve sdílené složky [kopírování na straně serveru](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) proběhlo.

### <a name="copy-from-an-azure-file-share-to-blob-storage"></a>Kopírování ze sdílené složky Azure do úložiště objektů Blob

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S
```
Při kopírování souboru ze sdílené souborové položky do objektu blob, [kopírování na straně serveru](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) proběhlo.

### <a name="copy-a-blob-from-blob-storage-to-an-azure-file-share"></a>Kopírovat objekt blob z úložiště objektů Blob do sdílené složky Azure

```azcopy
AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S
```
Při kopírování souboru z objektu blob do sdílené složky, [kopírování na straně serveru](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) proběhlo.

### <a name="synchronously-copy-files"></a>Synchronně zkopírovat soubory

Můžete zadat `/SyncCopy` možnost pro kopírování dat z úložiště souborů do služby File Storage, File Storage do služby Blob Storage a z úložiště objektů Blob do služby File Storage synchronně, AzCopy k tomu stahování zdroje dat k místní paměti a nahrajte ho znovu cíl. Platí poplatky za standardní výstup.

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy
```

Při kopírování ze souboru úložiště do úložiště objektů Blob, výchozí typ objektu blob je objekty blob bloku; Uživatel může zadat možnost `/BlobType:page` změnit typ cílového objektu blob.

Všimněte si, že `/SyncCopy` může generovat náklady na další výchozí přenos dat ve srovnání s asynchronní kopírování. Doporučený postup je ve virtuálním počítači Azure, která je ve stejné oblasti jako váš účet úložiště zdroje, aby se zabránilo náklady na výchozí přenos dat pomocí této možnosti.

## <a name="export-data-from-table-storage"></a>Exportovat data z Table storage

Pojďme se podívat na export dat z Azure Table storage pomocí AzCopy.

### <a name="export-a-table"></a>Export tabulky

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key
```

AzCopy zapíše soubor manifestu do určené cílové složky. Soubor manifestu se používá v procesu importu vyhledat potřebná data souborů a provést ověření dat. Ve výchozím nastavení používá soubor manifestu následujícími zásadami vytváření názvů:

    <account name>_<table name>_<timestamp>.manifest

Uživatele můžete také určit možnost `/Manifest:<manifest file name>` nastavit název souboru manifestu.

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /Manifest:abc.manifest
```

### <a name="split-an-export-from-table-storage-into-multiple-files"></a>Export z Table storage rozdělená do více souborů

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/mytable/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100
```

Využívá nástroj AzCopy *svazku index* v názvech souborů rozdělení dat k rozlišení více souborů. Index svazku se skládá ze dvou částí *index rozsah klíče oddílu* a *rozdělení souboru indexu*. Obě indexy jsou počítány od nuly.

Index rozsah klíče oddílu je 0, pokud neurčí možnost `/PKRS`.

Předpokládejme například, AzCopy vytvoří dva soubory dat poté, co uživatel zadá možnost `/SplitSize`. Výsledný názvy souborů dat může být:

    myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
    myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

Všimněte si, že minimální možné hodnoty pro možnost `/SplitSize` je 32 MB. Pokud je zadaný cíl úložiště objektů Blob, AzCopy rozdělí datový soubor po jeho velikosti dosáhne omezení velikosti objektu blob (200GB), bez ohledu na to, jestli možnost `/SplitSize` je zadaná uživatelem.

### <a name="export-a-table-to-json-or-csv-data-file-format"></a>Export tabulky do formátu souboru data JSON nebo CSV

Ve výchozím nastavení exportuje AzCopy tabulek do datové soubory JSON. Můžete zadat možnost `/PayloadFormat:JSON|CSV` Export tabulek jako JSON nebo CSV.

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PayloadFormat:CSV
```

Při zadávání datové části formát CSV, AzCopy také vygeneruje soubor schéma s příponou souboru `.schema.csv` pro každý datový soubor.

### <a name="export-table-entities-concurrently"></a>Export tabulky souběžně entity

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PKRS:"aa#bb"
```

AzCopy spouští souběžné operace Export entit, pokud uživatel zadá možnost `/PKRS`. Každá operace vyexportuje jeden rozsah klíče oddílu.

Všimněte si, že počet souběžných operací se také řídí možnost `/NC`. AzCopy používá jako výchozí hodnotu počtu jader procesorů `/NC` při kopírování tabulkové entity, i v případě `/NC` nebyl určen. Když uživatel zadá možnost `/PKRS`, AzCopy používá menší ze dvou hodnot - rozsahů klíče oddílů a implicitně nebo explicitně zadaná souběžných operací - k určení počtu souběžných operací spustit. Další informace, zadejte `AzCopy /?:NC` na příkazovém řádku.

### <a name="export-a-table-to-blob-storage"></a>Export tabulky do úložiště objektů Blob

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2
```

AzCopy generuje soubor dat JSON do kontejneru objektů blob s následující názvovou konvencí:

    <account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

Vygenerovaný soubor dat JSON Formát datové části pro – minimální metadata. Podrobnosti o této datové části formátu naleznete v tématu [Formát datové části pro operace služby s tabulkou](http://msdn.microsoft.com/library/azure/dn535600.aspx).

Mějte na paměti, že při exportu tabulky k objektům BLOB, AzCopy na místní dočasné datové soubory ke stažení tabulkové entity a potom je odešle tyto entity do objektu blob. Tyto dočasné datové soubory jsou vloženy do složky soubor deníku s výchozí cestou "<code>%LocalAppData%\Microsoft\Azure\AzCopy</code>", můžete zadat možnost/Z: [deníku souboru folder] pro deník změn souboru umístění složky a tak změnit umístění souborů dočasná data. Dočasná data, která velikost souborů je rozhodnuto pomocí tabulkové entity velikost a velikost, který jste zadali pomocí možností /SplitSize, i když dočasných dat souboru na místním disku se okamžitě odstraňovat, jakmile se odeslal do objektu blob, ujistěte se prosím, že máte dostatečně místní místo na disku pro ukládání těchto souborů dočasná data, než se odstraní.

## <a name="import-data-into-table-storage"></a>Import dat do Table storage

Pojďme se podívat na Import dat do Azure Table storage pomocí AzCopy.

### <a name="import-a-table"></a>Import tabulky

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

Možnost `/EntityOperation` Určuje, jak vložit entity do tabulky. Možné hodnoty:

* `InsertOrSkip`: Přeskočí existující entity nebo vloží nové entity, pokud neexistuje v tabulce.
* `InsertOrMerge`: Sloučí existující entity nebo vloží nové entity, pokud neexistuje v tabulce.
* `InsertOrReplace`: Nahradí existující entity nebo vloží nové entity, pokud neexistuje v tabulce.

Všimněte si, že nelze zadat možnost `/PKRS` ve scénáři importu. Na rozdíl od export scénář, ve které je nutné zadat `/PKRS` spuštění souběžných operací, AzCopy spuštěním souběžných operací ve výchozím nastavení při importu tabulky. Výchozí počet souběžných operací spuštění se rovná počtu jader procesorů; Můžete ale zadat jiný počet souběžných s možností `/NC`. Další informace, zadejte `AzCopy /?:NC` na příkazovém řádku.

Všimněte si, že AzCopy podporuje pouze import pro formát JSON, není sdílený svazek clusteru. AzCopy nepodporuje importy tabulky z uživatelem vytvořené JSON a soubory manifestu. Oba tyto soubory musí pocházet z tabulky export AzCopy. Aby nedocházelo k chybám, prosím neupravujte exportované JSON a soubor manifestu.

### <a name="import-entities-into-a-table-from-blob-storage"></a>Importovat entity do tabulky z úložiště objektů Blob

Předpokládejme, kontejner objektů Blob obsahuje následující: soubor A JSON Azure Table Storage a jeho související soubor manifestu.

    myaccount_mytable_20140103T112020.manifest
    myaccount_mytable_20140103T112020_0_0_0AF395F1DC42E952.json

Můžete spustit následující příkaz pro import entity do tabulky pomocí souboru manifestu v tomto kontejneru objektů blob:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:https://myaccount.table.core.windows.net/mytable /SourceKey:key1 /DestKey:key2 /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:"InsertOrReplace"
```

## <a name="other-azcopy-features"></a>Jiné funkce nástroje AzCopy

Pojďme se podívat na některé další funkce AzCopy.

### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Kopírovat pouze data, která neexistuje v cílovém umístění

`/XO` a `/XN` parametry umožňují vyloučit prostředky staršího nebo novějšího zdroje jsou zkopírovány v uvedeném pořadí. Pokud chcete zkopírovat zdrojové prostředky, které neexistují v cíli, můžete zadat oba parametry do příkazu AzCopy:

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /XO /XN

    /Source:C:\myfolder /Dest:http://myaccount.file.core.windows.net/myfileshare /DestKey:<destkey> /S /XO /XN

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:http://myaccount.blob.core.windows.net/mycontainer1 /SourceKey:<sourcekey> /DestKey:<destkey> /S /XO /XN

Všimněte si, že to není podporováno při zdroji nebo cíli je tabulka.

### <a name="use-a-response-file-to-specify-command-line-parameters"></a>Zadejte parametry příkazového řádku pomocí souboru odpovědí.

```azcopy
AzCopy /@:"C:\responsefiles\copyoperation.txt"
```

Všechny parametry příkazového řádku AzCopy můžete zahrnout v souboru odpovědí. AzCopy zpracovává parametry v souboru jako v případě, kdyby byly zadány v příkazovém řádku provádění s přímým přístupem nahrazení s obsahem souboru.

Soubor odpovědi se předpokládá `copyoperation.txt`, který obsahuje následující řádky. Jednotlivé parametry nástroje AzCopy můžete zadat na jednom řádku

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

nebo na samostatné řádky:

    /Source:http://myaccount.blob.core.windows.net/mycontainer
    /Dest:C:\myfolder
    /SourceKey:<sourcekey>
    /S
    /Y

AzCopy se nezdaří, pokud je parametr rozdělit mezi dva řádky, jak je znázorněno zde pro `/sourcekey` parametr:

    http://myaccount.blob.core.windows.net/mycontainer
     C:\myfolder
    /sourcekey:
    <sourcekey>
    /S
    /Y

### <a name="use-multiple-response-files-to-specify-command-line-parameters"></a>Zadejte parametry příkazového řádku pomocí více souborů odezvy

Soubor odpovědi se předpokládá `source.txt` , který určuje zdrojového kontejneru:

    /Source:http://myaccount.blob.core.windows.net/mycontainer

A soubor odpovědí s názvem `dest.txt` , který určuje cílovou složku v systému souborů:

    /Dest:C:\myfolder

A soubor odpovědí s názvem `options.txt` , který určuje možnosti pro AzCopy:

    /S /Y

AzCopy volat s těmito soubory odpovědí, které jsou umístěny v adresáři `C:\responsefiles`, použijte tento příkaz:

```azcopy
AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   
```

Stejně, jako kdyby jste zahrnuli všechny jednotlivé parametry příkazového řádku, zpracovává AzCopy tento příkaz:

```azcopy
AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y
```

### <a name="specify-a-shared-access-signature-sas"></a>Zadejte sdílený přístupový podpis (SAS)

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt
```

Můžete také zadat SAS URI kontejneru:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S
```

### <a name="journal-file-folder"></a>Složka souboru deníku

Pokaždé, když vydáte příkaz azcopy, zkontroluje, jestli existuje soubor deníku ve výchozí složce, nebo určuje, zda existuje ve složce, která jste zadali pomocí této možnosti. Pokud soubor deníku na jednom místě neexistuje, AzCopy zpracovává operace jako nové a vygeneruje nový soubor deníku.

Pokud soubor deníku neexistuje, AzCopy zkontroluje, zda odpovídá příkazového řádku, který zadáte na příkazovém řádku ve soubor deníku. Pokud se shodují se dvěma příkazové řádky, AzCopy obnoví neúplná operace. Pokud shodné nejsou, budete vyzváni buď přepsat soubor deníku spustit novou operaci, nebo zrušit aktuální operaci.

Pokud chcete použít výchozí umístění pro soubor deníku:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z
```

Pokud vynecháte možnost `/Z`, nebo možnost `/Z` bez cesty ke složce, jak je uvedeno výše, AzCopy vytvoří soubor deníku ve výchozím umístění, což je `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Pokud už existuje soubor deníku, AzCopy obnoví operace na základě souboru deníku.

Pokud chcete zadat vlastní umístění pro soubor deníku:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\
```

Tento příklad vytvoří soubor deníku, pokud ještě neexistuje. Pokud neexistuje, AzCopy obnoví operace na základě souboru deníku.

Pokud chcete pokračovat v operaci AzCopy:

```azcopy
AzCopy /Z:C:\journalfolder\
```

Tento příklad pokračuje v poslední operaci, která se nepodařilo dokončit.

### <a name="generate-a-log-file"></a>Generování souboru protokolu

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V
```

Pokud zadáte možnost `/V` bez zadání cestu k souboru do podrobného protokolování, AzCopy vytvoří soubor protokolu ve výchozím umístění, což je `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

V opačném případě můžete vytvořit soubor protokolu ve vlastním umístění:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log
```

Všimněte si, že pokud zadejte relativní cestu následující možnost `/V`, jako například `/V:test/azcopy1.log`, pak podrobného protokolování se vytvoří v aktuálním pracovním adresáři v podsložce s názvem `test`.

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Zadejte počet souběžných operací spuštění

Možnost `/NC` určuje počet souběžných kopírování. Ve výchozím nastavení spustí AzCopy počet souběžných operací, pokud chcete zvýšit propustnost dat přenos. Pro operace s tabulkou počet souběžných operací se rovná počtu procesorů, které máte. Pro operace objektů Blob a souboru, počet souběžných operací je roven 8krát počet procesorů, které máte. Pokud spustíte AzCopy přes síť s malou šířkou pásma, můžete zadat nižší číslo pro /NC Vyhněte se nezdařila z důvodu prostředků soutěže.

### <a name="run-azcopy-against-the-azure-storage-emulator"></a>Spusťte nástroj AzCopy emulátoru úložiště Azure

Spustíte AzCopy proti [emulátoru úložiště Azure](storage-use-emulator.md) objektů blob:

```azcopy
AzCopy /Source:https://127.0.0.1:10000/myaccount/mycontainer/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S
```

Můžete ho spustit také pro tabulky:

```azcopy
AzCopy /Source:https://127.0.0.1:10002/myaccount/mytable/ /Dest:C:\myfolder /SourceKey:key /SourceType:Table
```

### <a name="automatically-determine-content-type-of-a-blob"></a>Automaticky určit typ obsahu objektu Blob

AzCopy Určuje typ obsahu objektu blob na základě souboru JSON, který ukládá, typu obsahu chcete mapování přípony souboru. Tento soubor JSON má název AzCopyConfig.json a je umístěn v adresáři AzCopy. Pokud máte typ souboru, který se nenachází v seznamu můžete přidat mapování do souboru JSON:

```
{
  "MIMETypeMapping": {
    ".myext": "text/mycustomtype",
    .
    .
  }
}
```     

## <a name="azcopy-parameters"></a>AzCopy parametry

Parametry pro AzCopy jsou popsané níže. Můžete také zadat jeden z následujících příkazů z příkazového řádku pro help v pomocí nástroje AzCopy:

* Získat podrobnou nápovědu příkazového řádku pro AzCopy: `AzCopy /?`
* Podrobnou nápovědu k jakékoli AzCopy parametr: `AzCopy /?:SourceKey`
* Pro Příklady příkazového řádku: `AzCopy /?:Sample`

### <a name="sourcesource"></a>/ Source: "zdroj"

Určuje zdroj data, ze kterého chcete kopírovat. Zdrojem může být systémový adresář souboru, kontejner objektů blob, objektu blob virtuálního adresáře, sdílenou složku úložiště, adresář souboru úložiště nebo tabulku Azure.

**Platí pro:** objekty BLOB, soubory, tabulky

### <a name="destdestination"></a>/ Dest: "cíl"

Určuje cíl pro kopírování. Cílem může být systémový adresář souboru, kontejner objektů blob, objektu blob virtuálního adresáře, sdílenou složku úložiště, adresář souboru úložiště nebo tabulku Azure.

**Platí pro:** objekty BLOB, soubory, tabulky

### <a name="patternfile-pattern"></a>/ Vzorku: "vzor souborů"

Určuje soubor vzor, který označuje soubory, které chcete kopírovat. Chování /Pattern parametru se určuje podle umístění zdroje dat a přítomnost rekurzivního možnost režimu. Rekurzivní režim je zadáno pomocí možnosti parametrem/s.

Pokud se zadaný zdrojový adresář v systému souborů, pak standardní zástupné znaky jsou aktivní a vzor souborů, které jsou k dispozici je hledána soubory v adresáři. Pokud možnost je zadán, pak AzCopy také odpovídající zadanému vzoru proti všechny soubory v podsložkách pod adresářem.

Je-li zadaný zdrojový kontejner objektů blob nebo virtuální adresář, nejsou použity zástupné znaky. Pokud je možnost, který je zadán, pak AzCopy interpretuje vzor určený soubor jako předpona objektu blob. Pokud možnost není zadán parametr, pak AzCopy odpovídá vzoru souboru před názvy přesně objektů blob.

Pokud zadaný zdroj je sdílenou složku Azure, pak musíte buď zadat přesný název souboru, (např. abc.txt) zkopírovat jeden soubor nebo možnost /S zkopírujte všechny soubory ve sdílené složce rekurzivně. Pokus zadat obě souboru modelu a možnost /S společně výsledků k chybě.

AzCopy používá velká a malá písmena porovnávání, když / Source je kontejner objektů blob nebo virtuálního adresáře objektů blob a používá porovnávání ve všech ostatních případech.

Soubor výchozím způsobem používaným-li zadána žádná vzor souborů je *.* pro umístění v systému souborů nebo prázdnou předponu pro umístění služby Azure Storage. Zadávání více vzory souborů se nepodporuje.

**Platí pro:** objekty BLOB, soubory

### <a name="destkeystorage-key"></a>/ DestKey: "klíč úložiště"

Určuje klíč účtu úložiště pro cílový prostředek.

**Platí pro:** objekty BLOB, soubory, tabulky

### <a name="destsassas-token"></a>/ DestSAS: "sas token"

Určuje sdílený přístupový podpis (SAS) s oprávněními ke čtení a zápisu pro cíl (pokud existuje). Obklopit SAS s dvojitými uvozovkami, protože obsahuje speciální znaky příkazového řádku.

Pokud cílového prostředku je kontejner objektů blob, sdílené složky nebo tabulky, můžete zadat tuto možnost, za nímž následuje SAS token nebo sdíleného přístupového podpisu lze zadat jako součást cílový kontejner objektů blob, sdílené složky nebo identifikátor URI tabulky bez této možnosti.

Pokud zdroj a cíl jsou oba objekty BLOB, pak cílový objekt blob se musí nacházet v rámci stejného účtu úložiště jako zdrojový objekt blob.

**Platí pro:** objekty BLOB, soubory, tabulky

### <a name="sourcekeystorage-key"></a>/ SourceKey: "klíč úložiště"

Určuje klíč účtu úložiště pro zdrojový prostředek.

**Platí pro:** objekty BLOB, soubory, tabulky

### <a name="sourcesassas-token"></a>/ SourceSAS: "sas token"

Určuje sdílený přístupový podpis s oprávněními pro čtení a seznamu pro zdroj (pokud existuje). Obklopit SAS s dvojitými uvozovkami, protože obsahuje speciální znaky příkazového řádku.

Pokud zdroj prostředků je kontejner objektů blob a je k dispozici klíč ani SAS, kontejner objektů blob číst prostřednictvím anonymní přístup.

Pokud je zdroj sdílené složky nebo tabulky, musí být zadaná klíč nebo SAS.

**Platí pro:** objekty BLOB, soubory, tabulky

### <a name="s"></a>/S

Určuje režim rekurzivní pro operace kopírování. V režimu rekurzivní AzCopy zkopíruje všechny objekty BLOB nebo soubory, které odpovídají zadaného souboru modelu, včetně těch v podsložkách.

**Platí pro:** objekty BLOB, soubory

### <a name="blobtypeblock--page--append"></a>/ BlobType: "blok" | "page" | "připojení"

Určuje, zda cílový objekt blob je objekty blob bloku, objekty blob stránky nebo doplňovací objekt blob. Tato možnost se vztahuje pouze v případě, že jsou nahrání objektu blob. V opačném případě je vygenerována chyba. Pokud je cílový objekt blob a není tato možnost zadána, ve výchozím nastavení, AzCopy vytvoří objekt blob bloku.

**Platí pro:** objekty BLOB

### <a name="checkmd5"></a>/ CheckMD5

Vypočítá hodnotu hash MD5 pro stažená data a ověřuje, že hodnota hash MD5 uložená v objektu blob nebo vlastnost Content-MD5 souboru odpovídá vypočtený hash. Kontrola MD5 je vypnuto ve výchozím nastavení, proto musíte určit tuto možnost, při stahování dat provést kontrolu MD5.

Všimněte si, že Azure Storage nezaručuje, že je aktuální hodnota hash MD5 uložených objektů blob nebo souboru. Zodpovídá za klienta aktualizovat MD5 pokaždé, když je upraven objekt blob nebo souboru.

AzCopy vždy nastaví vlastnost Content-MD5 pro soubor objektů blob v Azure nebo po nahrání do služby.  

**Platí pro:** objekty BLOB, soubory

### <a name="snapshot"></a>/ Snímku

Označuje, zda přenos snímky. Tato možnost je platná, pouze pokud je zdroj objektu blob.

Snímky převedené objektů blob jsou přejmenovány v tomto formátu: .extension název objektu blob (snímek time)

Ve výchozím nastavení nejsou zkopírovány snímky.

**Platí pro:** objekty BLOB

### <a name="vverbose-log-file"></a>/ V: [podrobné log-file]

Zobrazí podrobné stavové zprávy do souboru protokolu.

Ve výchozím nastavení, je podrobného souboru protokolu s názvem AzCopyVerbose.log v `%LocalAppData%\Microsoft\Azure\AzCopy`. Pokud chcete zadat existující umístění souboru pro tuto možnost, podrobného protokolování se připojí k tomuto souboru.  

**Platí pro:** objekty BLOB, soubory, tabulky

### <a name="zjournal-file-folder"></a>/ Z: [deníku souboru folder]

Určuje složku soubor deníku pro operaci obnovení.

AzCopy vždy podporuje pokračování, pokud operace byla přerušena.

Pokud tato možnost není zadána nebo je zadán bez cestu ke složce, AzCopy se vytvoří soubor deníku výchozí umístění, které je % LocalAppData%\Microsoft\Azure\AzCopy.

Pokaždé, když vydáte příkaz azcopy, zkontroluje, jestli existuje soubor deníku ve výchozí složce, nebo určuje, zda existuje ve složce, která jste zadali pomocí této možnosti. Pokud soubor deníku na jednom místě neexistuje, AzCopy zpracovává operace jako nové a vygeneruje nový soubor deníku.

Pokud soubor deníku neexistuje, AzCopy zkontroluje, zda odpovídá příkazového řádku, který zadáte na příkazovém řádku ve soubor deníku. Pokud se shodují se dvěma příkazové řádky, AzCopy obnoví neúplná operace. Pokud shodné nejsou, budete vyzváni buď přepsat soubor deníku spustit novou operaci, nebo zrušit aktuální operaci.

Odstraní se tento soubor deníku po úspěšném dokončení operace.

Všimněte si, že pokračování operace, soubor deníku vytvořena pomocí předchozí verze nástroje AzCopy není podporována.

**Platí pro:** objekty BLOB, soubory, tabulky

### <a name="parameter-file"></a>/@:"Parameter-File"

Určuje soubor, který obsahuje parametry. AzCopy zpracovává parametry v souboru tak, jako kdyby kdyby byly zadány v příkazovém řádku.

V souboru odpovědí můžete zadat více parametrů na jednom řádku nebo zadejte každý parametr na samostatném řádku. Všimněte si, že jednotlivé parametr nemůžou zahrnovat více řádků.

Soubory odpovědí může obsahovat komentáře řádků, které začínají symbolem #.

Můžete zadat více souborů odezvy. Mějte však na paměti, že AzCopy nepodporuje vnořené soubory odpovědí.

**Platí pro:** objekty BLOB, soubory, tabulky

### <a name="y"></a>/Y

Potlačí všechny výzvy k potvrzení AzCopy. Tato možnost také umožňuje použití pouze pro zápis tokeny SAS pro scénáře nahrávání dat, když nejsou zadané /XO a /XN.

**Platí pro:** objekty BLOB, soubory, tabulky

### <a name="l"></a>/ L

Určuje operaci výpisu. žádná data zkopírována.

AzCopy interpretuje pomocí nástroje tuhle možnost simulace pro spuštění příkazového řádku bez možnosti/l a počty kolik objekty jsou zkopírovány, můžete zadat možnost /V ve stejnou dobu ke kontrole objektů, které jsou zkopírovány v podrobného protokolování.

Chování této možnosti je také určeno umístění zdroje dat a přítomnost rekurzivního možnost /S a soubor vzor možnost režim /Pattern.

AzCopy vyžaduje seznam a oprávnění ke čtení z tohoto umístění zdroje při použití této možnosti.

**Platí pro:** objekty BLOB, soubory

### <a name="mt"></a>/MT

Nastaví čas poslední změny stažený soubor být stejný jako zdrojový objekt blob nebo souboru.

**Platí pro:** objekty BLOB, soubory

### <a name="xn"></a>/XN

Vyloučí novější zdrojového prostředku. Prostředek není zkopírován, pokud čas poslední změny zdroje je stejná nebo novější než cíl.

**Platí pro:** objekty BLOB, soubory

### <a name="xo"></a>/XO
Vyloučí prostředek starší zdroje. Prostředek není zkopírován, pokud čas poslední změny zdroje je stejná nebo starší než cíl.

**Platí pro:** objekty BLOB, soubory

### <a name="a"></a>/A

Nahraje pouze soubory, které se mají nastavit atribut pro archivaci.

**Platí pro:** objekty BLOB, soubory

### <a name="iarashcnetoi"></a>/ IA: [RASHCNETOI]

Nahraje pouze soubory, které mají některý z sady zadané atributy.

Dostupné atributy patří:

* R = soubory jen pro čtení
* A = soubory připravené k archivaci
* S = systémové soubory
* H = skryté soubory
* C = komprimované soubory
* N = normální soubory
* E = šifrované soubory
* T = dočasné soubory
* O = Offline soubory
* Můžu = bez indexovat soubory

**Platí pro:** objekty BLOB, soubory

### <a name="xarashcnetoi"></a>/ XA: [RASHCNETOI]

Vyloučí soubory, které mají všechny zadané atributy sady.

Dostupné atributy patří:

* R = soubory jen pro čtení
* A = soubory připravené k archivaci
* S = systémové soubory
* H = skryté soubory
* C = komprimované soubory
* N = normální soubory
* E = šifrované soubory
* T = dočasné soubory
* O = Offline soubory
* Můžu = bez indexovat soubory

**Platí pro:** objekty BLOB, soubory

### <a name="delimiterdelimiter"></a>/ Oddělovač: oddělovač."

Označuje znak oddělovač použitý k oddělení virtuálních adresářů v názvu objektu blob.

Ve výchozím nastavení, využívá nástroj AzCopy / jako oddělovací znak. Ale AzCopy podporuje všechny běžné znaku (například @, #, nebo %) jako oddělovač. Pokud potřebujete jeden z těchto speciálních znaků v příkazovém řádku, uzavřete název souboru v uvozovkách.

Tato možnost platí pouze pro stažení objektů BLOB.

**Platí pro:** objekty BLOB

### <a name="ncnumber-of-concurrent-operations"></a>/ Síťovým Adaptérem: "číslo sady souběžných operace"

Určuje počet souběžných operací.

AzCopy ve výchozím nastavení spustí počet souběžných operací, pokud chcete zvýšit propustnost dat přenos. Všimněte si, že velký počet souběžných operací v prostředí s malou šířkou pásma může zahlcovat připojení k síti a zabránit operace plně dokončit. Omezení souběžných operací podle skutečné dostupnou šířku pásma sítě.

Horní mez pro souběžné operace je 512.

**Platí pro:** objekty BLOB, soubory, tabulky

### <a name="sourcetypeblob--table"></a>/ SourceType: "Blob" | "Table"

Určuje, že `source` prostředku je k dispozici v místním vývojovém prostředí, spouštění v emulátoru úložiště objektu blob.

**Platí pro:** objekty BLOB, tabulky

### <a name="desttypeblob--table"></a>/ DestType: "Blob" | "Table"

Určuje, že `destination` prostředku je k dispozici v místním vývojovém prostředí, spouštění v emulátoru úložiště objektu blob.

**Platí pro:** objekty BLOB, tabulky

### <a name="pkrskey1key2key3"></a>/ PKRS: "key1 #key2 klíč3 #..."

Rozdělí rozsah klíče oddílu aby povolil export dat tabulky současně, což zvyšuje rychlost operace exportu.

Pokud není tato možnost zadána, AzCopy používá jedno vlákno pro export tabulkové entity. Například, pokud uživatel zadá /PKRS: "aa #bb" AzCopy a spustí tři souběžných operací.

Každá operace exportuje jednu ze tří rozsahů klíče oddílů, jak je znázorněno níže:

  [první klíč oddílu, aa)

  [aa, bb)

  [bb, klíč oddílu poslední]

**Platí pro:** tabulky

### <a name="splitsizefile-size"></a>/ SplitSize: "velikost souboru"

Určuje velikost v MB, je minimální povolená hodnota rozdělení exportovaného souboru je 32.

Pokud není tato možnost zadána, AzCopy exportuje data tabulky do jednoho souboru.

Pokud tabulka data se exportují do objektu blob a velikost exportovaný soubor dosáhne 200 GB limitu pro velikost objektu blob, pak AzCopy rozdělí exportovaný soubor, i v případě, že tato možnost není zadána.

**Platí pro:** tabulky

### <a name="entityoperationinsertorskip--insertormerge--insertorreplace"></a>/ EntityOperation: "InsertOrSkip" | "InsertOrMerge" | "InsertOrReplace"

Určuje chování importu dat tabulky.

* InsertOrSkip – přeskakuje existující entity nebo vloží nové entity, pokud neexistuje v tabulce.
* InsertOrMerge - sloučí existující entity nebo vloží nové entity, pokud neexistuje v tabulce.
* InsertOrReplace - nahradí existující entity nebo vloží nové entity, pokud neexistuje v tabulce.

**Platí pro:** tabulky

### <a name="manifestmanifest-file"></a>/ Manifest: "souboru manifestu"

Určuje soubor manifestu v tabulce exportu a importu.

Tento parametr je nepovinný během exportu, AzCopy generuje soubor manifestu s názvem předdefinované, pokud tato možnost není zadána.

Tato možnost je vyžadována během operace importu pro vyhledávání dat souborů.

**Platí pro:** tabulky

### <a name="synccopy"></a>/ SyncCopy

Označuje, zda synchronně zkopírovat soubory mezi dva koncové body služby Azure Storage nebo objekty BLOB.

AzCopy ve výchozím nastavení používá asynchronní kopírování na straně serveru. Zadejte tuto možnost, chcete-li provést synchronní kopie, která stáhne soubory nebo objekty BLOB na místní paměti a odesílá je do služby Azure Storage.

Tuto možnost můžete použít při kopírování souborů do úložiště objektů Blob, File storage, nebo z úložiště objektů Blob do služby File storage a naopak.

**Platí pro:** objekty BLOB, soubory

### <a name="setcontenttypecontent-type"></a>/ SetContentType: "content-type"

Určuje typ obsahu MIME pro soubory nebo cílové objektů BLOB.

AzCopy ve výchozím nastavení nastaví typ obsahu pro objekt blob nebo soubor na application/octet-stream. Typ obsahu pro všechny soubory nebo objekty BLOB můžete nastavit tak, že explicitně zadáte hodnotu pro tuto možnost.

Pokud zadáte tuto možnost bez hodnoty, AzCopy se nastaví, každý objekt blob nebo typ obsahu souboru podle jeho přípony souboru.

**Platí pro:** objekty BLOB, soubory

### <a name="payloadformatjson--csv"></a>/ PayloadFormat: "JSON" | "SDÍLENÉHO SVAZKU CLUSTERU"

Určuje formát souboru exportovaná data tabulky.

Pokud není tato možnost zadána, exportuje AzCopy ve výchozím nastavení tabulka datový soubor ve formátu JSON.

**Platí pro:** tabulky

## <a name="known-issues-and-best-practices"></a>Známé problémy a osvědčené postupy

Pojďme se podívat na některé známé problémy a osvědčené postupy.

### <a name="limit-concurrent-writes-while-copying-data"></a>Limit souběžných zápisů při kopírování dat

Při kopírování objektů BLOB nebo soubory pomocí nástroje AzCopy, mějte na paměti, že jiná aplikace může být úpravy dat během kopírování ji. Pokud je to možné Ujistěte se, že data, která kopírujete nemění během operace kopírování. Například při kopírování virtuálního pevného disku přidružený k virtuálnímu počítači Azure, ujistěte se, že aktuálně psaní žádné jiné aplikace na virtuální pevný disk. Dobrým způsobem, jak to provést, je zapůjčení prostředek, který chcete zkopírovat. Alternativně můžete nejprve vytvořte snímek virtuální pevný disk a potom zkopírovat snímek.

Pokud nelze zabránit aplikacím možnost zapisovat do objektů BLOB nebo soubory, zatímco se kopírují, pak mějte na paměti, že v době, kdy se úloha dokončí, zkopírované prostředky může už nebude mít úplnou paritu s prostředky zdroje.

### <a name="enable-fips-compliant-md5-algorithms-for-azcopy-when-you-use-fips-compliant-algorithms-for-encryption-hashing-and-signing"></a>Povolení algoritmů MD5 kompatibilní se standardem FIPS pro nástroj AzCopy při je "použití algoritmy odpovídající standardu FIPS pro šifrování, algoritmus hash a podepisování."

AzCopy ve výchozím nastavení používá k výpočtu MD5 při kopírování objektů .NET MD5 implementaci, ale existují nějaké požadavky na zabezpečení, které je třeba AzCopy můžete povolit nastavení kompatibilní se standardem FIPS MD5.

Můžete vytvořit soubor app.config `AzCopy.exe.config` s vlastností `AzureStorageUseV1MD5` a vložit ho aside s AzCopy.exe.

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
      <appSettings>
        <add key="AzureStorageUseV1MD5" value="false"/>
      </appSettings>
    </configuration>

Pro vlastnost "AzureStorageUseV1MD5":

* True – výchozí hodnota, AzCopy používá implementace .NET MD5.
* False – AzCopy používá algoritmus MD5 kompatibilní se standardem FIPS.

Ve výchozím nastavení systému Windows jsou zakázány algoritmy odpovídající standardu FIPS. Nastavení této zásady můžete změnit na svém počítači. V okně Spustit (Windows + R) zadejte secpol.msc otevřete **místní zásady zabezpečení** okna. V **nastavení zabezpečení** okno, přejděte na **nastavení zabezpečení** > **místní zásady** > **možnosti zabezpečení**. Vyhledejte **kryptografický modul systému: použití algoritmy odpovídající standardu FIPS pro šifrování, algoritmus hash a podepisování** zásad. Dvakrát klikněte na zásadu, kterou chcete zobrazit hodnoty zobrazené v **nastavení zabezpečení** sloupce.

## <a name="next-steps"></a>Další postup

Další informace o službě Azure Storage a AzCopy najdete v následujících zdrojích informací:

### <a name="azure-storage-documentation"></a>Dokumentace k Azure Storage:
* [Seznámení se službou Azure Storage](../storage-introduction.md)
* [Použití Blob storage pomocí technologie .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Jak používat úložiště File z .NET](../storage-dotnet-how-to-use-files.md)
* [Jak používat úložiště Table z .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Jak vytvořit, spravovat nebo odstranit účet úložiště](../storage-create-storage-account.md)
* [Přenos dat pomocí AzCopy v Linuxu](storage-use-azcopy-linux.md)

### <a name="azure-storage-blog-posts"></a>Azure Storage blogových příspěvků:
* [Úvod do služby Azure Storage dat přesun knihovny ve verzi Preview](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [AzCopy: Představujeme synchronní kopie a vlastní typ obsahu](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [AzCopy: Oznámení obecné dostupnosti AzCopy 3.0 a 4.0 nástroje AzCopy vydání ve verzi preview s podporou tabulka a soubor](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [AzCopy: Optimalizovaná pro scénáře, ve velkém měřítku kopírování](http://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy: Podpora pro geograficky redundantní úložiště jen pro čtení](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [AzCopy: Přenos dat pomocí restartovatelnou službu režimu a tokenu SAS](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
* [AzCopy: Objekt Blob kopírování mezi účtu pomocí](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [AzCopy: Nahrávání a stahování souborů pro objekty BLOB Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)
