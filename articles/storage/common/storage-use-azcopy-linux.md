---
title: Kopírování nebo přesun dat do služby Azure Storage pomocí AzCopy v Linuxu | Dokumentace Microsoftu
description: Pomocí nástroje AzCopy v Linuxu nástroj Pokud chcete přesunout nebo kopírovat data do nebo z objektu blob a souboru obsahu. Kopírování dat do služby Azure Storage z místních souborů nebo kopírování dat v rámci nebo mezi účty úložiště. Snadno migrate data do služby Azure Storage.
services: storage
author: seguler
ms.service: storage
ms.topic: article
ms.date: 04/26/2018
ms.author: seguler
ms.subservice: common
ms.openlocfilehash: 1059dec80dcca80380b58aa8057162679496adc6
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55467369"
---
# <a name="transfer-data-with-azcopy-on-linux"></a>Přenos dat pomocí AzCopy v Linuxu

AzCopy je nástroj příkazového řádku určený pro kopírování dat do a z úložiště objektů Blob v Microsoft Azure a soubor, pomocí jednoduchých příkazů, které jsou navržené pro zajištění optimálního výkonu. Data můžete kopírovat mezi systémem souborů a účtem úložiště nebo mezi účty úložiště.  

Existují dvě verze AzCopy, který si můžete stáhnout. AzCopy v Linuxu cílí na platformy Linuxu nabízí možnosti příkazového řádku stylu POSIX. [AzCopy ve Windows](../storage-use-azcopy.md) stylu Windows nabízí možnosti příkazového řádku. Tento článek se týká AzCopy v Linuxu. 

> [!NOTE]  
> Od verze AzCopy 7.2 se závislosti .NET Core jsou zabaleny s balíčkem AzCopy. Pokud používáte verzi 7.2 nebo novější, už nebude potřeba jako předpoklad instalace .NET Core.

## <a name="download-and-install-azcopy"></a>Stáhněte a nainstalujte nástroje AzCopy

### <a name="installation-on-linux"></a>Instalace v Linuxu

> [!NOTE]
> Možná budete muset nainstalovat .NET Core 2.1 závislosti zvýrazněno to [článku .NET Core předpoklady](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x) v závislosti na vaší distribuci. 
>
> Pro distribuce RHEL 7 nainstalujte ICU a libunwind závislosti: ```yum install -y libunwind icu```

Instalace nástroje AzCopy v Linuxu (v7.2 nebo novější) je stejně jednoduché jako extrahování balíčku cíl a spuštění skriptu install. 

