---
title: Jak nasadit Azure Files | Microsoft Docs
description: Zjistěte, jak nasadit Azure soubory od začátku do konce.
services: storage
documentationcenter: ''
author: wmgries
manager: aungoo
editor: tamram
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2018
ms.author: wgries
ms.openlocfilehash: 38a0e64bf958ef6aebac41136ffcb9c213cc7ec0
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029777"
---
# <a name="how-to-deploy-azure-files"></a>Nasazení služby Soubory Azure
[Soubory Azure](storage-files-introduction.md) nabízí plně spravované sdílené složky v cloudu, které jsou přístupné přes průmyslový standard protokolu SMB. Tento článek vám ukáže, jak to prakticky nasazení Azure souborů v rámci vaší organizace.

Důrazně doporučujeme čtení [plánování nasazení Azure Files](storage-files-planning.md) před podle kroků v tomto článku.

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že jste již dokončili následující kroky:

- Vytvořit účet úložiště Azure s požadovanou odolnost proti chybám a šifrování možnosti, v oblasti, které očekáváte. V tématu [vytvořit účet úložiště](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) pro podrobné pokyny o tom, jak vytvořit účet úložiště.
- Vytvořit sdílenou složku Azure s vaší požadované kvóty ve vašem účtu úložiště. V tématu [vytvoření sdílené složky](storage-how-to-create-file-share.md) pro podrobné pokyny o tom, jak vytvořit sdílenou složku.

