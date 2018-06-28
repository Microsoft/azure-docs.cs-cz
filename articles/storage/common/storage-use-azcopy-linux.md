---
title: Zkopírovat nebo přesunout data do služby Azure Storage s AzCopy v systému Linux | Microsoft Docs
description: Pomocí AzCopy na nástroj Linux přesunutí nebo zkopírování dat do nebo z objektu blob a obsahu souborů. Kopírování dat do úložiště Azure z místních souborů, nebo zkopírujte data v rámci nebo mezi účty úložiště. Snadno migrujte data do úložiště Azure.
services: storage
documentationcenter: ''
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: aa155738-7c69-4a83-94f8-b97af4461274
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: seguler
ms.openlocfilehash: 3ed449912df1e16b5c8f1dfa3c83b81eaf635227
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035799"
---
# <a name="transfer-data-with-azcopy-on-linux"></a>Přenos dat pomocí nástroje AzCopy v systému Linux

AzCopy je nástroj příkazového řádku pro kopírování dat z úložiště objektů Blob v Microsoft Azure a soubor jednoduché příkazy určené pro optimální výkon. Data můžete kopírovat mezi systémem souborů a účtem úložiště nebo mezi účty úložiště.  

Existují dvě verze nástroje AzCopy, které si můžete stáhnout. AzCopy v systému Linux cílí platformy Linux nabídky stylu POSIX možnosti příkazového řádku. [AzCopy v systému Windows](../storage-use-azcopy.md) styl Windows nabízí možnosti příkazového řádku. Tento článek se zabývá AzCopy v systému Linux. 

> [!NOTE]  
> Od verze nástroje AzCopy 7.2 se závislostí .NET Core spojených s tímto balíčkem AzCopy. Pokud používáte verzi 7,2 nebo novější, již nebudou muset nainstalovat .NET Core jako nezbytný předpoklad.

## <a name="download-and-install-azcopy"></a>Stáhněte a nainstalujte AzCopy

### <a name="installation-on-linux"></a>Instalace v systému Linux

> [!NOTE]
> Možná budete muset nainstalovat rozhraní .NET Core 2.1 závislosti zvýrazněných v tomto [článku .NET Core předpoklady](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x) v závislosti na vaší distribuce. 
>
> Pro RHEL 7 distribuce instalace ICU a libunwind závislosti: ```yum install -y libunwind icu```

Instalace nástroje AzCopy v systému Linux (v7.2 nebo novější) je stejně snadná jako extrahování balíčku vkládání a spuštění skriptu install. 