**Distribucích založených na systému RHEL 6**: [odkaz ke stažení](https://aka.ms/downloadazcopylinuxrhel6)
```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinuxrhel6
tar -xf azcopy.tar.gz
sudo ./install.sh
```

**Ostatní Linuxové distribuce**: [odkaz ke stažení](https://aka.ms/downloadazcopylinux64)
```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
tar -xf azcopy.tar.gz
sudo ./install.sh
```

Extrahované soubory můžete odebrat po instalaci nástroje AzCopy v Linuxu. Případně, pokud nemáte oprávnění superuživatele můžete také spustit `azcopy` pomocí azcopy skriptu prostředí v extrahovanou složku.

### <a name="alternative-installation-on-ubuntu"></a>Alternativní instalace na Ubuntu

**Ubuntu 14.04**

Přidání zdroje apt úložiště produktu Linux společnosti Microsoft a nainstalujte nástroje AzCopy:

```bash
sudo echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod/ trusty main" > azure.list
sudo cp ./azure.list /etc/apt/sources.list.d/
sudo apt-key adv --keyserver packages.microsoft.com --recv-keys EB3E94ADBE1229CF
```

```bash
sudo apt-get update
sudo apt-get install azcopy
```

**Ubuntu 16.04**

Přidání zdroje apt úložiště produktu Linux společnosti Microsoft a nainstalujte nástroje AzCopy:

```bash
echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod/ xenial main" > azure.list
sudo cp ./azure.list /etc/apt/sources.list.d/
sudo apt-key adv --keyserver packages.microsoft.com --recv-keys EB3E94ADBE1229CF
```

```bash
sudo apt-get update
sudo apt-get install azcopy
```

## <a name="writing-your-first-azcopy-command"></a>Zápis prvního příkazu AzCopy
Základní syntaxe pro příkazy AzCopy je:

```azcopy
azcopy --source <source> --destination <destination> [Options]
```

Následující příklady ukazují různé scénáře pro kopírování dat do a z Microsoft Azure Blobs a souborů. Odkazovat `azcopy --help` nabídky podrobné vysvětlení parametrů použitých v každém vzorku.

## <a name="blob-download"></a>Objekt BLOB: Ke stažení
### <a name="download-single-blob"></a>Stáhněte si jeden objekt blob

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key> 
```

Pokud složka `/mnt/myfiles` buď neexistuje, AzCopy ho vytvoří a stáhne `abc.txt ` do nové složky. 

### <a name="download-single-blob-from-secondary-region"></a>Stáhněte si jeden objekt blob ze sekundární oblasti

```azcopy
azcopy \
    --source https://myaccount-secondary.blob.core.windows.net/mynewcontainer/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key>
```

Všimněte si, že musí mít povolené geograficky redundantní úložiště jen pro čtení.

### <a name="download-all-blobs"></a>Stáhnout všechny objekty BLOB

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

Předpokládejme, že následující objekty BLOB jsou umístěny v zadaném kontejneru:  

```
abc.txt
abc1.txt
abc2.txt
vd1/a.txt
vd1/abcd.txt
```

Po provedení této operace stažení adresáři `/mnt/myfiles` zahrnuje následující soubory:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/vd1/a.txt
/mnt/myfiles/vd1/abcd.txt
```

Pokud nezadáte možnost `--recursive`, stáhnou se žádný objekt blob.

### <a name="download-blobs-with-specified-prefix"></a>Stažení objektů BLOB pomocí zadané předpony

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "a" \
    --recursive
```

Předpokládejme, že následující objekty BLOB jsou umístěny v zadaném kontejneru. Všechny objekty BLOB začínající předponou `a` se stáhnou.

```
abc.txt
abc1.txt
abc2.txt
xyz.txt
vd1\a.txt
vd1\abcd.txt
```

Po provedení této operace stažení složce `/mnt/myfiles` zahrnuje následující soubory:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
```

Předpona, která se vztahuje na virtuální adresář, který tvoří první část názvu objektu blob. Ve výše uvedeném příkladu virtuální adresář neodpovídá zadané předpony, proto je stažen žádný objekt blob. Kromě toho pokud možnost `--recursive` není zadán, AzCopy nestáhne žádné objekty BLOB.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Nastavit čas poslední změny exportované soubory na stejnou hodnotu jako objekty BLOB zdroje

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination "/mnt/myfiles" \
    --source-key <key> \
    --preserve-last-modified-time
```

Objekty BLOB můžete také vyloučit z operace stahování na základě času jejich poslední úpravy. Pokud budete chtít vyloučit objekty BLOB, jehož poslední změny času je například stejnou nebo novější, než cílový soubor, přidejte `--exclude-newer` možnost:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-newer
```

Nebo pokud budete chtít vyloučit objekty BLOB, čas poslední změny je na stejné nebo starší než cílový soubor, přidejte `--exclude-older` možnost:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-older
```

## <a name="blob-upload"></a>Objekt BLOB: Odeslat
### <a name="upload-single-file"></a>Nahrání jednoho souboru

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer/abc.txt \
    --dest-key <key>
```

Pokud zadaný cílový kontejner neexistuje, AzCopy ho vytvoří a soubor do něj nahraje.

### <a name="upload-single-file-to-virtual-directory"></a>Nahrání jednoho souboru do virtuálního adresáře

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer/vd/abc.txt \
    --dest-key <key>
```

Pokud zadaný virtuální adresář neexistuje, AzCopy nahraje soubor, který má obsahovat virtuální adresář v názvu objektu blob (*například*, `vd/abc.txt` v předchozím příkladu).

### <a name="redirect-from-stdin"></a>Přesměrovat ze stdin

```azcopy
gzip myarchive.tar -c | azcopy \
    --destination https://myaccount.blob.core.windows.net/mycontainer/mydir/myarchive.tar.gz \
    --dest-key <key>
```

### <a name="upload-all-files"></a>Nahrání všech souborů

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive
```

Zadání možnosti `--recursive` nahraje do objektu Blob úložiště rekurzivně, což znamená, že všechny podsložky a jejich soubory jsou odeslány také obsah zadaného adresáře. Například předpokládejme následující soubory jsou umístěny ve složce `/mnt/myfiles`:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

Po provedení této operace nahrávání kontejneru zahrnuje následující soubory:

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

Pokud možnost `--recursive` není zadán, jsou odeslány pouze následující tři soubory:

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="upload-files-matching-specified-pattern"></a>Nahrát soubory odpovídající zadanému vzoru

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --include "a*" \
    --recursive
```

Předpokládejme následující soubory jsou umístěny ve složce `/mnt/myfiles`:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/xyz.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

Po provedení této operace nahrávání kontejneru zahrnuje následující soubory:

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

Pokud možnost `--recursive` není zadán, soubory, které jsou v podadresářích přeskočí AzCopy:

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Zadejte typ obsahu MIME cílový objekt blob
Ve výchozím nastavení, AzCopy nastaví typ obsahu cílový objekt blob do `application/octet-stream`. Ale můžete explicitně určit typ obsahu prostřednictvím možnost `--set-content-type [content-type]`. Tato syntaxe nastaví typ obsahu pro všechny objekty BLOB v rámci operace nahrávání.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type "video/mp4"
```

Pokud možnost `--set-content-type` je zadaný bez hodnoty, AzCopy nastaví každý objekt blob nebo typ obsahu souboru podle jeho přípony souboru.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type
```

### <a name="customizing-the-mime-content-type-mapping"></a>Přizpůsobení mapování typ obsahu MIME
AzCopy používá konfigurační soubor, který obsahuje mapování souboru rozšíření typu obsahu. Můžete přizpůsobit toto mapování a přidat nové páry podle potřeby. Mapování se nachází v  ```/usr/lib/azcopy/AzCopyConfig.json```

## <a name="blob-copy"></a>Objekt BLOB: Kopírovat
### <a name="copy-single-blob-within-storage-account"></a>Jeden objekt blob kopírování v rámci účtu úložiště

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-key <key> \
    --dest-key <key>
```

Při kopírování objektu blob bez – možnosti synchronizace kopírování [kopírování na straně serveru](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) proběhlo.

### <a name="copy-single-blob-across-storage-accounts"></a>Zkopírujte jeden objekt blob mezi účty úložiště

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://destaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-key <key1> \
    --dest-key <key2>
```

Při kopírování objektu blob bez – možnosti synchronizace kopírování [kopírování na straně serveru](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) proběhlo.

### <a name="copy-single-blob-from-secondary-region-to-primary-region"></a>Zkopírujte jeden objekt blob ze sekundární oblasti do primární oblasti

```azcopy
azcopy \
    --source https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1/abc.txt \
    --destination https://myaccount2.blob.core.windows.net/mynewcontainer2/abc.txt \
    --source-key <key1> \
    --dest-key <key2>
```

Všimněte si, že musí mít povolené geograficky redundantní úložiště jen pro čtení.

### <a name="copy-single-blob-and-its-snapshots-across-storage-accounts"></a>Jeden objekt blob kopírování a její snímky mezi různými účty úložiště

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1/ \
    --destination https://destaccount.blob.core.windows.net/mycontainer2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt" \
    --include-snapshot
```

Po provedení této operace kopírování obsahuje cílový kontejner objektu blob a jeho snímků. Kontejner obsahuje následující objekt blob a jeho snímky:

```
abc.txt
abc (2013-02-25 080757).txt
abc (2014-02-21 150331).txt
```

### <a name="synchronously-copy-blobs-across-storage-accounts"></a>Synchronně kopírování objektů BLOB mezi účty úložiště
AzCopy ve výchozím nastavení asynchronně kopíruje data mezi dva koncové body služby úložiště. Proto je zkopírován kopírování spuštění operace na pozadí pomocí kapacity přebytečné šířky pásma, která nemá žádné smlouvy SLA na základě jak rychle objektu blob. 

`--sync-copy` Možnost zajišťuje, že operace kopírování získá konzistentní rychlost. AzCopy provádí synchronní kopie stažením přes bloby až po kopírování ze zadaného zdroje do místní paměti, a pak se nahrají do cíle úložiště objektů Blob.

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/myContainer/ \
    --destination https://myaccount2.blob.core.windows.net/myContainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "ab" \
    --sync-copy
```

`--sync-copy` náklady na další výchozí přenos dat ve srovnání s asynchronní kopie může vygenerovat. Doporučený postup je ve virtuálním počítači Azure, který je ve stejné oblasti jako váš účet úložiště zdroje, aby se zabránilo náklady na výchozí přenos dat pomocí této možnosti.

## <a name="file-download"></a>Soubor: Ke stažení
### <a name="download-single-file"></a>Stažení jednotlivých souborů

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/myfolder1/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key>
```

Pokud je zadaný zdroj sdílené složky Azure, pak buď musíte zadat přesný název souboru, (*například* `abc.txt`) ke stažení jednoho souboru nebo možnost `--recursive` ke stažení všechny soubory ve sdílené složce rekurzivně. Pokus zadat vzor souborů i možnost `--recursive` společně dojde k chybě.

### <a name="download-all-files"></a>Stáhnout všechny soubory

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/ \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

Všimněte si, že všechny prázdné složky nestahují.

## <a name="file-upload"></a>Soubor: Odeslat
### <a name="upload-single-file"></a>Nahrání jednoho souboru

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.file.core.windows.net/myfileshare/abc.txt \
    --dest-key <key>
```

### <a name="upload-all-files"></a>Nahrání všech souborů

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --recursive
```

Všimněte si, že nejsou uloženy žádné prázdné složky.

### <a name="upload-files-matching-specified-pattern"></a>Nahrát soubory odpovídající zadanému vzoru

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --include "ab*" \
    --recursive
```

## <a name="file-copy"></a>Soubor: Kopírovat
### <a name="copy-across-file-shares"></a>Kopírovat na sdílené složky

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Při kopírování souboru ve sdílené složky [kopírování na straně serveru](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) proběhlo.

### <a name="copy-from-file-share-to-blob"></a>Kopírování ze sdílené souborové položky do objektu blob

```azcopy
azcopy \ 
    --source https://myaccount1.file.core.windows.net/myfileshare/ \
    --destination https://myaccount2.blob.core.windows.net/mycontainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Při kopírování souboru ze sdílené souborové položky do objektu blob, [kopírování na straně serveru](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) proběhlo.

### <a name="copy-from-blob-to-file-share"></a>Zkopírujte z objektu blob do sdílené složky

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/mycontainer/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Při kopírování souboru z objektu blob do sdílené složky, [kopírování na straně serveru](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) proběhlo.

### <a name="synchronously-copy-files"></a>Synchronně zkopírovat soubory
Můžete zadat `--sync-copy` možnost ke zkopírování dat z úložiště souborů do služby File Storage, File Storage do služby Blob Storage a z úložiště objektů Blob do služby File Storage synchronně. AzCopy se spustí tuto operaci stažením zdrojových dat k místní paměti, a pak pak ho nahrát do cíle. V tomto případě platí poplatky za standardní výstup.

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive \
    --sync-copy
```

Při kopírování ze souboru úložiště do úložiště objektů Blob, výchozí typ objektu blob je objekty blob bloku, může uživatel zadat možnost `--blob-type page` změnit typ cílového objektu blob. Dostupné typy jsou `page | block | append`.

Všimněte si, že `--sync-copy` může generovat další náklady porovnává se s asynchronní kopírování výstup. Doporučený postup je ve virtuálním počítači Azure, který je ve stejné oblasti jako váš účet úložiště zdroje, aby se zabránilo náklady na výchozí přenos dat pomocí této možnosti.

## <a name="other-azcopy-features"></a>Jiné funkce nástroje AzCopy
### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Kopírovat pouze data, která neexistuje v cílovém umístění
`--exclude-older` a `--exclude-newer` parametry umožňují vyloučit prostředky staršího nebo novějšího zdroje jsou zkopírovány v uvedeném pořadí. Pokud chcete zkopírovat zdrojové prostředky, které neexistují v cíli, můžete zadat oba parametry do příkazu AzCopy:

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --exclude-older --exclude-newer

    --source /mnt/myfiles --destination http://myaccount.file.core.windows.net/myfileshare --dest-key <destkey> --recursive --exclude-older --exclude-newer

    --source http://myaccount.blob.core.windows.net/mycontainer --destination http://myaccount.blob.core.windows.net/mycontainer1 --source-key <sourcekey> --dest-key <destkey> --recursive --exclude-older --exclude-newer

### <a name="use-a-configuration-file-to-specify-command-line-parameters"></a>Zadejte parametry příkazového řádku pomocí konfiguračního souboru

```azcopy
azcopy --config-file "azcopy-config.ini"
```

V konfiguračním souboru můžete zahrnout všechny parametry příkazového řádku AzCopy. AzCopy zpracovává parametry v souboru jako v případě, kdyby byly zadány v příkazovém řádku provádění s přímým přístupem nahrazení s obsahem souboru.

Předpokládejme konfigurační soubor s názvem `copyoperation`, který obsahuje následující řádky. Jednotlivé parametry nástroje AzCopy můžete zadat na jednom řádku.

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --quiet

nebo na samostatné řádky:

    --source http://myaccount.blob.core.windows.net/mycontainer
    --destination /mnt/myfiles
    --source-key<sourcekey>
    --recursive
    --quiet

AzCopy se nezdaří, pokud je parametr rozdělit mezi dva řádky, jak je znázorněno zde pro `--source-key` parametr:

    http://myaccount.blob.core.windows.net/mycontainer
    /mnt/myfiles
    --sourcekey
    <sourcekey>
    --recursive
    --quiet

### <a name="specify-a-shared-access-signature-sas"></a>Zadejte sdílený přístupový podpis (SAS)

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-sas <SAS1> \
    --dest-sas <SAS2>
```

Můžete také zadat SAS URI kontejneru:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken \
    --destination /mnt/myfiles \
    --recursive
```

### <a name="journal-file-folder"></a>Složka souboru deníku
Pokaždé, když vydáte příkaz azcopy, zkontroluje, jestli existuje soubor deníku ve výchozí složce, nebo určuje, zda existuje ve složce, která jste zadali pomocí této možnosti. Pokud soubor deníku na jednom místě neexistuje, AzCopy zpracovává operace jako nové a vygeneruje nový soubor deníku.

Pokud soubor deníku neexistuje, AzCopy zkontroluje, zda odpovídá příkazového řádku, který zadáte na příkazovém řádku ve soubor deníku. Pokud se shodují se dvěma příkazové řádky, AzCopy obnoví neúplná operace. Pokud shodné nejsou, AzCopy vyzve uživatele k buď přepsat soubor deníku spustit novou operaci, nebo zrušit aktuální operaci.

Pokud chcete použít výchozí umístění pro soubor deníku:

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --resume
```

Pokud vynecháte možnost `--resume`, nebo možnost `--resume` bez cesty ke složce, jak je uvedeno výše, AzCopy vytvoří soubor deníku ve výchozím umístění, což je `~\Microsoft\Azure\AzCopy`. Pokud už existuje soubor deníku, AzCopy obnoví operace na základě souboru deníku.

Pokud chcete zadat vlastní umístění pro soubor deníku:

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key key \
    --resume "/mnt/myjournal"
```

Tento příklad vytvoří soubor deníku, pokud ještě neexistuje. Pokud neexistuje, AzCopy obnoví operace na základě souboru deníku.

Pokud chcete pokračovat v operaci AzCopy, opakujte stejný příkaz. AzCopy v Linuxu potom zobrazí výzvu k potvrzení:

```azcopy
Incomplete operation with same command line detected at the journal directory "/home/myaccount/Microsoft/Azure/AzCopy", do you want to resume the operation? Choose Yes to resume, choose No to overwrite the journal to start a new operation. (Yes/No)
```

### <a name="output-verbose-logs"></a>Podrobné protokoly výstupu

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --verbose
```

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Zadejte počet souběžných operací spuštění
Možnost `--parallel-level` určuje počet souběžných kopírování. Ve výchozím nastavení spustí AzCopy počet souběžných operací, pokud chcete zvýšit propustnost dat přenos. Počet souběžných operací se rovná osm časy počet procesorů, které máte. Pokud spustíte AzCopy přes síť s malou šířkou pásma, můžete zadat nižší číslo pro – paralelní úroveň, aby se nezdařila z důvodu podle soutěže prostředků.

>[!TIP]
>Chcete-li zobrazit úplný seznam AzCopy parametry, podívejte se na "azcopy--help" nabídky.

## <a name="installation-steps-for-azcopy-71-and-earlier-versions"></a>Postup instalace AzCopy 7.1 a starší verze

AzCopy v Linuxu (verze 7.1 a dřívější) vyžaduje rozhraní .NET Core framework. Pokyny k instalaci jsou k dispozici na [instalace .NET Core](https://www.microsoft.com/net/core#linuxubuntu) stránky.

Například začněte tím, že instalace .NET Core v Ubuntu 16.10. Nejnovější instalační příručce najdete [.NET Core v Linuxu](https://www.microsoft.com/net/core#linuxubuntu) stránku instalace.


```bash
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ yakkety main" > /etc/apt/sources.list.d/dotnetdev.list' 
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
sudo apt-get update
sudo apt-get install dotnet-sdk-2.0.0
```

Po instalaci rozhraní .NET Core, stáhněte a nainstalujte nástroj AzCopy.

```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopyprlinux
tar -xf azcopy.tar.gz
sudo ./install.sh
```

Extrahované soubory můžete odebrat po instalaci nástroje AzCopy v Linuxu. Případně pokud nemáte oprávnění superuživatele, můžete také spustit `azcopy` pomocí azcopy skriptu prostředí v extrahovanou složku.

## <a name="known-issues-and-best-practices"></a>Známé problémy a osvědčené postupy
### <a name="error-installing-azcopy"></a>Chyba při instalaci nástroje AzCopy
Pokud narazíte na problémy s instalací nástroje AzCopy, zkuste spustit AzCopy pomocí skriptu bash v extrahované `azcopy` složky.

```bash
cd azcopy
./azcopy
```

### <a name="limit-concurrent-writes-while-copying-data"></a>Limit souběžných zápisů při kopírování dat
Při kopírování objektů BLOB nebo soubory pomocí nástroje AzCopy, mějte na paměti, že jiná aplikace může být úpravy dat během kopírování ji. Pokud je to možné Ujistěte se, že data, která kopírujete nemění během operace kopírování. Například při kopírování virtuálního pevného disku přidružený k virtuálnímu počítači Azure, ujistěte se, že aktuálně psaní žádné jiné aplikace na virtuální pevný disk. Dobrým způsobem, jak to provést, je zapůjčení prostředek, který chcete zkopírovat. Alternativně můžete nejprve vytvořte snímek virtuální pevný disk a potom zkopírovat snímek.

Pokud nelze zabránit aplikacím možnost zapisovat do objektů BLOB nebo soubory, zatímco se kopírují, pak mějte na paměti, že v době, kdy se úloha dokončí, zkopírované prostředky může už nebude mít úplnou paritu s prostředky zdroje.

### <a name="running-multiple-azcopy-processes"></a>Spuštěno více procesů, nástroje AzCopy
Můžete spustit více procesů AzCopy na jednoho klienta, zajištění, že používáte jiný deníku složky. Použití jedné deníku složku pro více procesů AzCopy není podporováno.

1. proces:
```azcopy
azcopy \
    --source /mnt/myfiles1 \
    --destination https://myaccount.blob.core.windows.net/mycontainer/myfiles1 \
    --dest-key <key> \
    --resume "/mnt/myazcopyjournal1"
```

2. proces:
```azcopy
azcopy \
    --source /mnt/myfiles2 \
    --destination https://myaccount.blob.core.windows.net/mycontainer/myfiles2 \
    --dest-key <key> \
    --resume "/mnt/myazcopyjournal2"
```

## <a name="next-steps"></a>Další postup
Další informace o službě Azure Storage a AzCopy najdete v následujících zdrojích informací:

### <a name="azure-storage-documentation"></a>Dokumentace k Azure Storage:
* [Seznámení se službou Azure Storage](../storage-introduction.md)
* [Vytvoření účtu úložiště](../storage-create-storage-account.md)
* [Správa objektů blob pomocí Průzkumníka služby Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)
* [Použití Azure CLI s Azure Storage](../storage-azure-cli.md)
* [Používání úložiště Blob z jazyka C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Používání úložiště Blob z Javy](../blobs/storage-java-how-to-use-blob-storage.md)
* [Používání úložiště Blob z Node.js](../blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Používání úložiště Blob z Pythonu](../blobs/storage-python-how-to-use-blob-storage.md)

### <a name="azure-storage-blog-posts"></a>Azure Storage blogových příspěvků:
* [Oznamujeme vydání AzCopy v Linuxu ve verzi Preview](https://azure.microsoft.com/blog/announcing-azcopy-on-linux-preview/)
* [Úvod do služby Azure Storage dat přesun knihovny ve verzi Preview](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [AzCopy: Představujeme synchronní kopie a vlastní typ obsahu](https://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [AzCopy: Oznámení obecné dostupnosti AzCopy 3.0 a preview verze AzCopy 4.0 s podporou Table a File](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [AzCopy: Optimalizovaná pro scénáře, ve velkém měřítku kopírování](https://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy: Podpora pro geograficky redundantní úložiště jen pro čtení](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [AzCopy: Přenos dat pomocí restartovatelnou službu režimu a tokenu SAS](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
* [AzCopy: Použití objektů Blob v účtu různé kopie](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [AzCopy: Nahrávání a stahování souborů pro objekty BLOB Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)