## <a name="transfer-data-into-azure-files"></a>Přenos dat do Azure Files
Můžete migrovat existující sdílené složky, například tyto uložené místně do nové sdílené složky Azure souboru. V této části vám ukáže, jak pro přesun dat do sdílenou složku Azure přes několik oblíbených metod z [Příručka pro plánování](storage-files-planning.md#data-transfer-method)

### <a name="azure-file-sync-preview"></a>Synchronizace Azure File (Preview)
Soubor synchronizace služby Azure (Preview) umožňuje centralizovat vaší organizace sdílené složky v souborech Azure bez nutnosti poskytnutí flexibilitu, výkonu a kompatibility pro místní souborový server. Dělá to pomocí transformace serverech s Windows do rychlé mezipaměti Azure sdílené složky. Pro místní přístup k datům můžete použít jakýkoli protokol dostupný ve Windows Serveru (včetně SMB, NFS a FTPS) a můžete mít libovolný počet mezipamětí po celém světě.

Synchronizace služby Azure souboru slouží k migraci dat do sdílenou složku Azure, i když tento synchronizační mechanismus není žádoucí pro dlouhodobé používání. Další informace o tom, jak používat Azure souboru Sync k přenosu dat do sdílenou složku Azure naleznete v [plánování nasazení Azure souboru Sync](storage-sync-files-planning.md) a [jak nasadit Azure souboru Sync](storage-sync-files-deployment-guide.md).

### <a name="azure-importexport"></a>Azure Import/Export
Služba Azure Import/Export umožňuje bezpečně přenáší velké objemy dat do sdílenou složku Azure přesouvání pevných disků o datovém centru Azure. V tématu [používat službu Microsoft Azure Import/Export k přenosu dat do úložiště Azure](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) pro podrobnější přehled služby.

> [!Note]  
> Služba Azure Import/Export export souborů z sdílenou složku Azure v tuto chvíli nepodporuje.

Následující kroky importovat data z místního umístění na Azure sdílené složky.

1. Pořídit požadovaný počet pevné disky, aby e-mailu do Azure. Pevné disky může mít libovolnou velikost disku, ale musí být buď 2,5" nebo 3,5" SSD a HDD podporující standard SATA II nebo SATA III. 

2. Připojení a připojte každého disku, na server/PC přenos dat provádění. Pro optimální výkon doporučujeme spustit úlohy exportu místní místně na serveru, který obsahuje data. V některých případech, například po souborový server, který slouží data na zařízení NAS nemusí být možné. V takovém případě je zcela přijatelné připojit každého disku, na počítač.

3. Zkontrolujte každé jednotky, je online, inicializovat a je přiřazeno písmeno jednotky. Uvést online na jednotku, inicializovat a přiřadit písmeno jednotky, otevřete modulu snap-in MMC programu Správa disků (diskmgmt.msc).

    - Uvést disk online (Pokud již není online), klikněte pravým tlačítkem na disk v dolním podokně konzoly MMC Správa disku a vyberte "Online".
    - Inicializace disku, klikněte pravým tlačítkem na disk v dolním podokně (po je disk online) a vyberte možnost "Inicializovat". Je nutné vybrat "GPT", když se zobrazí dotaz.

        ![Snímek obrazovky nabídky inicializovat Disk v konzole MMC Správa disku](media/storage-files-deployment-guide/transferdata-importexport-1.PNG)

    - Přiřadit písmeno jednotky na disk, klikněte pravým tlačítkem na "nepřidělené" místo online a inicializované disku a klikněte na tlačítko "Nový jednoduchý svazek". To vám umožní přiřadit písmeno jednotky. Všimněte si, že není potřeba Naformátujte svazek jako to bude provedeno později.

        ![Snímek obrazovky průvodci Nový jednoduchý svazek v konzole MMC Správa disku](media/storage-files-deployment-guide/transferdata-importexport-2.png)

4. Vytvořte soubor CSV datovou sadu. Požadované sdílenou složkou Azure data by se měl zkopírovat do souboru CSV datovou sadu je mapování mezi cestu k dat na místě. Například následující soubor CSV datovou sadu mapuje sdílené složky ("F:\shares\scratch") na místní sítě do Azure sdílené složky ("MyAzureFileShare"):
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\shares\scratch\","MyAzureFileShare/",file,rename,"None",None
    ```

    Je možné zadat více sdílených složek s účtem úložiště. V tématu [připravte si soubor CSV datovou sadu](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-the-dataset-csv-file) Další informace.

5. Vytvořte soubor CSV driveset. Soubor CSV driveset vypíše disky, které jsou k dispozici pro export agenta místně. Například následující driveset CSV souboru seznamy `X:`, `Y:`, a `Z:` jednotky, které mají být použity v místní úloha exportu:

    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    X,Format,SilentMode,Encrypt,
    Y,Format,SilentMode,Encrypt,
    Z,Format,SilentMode,Encrypt,
    ```
    
    V tématu [připravte si soubor CSV driveset](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-initialdriveset-or-additionaldriveset-csv-file) Další informace.

6. Použití [WAImportExport nástroj](https://www.microsoft.com/en-us/download/details.aspx?id=55280) ke kopírování dat na jeden nebo více pevných disků.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

    > [!Warning]  
    > Neprovádějte žádné změny dat na jednotky pevného disku nebo souboru deníku po dokončení Příprava disku.

7. [Vytvoření úlohy importu](../common/storage-import-export-data-to-files.md#step-2-create-an-import-job).
    
### <a name="robocopy"></a>Robocopy
Robocopy je dobře známé kopie nástroj, který se dodává s Windows a Windows Server. Robocopy může sloužit k přenosu dat do Azure souborů připojení sdílené složky místně a následným použitím připojené umístění jako cíl v příkazu Robocopy. Použití Robocopy je poměrně jednoduché:

1. [Připojení Azure sdílené složky](storage-how-to-use-files-windows.md). Pro optimální výkon doporučujeme připojení Azure sdílené složky místně na serveru, který obsahuje data. V některých případech, například po souborový server, který slouží data na zařízení NAS nemusí být možné. V takovém případě je zcela přijatelné připojit sdílenou složkou Azure na počítač. V tomto příkladu `net use` se používá v příkazovém řádku k připojení sdílené složky:

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. Použití `robocopy` na příkazovém řádku pro přesun dat do Azure sdílené složky:

    ```
    robocopy <path-to-local-share> <path-to-azure-file-share> /E /Z /MT:32
    ```
    
    Robocopy má velký počet možnosti úprav chování kopie podle potřeby. Další informace naleznete [Robocopy](https://technet.microsoft.com/library/cc733145.aspx) stránky ručně.

### <a name="azcopy"></a>AzCopy
AzCopy je nástroj příkazového řádku pro kopírování dat do a z Azure Files, jakož i úložiště objektů Blob v Azure pomocí jednoduchých příkazů optimální výkon. Pomocí AzCopy je snadné:

1. Stažení [nejnovější verzi AzCopy v systému Windows](http://aka.ms/downloadazcopy) nebo [Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#download-and-install-azcopy).
2. Použití `azcopy` na příkazovém řádku pro přesun dat do Azure sdílené složky. Syntaxe v systému Windows je následující: 

    ```
    azcopy /Source:<path-to-local-share> /Dest:https://<storage-account>.file.core.windows.net/<file-share>/ /DestKey:<storage-account-key> /S
    ```

    Syntaxe příkazu pro systémy Linux, je mírně liší:

    ```
    azcopy --source <path-to-local-share> --destination https://<storage-account>.file.core.windows.net/<file-share>/ --dest-key <storage-account-key> --recursive
    ```

    AzCopy má velký počet možnosti úprav chování kopie podle potřeby. Další informace, [AzCopy v systému Windows](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) a [AzCopy v systému Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="automatically-mount-on-needed-pcsservers"></a>Automaticky připojit na potřebné počítače nebo servery
Pokud chcete nahradit místní sdílené složky, je užitečné předem připojit sdílené složky na počítačích, které se budou používat v. To můžete provést automaticky na seznam počítačů.

> [!Note]  
> Připojení sdílenou složku Azure vyžaduje použití klíče účtu úložiště jako heslo, tedy pouze doporučujeme připojení v důvěryhodné prostředích. 

### <a name="windows"></a>Windows
Prostředí PowerShell je možné spustit příkaz připojení na víc počítačích. V následujícím příkladu `$computers` je vyplněný ručně, ale můžete vygenerovat seznam počítačů, automaticky připojit. Například můžete naplnit tuto proměnnou s výsledky ze služby Active Directory.

```PowerShell
$computer = "MyComputer1", "MyComputer2", "MyComputer3", "MyComputer4"
$computer | ForEach-Object { Invoke-Command -ComputerName $_ -ScriptBlock { net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name> /PERSISTENT:YES } }
```

### <a name="linux"></a>Linux
Jednoduché bash skript v kombinaci s SSH přispět stejný výsledek v následujícím příkladu. `$computer` Proměnné je podobně zleva uživatelem zadáno jinak:

```
computer = ("MyComputer1" "MyComputer2" "MyComputer3" "MyComputer4")
for item in "${computer[@]}"
do
    ssh $item "sudo bash -c 'echo \"//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino\" >> /etc/fstab'", "sudo mount -a"
done
```

## <a name="next-steps"></a>Další postup
- [Plánování nasazení služby Azure souboru Sync](storage-sync-files-planning.md)
- [Řešení potíží s soubory v systému Windows Azure](storage-troubleshoot-windows-file-connection-problems.md)
- [Řešení potíží s Azure soubory v systému Linux](storage-troubleshoot-linux-file-connection-problems.md)