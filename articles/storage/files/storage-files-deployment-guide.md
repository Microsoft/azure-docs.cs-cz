---
title: Postup nasazení služby soubory Azure | Dokumentace Microsoftu
description: Informace o nasazení služby soubory Azure od začátku do konce.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 05/22/2018
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: d17389af1129c63d2c0eadfac04c034669200620
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462728"
---
# <a name="how-to-deploy-azure-files"></a>Nasazení služby Soubory Azure
[Služba soubory Azure](storage-files-introduction.md) nabízí plně spravované sdílené složky v cloudu, které jsou přístupné přes standardní protokol SMB. Tento článek vám ukáže postup prakticky nasazení služby soubory Azure v rámci vaší organizace.

Důrazně doporučujeme čtení [plánování nasazení služby soubory Azure](storage-files-planning.md) před podle kroků v tomto článku.

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že jste již dokončili následující kroky:

- Vytvoření účtu služby Azure Storage s požadované odolnosti proti chybám a šifrování možnosti, v oblasti, které očekáváte. Zobrazit [vytvořit účet úložiště](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) pro podrobné pokyny o tom, jak vytvořit účet úložiště.
- Vytvořit sdílené složky Azure se vaše požadované kvóty ve vašem účtu úložiště. Zobrazit [vytvoření sdílené složky](storage-how-to-create-file-share.md) pro podrobné pokyny o tom, jak vytvořit sdílenou složku.