**RHEL 6 na základě distribuce**: [stáhnout odkaz](https://aka.ms/downloadazcopylinuxrhel6)
```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinuxrhel6
tar -xf azcopy.tar.gz
sudo ./install.sh
```

**Všechny ostatní Linuxových distribucích**: [stáhnout odkaz](https://aka.ms/downloadazcopylinux64)
```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
tar -xf azcopy.tar.gz
sudo ./install.sh
```

Po instalaci nástroje AzCopy v systému Linux můžete odebrat extrahované soubory. Případně, pokud nemáte oprávnění superuživatele můžete také spustit `azcopy` pomocí azcopy skriptu prostředí v extrahovanou složku.

### <a name="alternative-installation-on-ubuntu"></a>Alternativní instalace na Ubuntu

**Ubuntu 14.04**

Přidejte výstižný zdroj pro produkt úložiště Linux společnosti Microsoft a nainstalujte AzCopy:

```bash
sudo echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod/ trusty main" > azure.list
sudo cp ./azure.list /etc/apt/sources.list.d/
apt-key adv --keyserver packages.microsoft.com --recv-keys B02C46DF417A0893
```

```bash
sudo apt-get update
sudo apt-get install azcopy
```

**Ubuntu 16.04**

Přidejte výstižný zdroj pro produkt úložiště Linux společnosti Microsoft a nainstalujte AzCopy:

```bash
echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod/ xenial main" > azure.list
sudo cp ./azure.list /etc/apt/sources.list.d/
apt-key adv --keyserver packages.microsoft.com --recv-keys B02C46DF417A0893
```

```bash
sudo apt-get update
sudo apt-get install azcopy
```

## <a name="writing-your-first-azcopy-command"></a>Zápis vaše první příkaz AzCopy
Základní syntaxe pro příkazy AzCopy je:

```azcopy
azcopy --source <source> --destination <destination> [Options]
```

Následující příklady ukazují různé scénáře pro kopírování dat do a z Microsoft Azure Blobs a soubory. Odkazovat `azcopy --help` nabídky podrobné vysvětlení parametrů použitých v každém vzorku.

## <a name="blob-download"></a>Objekt BLOB: stažení
### <a name="download-single-blob"></a>Stáhnout jediného objektu blob

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key> 
```

Pokud složka `/mnt/myfiles` neexistuje, AzCopy ji vytvoří a stáhne `abc.txt ` do nové složky. 

### <a name="download-single-blob-from-secondary-region"></a>Stáhnout jediného objektu blob ze sekundární oblasti

```azcopy
azcopy \
    --source https://myaccount-secondary.blob.core.windows.net/mynewcontainer/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key>
```

Všimněte si, že je nutné mít přístup pro čtení geograficky redundantní úložiště s povoleným.

### <a name="download-all-blobs"></a>Stažení všech objektů BLOB

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

Předpokládejme, že následující objekty BLOB se nacházejí v zadaném kontejneru:  

```
abc.txt
abc1.txt
abc2.txt
vd1/a.txt
vd1/abcd.txt
```

Po stažení operaci adresáři `/mnt/myfiles` zahrnuje následující soubory:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/vd1/a.txt
/mnt/myfiles/vd1/abcd.txt
```

Pokud nezadáte možnost `--recursive`, budou staženy žádné objektů blob.

### <a name="download-blobs-with-specified-prefix"></a>Stáhnout objekty BLOB se zadanou předponou.

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "a" \
    --recursive
```

Předpokládejme, že následující objekty BLOB se nacházejí v zadaném kontejneru. Všechny objekty BLOB začínající předponou `a` staženy.

```
abc.txt
abc1.txt
abc2.txt
xyz.txt
vd1\a.txt
vd1\abcd.txt
```

Po stažení operaci složce `/mnt/myfiles` zahrnuje následující soubory:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
```

Předpona, která se vztahuje na virtuální adresář, který tvoří první část názvu objektu blob. Ve výše uvedeném příkladu virtuální adresář neodpovídá zadané předpony, takže se stáhne žádné objektů blob. Kromě toho pokud možnost `--recursive` není zadán, AzCopy nestáhne žádné objekty BLOB.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Nastavte čas poslední úpravy exportované soubory na stejnou hodnotu jako zdroj objektů BLOB

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination "/mnt/myfiles" \
    --source-key <key> \
    --preserve-last-modified-time
```

Můžete také vyloučit objekty BLOB z operace stažení podle jejich čas poslední změny. Pokud chcete vyloučit objekty BLOB, jejichž poslední úpravy času je například stejnou nebo novější, než cílový soubor, přidejte `--exclude-newer` možnost:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-newer
```

Nebo pokud chcete vyloučit objekty BLOB, jejichž poslední úpravy času je na stejné nebo starší než cílový soubor, přidejte `--exclude-older` možnost:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-older
```

## <a name="blob-upload"></a>Objekt BLOB: nahrát
### <a name="upload-single-file"></a>Odeslat jedním souborem

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer/abc.txt \
    --dest-key <key>
```

Pokud zadaný cílový kontejner neexistuje, AzCopy ho vytvoří a soubor do něj nahraje.

### <a name="upload-single-file-to-virtual-directory"></a>Jediný soubor nahrát do virtuálního adresáře

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer/vd/abc.txt \
    --dest-key <key>
```

Pokud zadaný virtuální adresář neexistuje, AzCopy nahrávání souboru v názvu objektu blob virtuálního adresáře (*například*, `vd/abc.txt` v předchozím příkladu).

### <a name="redirect-from-stdin"></a>Přesměrování z stdin –

```azcopy
gzip myarchive.tar -c | azcopy \
    --destination https://myaccount.blob.core.windows.net/mycontainer/mydir/myarchive.tar.gz \
    --dest-key <key>
```

### <a name="upload-all-files"></a>Odeslat všechny soubory.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive
```

Zadání `--recursive` odešle obsah zadaného adresáře k rekurzivnímu úložiště objektů Blob, což znamená, že jsou také odeslány všechny podsložky a jejich soubory. Například předpokládejme následující soubory jsou umístěny ve složce `/mnt/myfiles`:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

Po operaci odeslání kontejneru obsahuje následující soubory:

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

### <a name="upload-files-matching-specified-pattern"></a>Nahrání souborů odpovídající zadanému vzoru

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

Po operaci odeslání kontejneru obsahuje následující soubory:

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

Pokud možnost `--recursive` není zadán, AzCopy přeskočí soubory, které se nacházejí v podadresářích:

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Zadejte typ MIME obsahu cílový objekt blob
Ve výchozím nastavení, AzCopy nastaví typ obsahu cílový objekt blob do `application/octet-stream`. Však můžete explicitně zadat typ obsahu prostřednictvím možnosti `--set-content-type [content-type]`. Tuto syntaxi nastaví typ obsahu pro všechny objekty BLOB v operaci odeslání.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type "video/mp4"
```

Pokud možnost `--set-content-type` je zadán bez hodnoty AzCopy nastaví jednotlivých objektů blob nebo typ obsahu souboru podle jeho přípony.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type
```

## <a name="blob-copy"></a>Objekt BLOB: kopírování
### <a name="copy-single-blob-within-storage-account"></a>Zkopírujte jediného objektu blob v rámci účtu úložiště

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-key <key> \
    --dest-key <key>
```

Při kopírování objektu blob bez – možnosti synchronní kopie, [serverové kopie](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operace.

### <a name="copy-single-blob-across-storage-accounts"></a>Zkopírujte jediného objektu blob mezi různými účty úložiště

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://destaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-key <key1> \
    --dest-key <key2>
```

Při kopírování objektu blob bez – možnosti synchronní kopie, [serverové kopie](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operace.

### <a name="copy-single-blob-from-secondary-region-to-primary-region"></a>Zkopírujte jediného objektu blob ze sekundární oblasti primární oblasti

```azcopy
azcopy \
    --source https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1/abc.txt \
    --destination https://myaccount2.blob.core.windows.net/mynewcontainer2/abc.txt \
    --source-key <key1> \
    --dest-key <key2>
```

Všimněte si, že je nutné mít přístup pro čtení geograficky redundantní úložiště s povoleným.

### <a name="copy-single-blob-and-its-snapshots-across-storage-accounts"></a>Zkopírujte jediného objektu blob a jeho snímky mezi různými účty úložiště

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1/ \
    --destination https://destaccount.blob.core.windows.net/mycontainer2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt" \
    --include-snapshot
```

Po operaci kopírování zahrnuje cílový kontejner objektu blob a jeho snímků. Kontejner obsahuje následující objektu blob a jeho snímky:

```
abc.txt
abc (2013-02-25 080757).txt
abc (2014-02-21 150331).txt
```

### <a name="synchronously-copy-blobs-across-storage-accounts"></a>Synchronně kopírovat mezi různými účty úložiště objektů BLOB
AzCopy ve výchozím nastavení zkopíruje data mezi dva koncové body úložiště asynchronně. Operace kopírování se spustí v pozadí využití kapacity přebytečné šířky pásma, která nemá žádné SLA z hlediska jak rychlé objekt blob je proto zkopírovány. 

`--sync-copy` Možnost zajistí, že operace kopírování získá konzistentní rychlost. AzCopy provede synchronní kopie stažením objektů blob pro kopírování ze zadaného zdroje do místní paměti a uložte je do cílového umístění úložiště objektů Blob.

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/myContainer/ \
    --destination https://myaccount2.blob.core.windows.net/myContainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "ab" \
    --sync-copy
```

`--sync-copy` může generovat další odchozí nákladů ve srovnání s asynchronní kopírování. Doporučený přístup je pro tuto možnost použijte virtuální počítač Azure, který je ve stejné oblasti jako váš účet úložiště zdroj předejdete odchozí náklady.

## <a name="file-download"></a>Soubor: stažení
### <a name="download-single-file"></a>Stáhnout jedním souborem

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/myfolder1/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key>
```

Pokud je zadaný zdroj sdílenou složku Azure, pak buď musíte zadat přesný název souboru, (*například* `abc.txt`) ke stažení jeden soubor nebo zadejte možnost `--recursive` ke stažení všechny soubory ve sdílené složce rekurzivně. Probíhá pokus o zadat šablonu souboru a možnost `--recursive` společně dojde k chybě.

### <a name="download-all-files"></a>Stáhnout všechny soubory

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/ \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

Všimněte si, že všechny prázdné složky nestahují.

## <a name="file-upload"></a>Soubor: nahrát
### <a name="upload-single-file"></a>Odeslat jedním souborem

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.file.core.windows.net/myfileshare/abc.txt \
    --dest-key <key>
```

### <a name="upload-all-files"></a>Odeslat všechny soubory.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --recursive
```

Všimněte si, že nejsou uloženy žádné prázdné složky.

### <a name="upload-files-matching-specified-pattern"></a>Nahrání souborů odpovídající zadanému vzoru

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --include "ab*" \
    --recursive
```

## <a name="file-copy"></a>Soubor: kopírování
### <a name="copy-across-file-shares"></a>Kopírování mezi sdílenými složkami

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Při kopírování souboru mezi sdílenými složkami [serverové kopie](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operace.

### <a name="copy-from-file-share-to-blob"></a>Kopírování ze sdílené složky do objektu blob

```azcopy
azcopy \ 
    --source https://myaccount1.file.core.windows.net/myfileshare/ \
    --destination https://myaccount2.blob.core.windows.net/mycontainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Při kopírování souboru ze sdílené složky do objektu blob, [serverové kopie](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operace.

### <a name="copy-from-blob-to-file-share"></a>Zkopírujte z objektu blob do sdílené složky

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/mycontainer/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Při kopírování souboru z objektu blob do sdílené složky [serverové kopie](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operace.

### <a name="synchronously-copy-files"></a>Synchronně kopírování souborů
Můžete zadat `--sync-copy` možnost ke zkopírování dat z úložiště File k úložišti souborů ze souboru úložiště do úložiště objektů Blob a z úložiště objektů Blob k úložišti souborů synchronně. AzCopy spustí tuto operaci stahování zdrojová data do místní paměti a odesílá je do cílového umístění. V takovém případě platí standardní výstupní náklady.

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive \
    --sync-copy
```

Při kopírování ze souboru úložiště do úložiště objektů Blob, výchozí typ objektu blob je objekt blob bloku, může uživatel zadat možnost `--blob-type page` Chcete-li změnit typ cílového objektu blob. Dostupné typy jsou `page | block | append`.

Všimněte si, že `--sync-copy` může generovat další odchozí náklady srovnáním asynchronní kopírování. Doporučený přístup je pro tuto možnost použijte virtuální počítač Azure, který je ve stejné oblasti jako váš účet úložiště zdroj předejdete odchozí náklady.

## <a name="other-azcopy-features"></a>Další funkce AzCopy
### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Kopírovat pouze data, která neexistuje v cílovém
`--exclude-older` a `--exclude-newer` parametry umožňují vyloučit prostředky starší nebo novější zdroj z kopírování, v uvedeném pořadí. Pokud chcete kopírovat zdroje prostředky, které neexistují v cílovém, můžete zadat oba parametry v příkazu AzCopy:

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --exclude-older --exclude-newer

    --source /mnt/myfiles --destination http://myaccount.file.core.windows.net/myfileshare --dest-key <destkey> --recursive --exclude-older --exclude-newer

    --source http://myaccount.blob.core.windows.net/mycontainer --destination http://myaccount.blob.core.windows.net/mycontainer1 --source-key <sourcekey> --dest-key <destkey> --recursive --exclude-older --exclude-newer

### <a name="use-a-configuration-file-to-specify-command-line-parameters"></a>Zadejte parametry příkazového řádku pomocí konfiguračního souboru

```azcopy
azcopy --config-file "azcopy-config.ini"
```

Parametry příkazového řádku AzCopy můžete zahrnout v konfiguračním souboru. AzCopy zpracovává parametry v souboru jako v případě, kdyby byly zadány na příkazovém řádku, provádění přímé nahrazení s obsahem souboru.

Předpokládejme, konfigurační soubor s názvem `copyoperation`, který obsahuje následující řádky. Každý parametr AzCopy můžete zadat na jeden řádek.

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --quiet

nebo na samostatné řádky:

    --source http://myaccount.blob.core.windows.net/mycontainer
    --destination /mnt/myfiles
    --source-key<sourcekey>
    --recursive
    --quiet

AzCopy selže, pokud rozložení parametru mezi dvěma čárami, jak je vidět tady pro `--source-key` parametr:

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

Můžete také určit SAS URI kontejneru:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken \
    --destination /mnt/myfiles \
    --recursive
```

### <a name="journal-file-folder"></a>Složky pro soubor deníku
Pokaždé, když příkaz azcopy, zkontroluje existenci souboru deníku ve výchozí složce, nebo jestli existuje ve složce, kterou jste zadali pomocí této možnosti. Pokud soubor deníku neexistuje ani na jednom místě, AzCopy zpracovává operaci jako nové a generuje nový soubor deníku.

Pokud soubor deníku neexistuje, AzCopy kontroluje, zda příkazového řádku, který můžete vložit odpovídá příkazového řádku v souboru deníku. Pokud se dva příkazové řádky shodují, obnoví AzCopy neúplné operaci. Pokud se neshodují, AzCopy vyzve uživatele k buď přepsání souboru deníku spustit novou operaci, nebo zrušit aktuální operace.

Pokud chcete použít výchozí umístění pro soubor deníku:

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --resume
```

Pokud není možnost `--resume`, nebo zadejte možnost `--resume` bez cesta ke složce, jako v příkladu nahoře, AzCopy vytvoří soubor deníku ve výchozím umístění, což je `~\Microsoft\Azure\AzCopy`. Pokud soubor deníku již existuje, AzCopy obnoví operaci na základě souboru deníku.

Pokud chcete určit vlastní umístění souboru deníku:

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key key \
    --resume "/mnt/myjournal"
```

Tento příklad vytvoří soubor deníku, pokud ještě neexistuje. Pokud neexistuje, AzCopy obnoví operaci na základě souboru deníku.

Pokud chcete pokračovat v činnosti AzCopy, zopakujte stejný příkaz. AzCopy v systému Linux potom zobrazí výzvu k potvrzení:

```azcopy
Incomplete operation with same command line detected at the journal directory "/home/myaccount/Microsoft/Azure/AzCopy", do you want to resume the operation? Choose Yes to resume, choose No to overwrite the journal to start a new operation. (Yes/No)
```

### <a name="output-verbose-logs"></a>Výstupní podrobné protokoly

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --verbose
```

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Zadejte počet souběžných operací spuštění
Možnost `--parallel-level` určuje počet souběžných kopie operací. Ve výchozím nastavení spustí AzCopy počet souběžných operací, pokud chcete zvýšit propustnost dat přenosu. Počet souběžných operací osm časy počet procesorů, které máte. Pokud používáte AzCopy přes síť s malou šířkou pásma, můžete na nižší číslo pro – paralelní úrovni, aby se zabránilo selhání kvůli konfliktům prostředků.

>[!TIP]
>Pokud chcete zobrazit úplný seznam parametrů AzCopy, podívejte se na 'azcopy – Nápověda' nabídky.

## <a name="installation-steps-for-azcopy-71-and-earlier-versions"></a>Postup instalace AzCopy 7.1 a starší verze

AzCopy v systému Linux (v7.1 a starší) vyžaduje rozhraní .NET Core. Pokyny k instalaci jsou k dispozici na [instalace .NET Core](https://www.microsoft.com/net/core#linuxubuntu) stránky.

Například začněte tím, že na Ubuntu 16.10 instalaci .NET Core. Nejnovější Průvodce instalací, najdete v článku [.NET Core v systému Linux](https://www.microsoft.com/net/core#linuxubuntu) instalační stránka.


```bash
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ yakkety main" > /etc/apt/sources.list.d/dotnetdev.list' 
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
sudo apt-get update
sudo apt-get install dotnet-sdk-2.0.0
```

Po instalaci .NET Core, stáhněte a nainstalujte AzCopy.

```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopyprlinux
tar -xf azcopy.tar.gz
sudo ./install.sh
```

Po instalaci nástroje AzCopy v systému Linux můžete odebrat extrahované soubory. Případně pokud nemáte oprávnění superuživatele, můžete také spustit `azcopy` pomocí azcopy skriptu prostředí v extrahovanou složku.

## <a name="known-issues-and-best-practices"></a>Známé problémy a doporučené postupy
### <a name="error-installing-azcopy"></a>Chyba při instalaci nástroje AzCopy
Pokud narazíte na potíže s instalací AzCopy, můžete se pokusit spustit AzCopy pomocí skriptů bash ve extrahované `azcopy` složky.

```bash
cd azcopy
./azcopy
```

### <a name="limit-concurrent-writes-while-copying-data"></a>Limit souběžných zápisy při kopírování dat
Při kopírování objektů BLOB nebo soubory s AzCopy, mějte na paměti, že jiná aplikace může být úprava dat během kopírování ho. Pokud je to možné Ujistěte se, které chcete kopírovat data nemění při kopírování. Například při kopírování virtuálního pevného disku přidružený virtuální počítač Azure, ujistěte se, že žádné další aplikace jsou aktuálně zápis na virtuální pevný disk. Dobrým způsobem, jak to udělat, je leasing prostředků, které se mají zkopírovat. Alternativně můžete nejprve vytvořte snímek virtuálního pevného disku a poté zkopírujte snímku.

Pokud nelze zabránit jiné aplikace z zápis do objektů BLOB nebo soubory, když se kopírují, pak mějte na paměti, že v době dokončení úlohy, kopírované prostředky buď již nemá úplné parita s prostředky zdroje.

### <a name="running-multiple-azcopy-processes"></a>Spuštění více procesů AzCopy
Více procesů AzCopy můžete spustit také na jednoho klienta zajištění, že používáte jiný deníku složek. Použití jedné deníku složku pro více procesů AzCopy není podporováno.

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
* [vytvořit účet úložiště](../storage-create-storage-account.md)
* [Správa objektů blob pomocí Průzkumníka služby Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)
* [Použití Azure CLI 2.0 s Azure Storage](../storage-azure-cli.md)
* [Používání úložiště Blob z jazyka C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Používání úložiště Blob z Javy](../blobs/storage-java-how-to-use-blob-storage.md)
* [Používání úložiště Blob z Node.js](../blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Používání úložiště Blob z Pythonu](../blobs/storage-python-how-to-use-blob-storage.md)

### <a name="azure-storage-blog-posts"></a>Příspěvky blogu Azure Storage:
* [Uvedení AzCopy na Linux Preview](https://azure.microsoft.com/en-in/blog/announcing-azcopy-on-linux-preview/)
* [Představení náhled knihovny přesun dat úložiště Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [AzCopy: Úvod synchronní kopie a vlastní typ obsahu](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [AzCopy: Uvedení obecné dostupnosti AzCopy 3.0 a AzCopy 4.0 verze preview s podporou tabulky a souboru](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [AzCopy: Optimalizovaných pro rozsáhlé kopie scénáře](http://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy: Podpora pro geograficky redundantní úložiště s přístupem pro čtení](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [AzCopy: Přenos dat pomocí režimu s možností restartování a tokenu SAS](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
* [AzCopy: Objekt Blob kopírování mezi účet pomocí](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [AzCopy: Nahrávání nebo stahování souborů pro objekty BLOB Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)