## <a name="transfer-data-into-azure-files"></a>Přenos dat do soubory Azure
Můžete chtít migrovat existující sdílené složky, například tyto uložené místně, do nové sdílené složky Azure. V této části ukazují, jak přesunout data do sdílené složky Azure přes několik oblíbených uvedených z metod [Příručka pro plánování](storage-files-planning.md#data-transfer-method)

### <a name="azure-file-sync"></a>Synchronizace souborů Azure
Synchronizace souborů Azure umožňuje centralizovat sdílené složky organizace ve službě Soubory Azure bez ztráty flexibility, výkonu a kompatibility místního souborového serveru. Dělá to pomocí transformace serverů Windows na rychlou mezipaměť sdílené složky Azure. Pro místní přístup k datům můžete použít jakýkoli protokol dostupný ve Windows Serveru (včetně SMB, NFS a FTPS) a můžete mít libovolný počet mezipamětí po celém světě.

Azure File Sync slouží k migraci dat do sdílené složky Azure, i když není žádoucí tento synchronizační mechanismus pro dlouhodobé používání. Další informace o tom, jak používat Azure File Sync k přenosu dat do sdílené složky Azure můžete najít v [plánování nasazení služby Azure File Sync](storage-sync-files-planning.md) a [postup nasazení služby Azure File Sync](storage-sync-files-deployment-guide.md).

### <a name="azure-importexport"></a>Azure Import/Export
Služba Azure Import/Export umožňuje bezpečně přenášet velké objemy dat do sdílené složky Azure přenosem pevných disků do datacentra Azure. Zobrazit [přenos dat do služby Azure storage pomocí služby Microsoft Azure Import/Export](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) pro podrobnější přehled služby.

> [!Note]  
> Služba Azure Import/Export exportovat soubory ze sdílené složky Azure v tuto chvíli nepodporuje.

Následující kroky importovat data z místního umístění na sdílené složky Azure.

1. Pořídit požadovaný počet pevných disků do e-mailu do Azure. Pevné disky může mít libovolnou velikost disku, ale musí být buď 2,5" nebo 3,5" SSD nebo pevný disk podporující standard SATA II a SATA III. 

2. Připojení a připojení na serveru/počítači způsobem přenos dat jednotlivých disků. Pro zajištění optimálního výkonu doporučujeme spustit úlohy exportu v místním místně na serveru, který obsahuje data. V některých případech, například pokud je souborový server, který poskytuje data zařízení NAS nemusí být možné. V takovém případě je naprosto přijatelné pro připojení jednotlivých disků v počítači.

3. Zkontrolujte každé jednotce je online, inicializována a je přiřazeno písmeno jednotky. Pokud chcete přenést disku online, inicializace a přiřadit písmeno jednotky, otevřete modul snap-in konzoly MMC Správa disků (diskmgmt.msc).

    - Převést disk online (Pokud již není online), klikněte pravým tlačítkem na disk v dolním podokně Správa disků konzoly MMC a vyberte "Online".
    - Chcete-li inicializovat disk, klikněte pravým tlačítkem na disk v dolním podokně (po je disk online) a vyberte "Inicializace". Je potřeba vybrat možnost "GPT", když se zobrazí výzva.

        ![Snímek obrazovky nabídky inicializovat Disk v Správa disků konzoly MMC](media/storage-files-deployment-guide/transferdata-importexport-1.PNG)

    - Pokud chcete přiřadit písmeno jednotky na disk, klikněte pravým tlačítkem na "nepřidělené" místo disku online a inicializované a klikněte na tlačítko "Nový jednoduchý svazek". To vám umožní přiřadit písmeno jednotky. Všimněte si, že není nutné naformátování svazku, protože to se provede později.

        ![Snímek obrazovky s průvodci Nový jednoduchý svazek v Správa disků konzoly MMC](media/storage-files-deployment-guide/transferdata-importexport-2.png)

4. Vytvořte soubor CSV datové sady. Požadované sdílené složky Azure data mají být zkopírovány do souboru CSV datové sady je mapování mezi cesta k data dostupná místně. Například následující soubor CSV datovou sadu mapuje místní sdílenou složku ("F:\shares\scratch") sdílené složky Azure ("MyAzureFileShare"):
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\shares\scratch\","MyAzureFileShare/",file,rename,"None",None
    ```

    Je možné zadat více sdílených složek s účtem úložiště. Zobrazit [Příprava sdíleného svazku clusteru soubor datové sady](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-the-dataset-csv-file) Další informace.

5. Vytvořte soubor driveset sdíleného svazku clusteru. Soubor CSV driveset seznam k dispozici pro export agenta s místními disky. Například následující driveset CSV soubor seznamy `X:`, `Y:`, a `Z:` jednotky, který se má použít v místní úloha exportu:

    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    X,Format,SilentMode,Encrypt,
    Y,Format,SilentMode,Encrypt,
    Z,Format,SilentMode,Encrypt,
    ```
    
    Zobrazit [Příprava souboru CSV driveset](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-initialdriveset-or-additionaldriveset-csv-file) Další informace.

6. Použití [WAImportExport nástroj](https://www.microsoft.com/en-us/download/details.aspx?id=55280) ke kopírování dat na jeden nebo více pevných disků.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

    > [!Warning]  
    > Neprovádějte žádné změny dat na jednotky pevných disků nebo soubor deníku po dokončení Příprava disku.

7. [Vytvořit úlohu importu](../common/storage-import-export-data-to-files.md#step-2-create-an-import-job).
    
### <a name="robocopy"></a>Robocopy
Příkaz Robocopy je dobře známé kopírování nástroj, který se dodává s Windows a Windows Server. Příkaz Robocopy můžou sloužit k připojení sdílené místně a následným použitím umístění připojené jako cíl v příkazu Robocopy přenášet data do soubory Azure. Pomocí příkazu Robocopy je poměrně jednoduchý:

1. [Připojení sdílené složky Azure](storage-how-to-use-files-windows.md). Pro zajištění optimálního výkonu doporučujeme připojení sdílené složky Azure místně na serveru, který obsahuje data. V některých případech, například pokud je souborový server, který poskytuje data zařízení NAS nemusí být možné. V takovém případě je zcela přijatelné pro připojení sdílené složky Azure na počítači. V tomto příkladu `net use` slouží pro připojení sdílené složky na příkazovém řádku:

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. Použití `robocopy` příkazového řádku pro přesun dat do sdílené složky Azure:

    ```
    robocopy <path-to-local-share> <path-to-azure-file-share> /E /Z /MT:32
    ```
    
    Příkaz Robocopy má velký počet možnosti úprav chování kopírování podle potřeby. Další informace najdete [Robocopy](https://technet.microsoft.com/library/cc733145.aspx) stránky ručně.

### <a name="azcopy"></a>AzCopy
AzCopy je nástroj příkazového řádku určený pro kopírování dat do a z Azure Files, jakož i úložiště objektů Blob v Azure pomocí jednoduchých příkazů s optimálním výkonem. Pomocí AzCopy velmi snadno:

1. Stáhněte si [nejnovější verzi AzCopy ve Windows](https://aka.ms/downloadazcopy) nebo [Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#download-and-install-azcopy).
2. Použití `azcopy` příkazového řádku pro přesun dat do sdílené složky Azure. Syntaxe na Windows je následující: 

    ```
    azcopy /Source:<path-to-local-share> /Dest:https://<storage-account>.file.core.windows.net/<file-share>/ /DestKey:<storage-account-key> /S
    ```

    V systému Linux je trochu jiné syntaxe příkazu:

    ```
    azcopy --source <path-to-local-share> --destination https://<storage-account>.file.core.windows.net/<file-share>/ --dest-key <storage-account-key> --recursive
    ```

    AzCopy je velký počet možnosti úprav chování kopírování podle potřeby. Další informace získáte [AzCopy ve Windows](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) a [AzCopy v Linuxu](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="automatically-mount-on-needed-pcsservers"></a>Automatické připojení na potřeby počítače/servery
Nahradit sdílenou složku v místním, je vhodné předem připojte sdílené složky na počítače, které se budou používat v. To můžete udělat automaticky na seznam počítačů.

> [!Note]  
> Připojení sdílené složky Azure vyžaduje použití klíče účtu úložiště jako heslo, tedy pouze doporučujeme připojení v prostředí důvěryhodné. 

### <a name="windows"></a>Windows
PowerShell je možné spustit příkaz připojení ve víc počítačích. V následujícím příkladu `$computers` se vyplní ručně, ale dají vygenerovat seznam počítačů, které se automaticky připojit. Například lze vyplnit tuto proměnnou s výsledky ze služby Active Directory.

```PowerShell
$computer = "MyComputer1", "MyComputer2", "MyComputer3", "MyComputer4"
$computer | ForEach-Object { Invoke-Command -ComputerName $_ -ScriptBlock { net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name> /PERSISTENT:YES } }
```

### <a name="linux"></a>Linux
Skript jednoduché prostředí bash v kombinaci s SSH může přinést stejný výsledek v následujícím příkladu. `$computer` Proměnná zůstane podobně vyplní uživatelem:

```
computer = ("MyComputer1" "MyComputer2" "MyComputer3" "MyComputer4")
for item in "${computer[@]}"
do
    ssh $item "sudo bash -c 'echo \"//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino\" >> /etc/fstab'", "sudo mount -a"
done
```

## <a name="next-steps"></a>Další postup
- [Plánování nasazení služby Azure File Sync](storage-sync-files-planning.md)
- [Řešení problémů se soubory Azure na Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Řešení problémů se soubory Azure v Linuxu](storage-troubleshoot-linux-file-connection-problems.md)
