---
title: Jak nasadit soubory Azure | Microsoft Docs
description: Přečtěte si, jak nasadit soubory Azure ze začátku do konce. Přeneste data do souborů Azure. Automaticky připojit k potřebným počítačům nebo serverům.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/22/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 53111ccd634c516d0db10c0e2dd41768aba43f41
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629236"
---
# <a name="how-to-deploy-azure-files"></a>Nasazení služby Soubory Azure
[Soubory Azure](storage-files-introduction.md) nabízí plně spravované sdílené složky v cloudu, které jsou přístupné přes standardní průmyslový protokol SMB. V tomto článku se dozvíte, jak prakticky nasadit soubory Azure v rámci vaší organizace.

Důrazně doporučujeme, abyste načetli [plánování pro nasazení souborů Azure](storage-files-planning.md) před provedením kroků v tomto článku.

## <a name="prerequisites"></a>Předpoklady
V tomto článku se předpokládá, že jste už dokončili následující kroky:

- Vytvořili jste účet Azure Storage s požadovanými možnostmi odolnosti a šifrování v oblasti, kterou si přejete. Podrobné pokyny k vytvoření účtu úložiště najdete v tématu [Vytvoření účtu úložiště](../common/storage-account-create.md?toc=%252fazure%252fstorage%252ffiles%252ftoc.json) .
- Vytvořili jste sdílenou složku Azure s požadovanou kvótou v účtu úložiště. Podrobné pokyny, jak vytvořit sdílenou složku, najdete v tématu věnovaném [Vytvoření sdílené složky](storage-how-to-create-file-share.md) .

## <a name="transfer-data-into-azure-files"></a>Přenos dat do souborů Azure
Možná budete chtít migrovat existující sdílené složky, jako jsou místně uložené, do nové sdílené složky Azure. V této části se dozvíte, jak přesunout data do sdílené složky Azure pomocí několika oblíbených metod popsaných v [Průvodci plánováním](storage-files-planning.md#migration) .

### <a name="azure-file-sync"></a>Synchronizace souborů Azure
Synchronizace souborů Azure umožňuje centralizovat sdílené složky organizace ve službě Soubory Azure bez ztráty flexibility, výkonu a kompatibility místního souborového serveru. Dělá to tak, že transformuje servery Windows na rychlou mezipaměť sdílené složky Azure. Pro místní přístup k datům můžete použít jakýkoli protokol dostupný ve Windows Serveru (včetně SMB, NFS a FTPS) a můžete mít libovolný počet mezipamětí po celém světě.

Azure File Sync můžete použít k migraci dat do sdílené složky Azure, a to i v případě, že mechanismus synchronizace není pro dlouhodobé použití potřeba. Další informace o použití Azure File Sync k přenosu dat do sdílené složky Azure najdete v tématu [Plánování nasazení Azure File Sync](storage-sync-files-planning.md) a nasazení [Azure File Sync](storage-sync-files-deployment-guide.md).

### <a name="azure-importexport"></a>Azure Import/Export
Služba importu/exportu v Azure umožňuje bezpečně přenášet velké objemy dat do sdílené složky Azure tím, že je přenáší jednotky pevného disku do datacentra Azure. Podrobnější přehled služby najdete v tématu [použití služby Microsoft Azure import/export k přenosu dat do služby Azure Storage](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) .

> [!Note]  
> Služba import/export Azure v tuto chvíli nepodporuje export souborů ze sdílené složky Azure.

Následující kroky budou importovat data z místního umístění do sdílené složky Azure.

1. Pořídit požadovaný počet pevných disků k odeslání do Azure. Na pevných discích může být libovolná velikost disku, ale musí to být 2,5 "nebo 3,5" SSD nebo HDD podporující standard SATA II nebo SATA III. 

2. Připojte a připojte každý disk na serveru nebo počítači k přenosu dat. Pro zajištění optimálního výkonu doporučujeme spustit místní úlohu exportu místně na serveru, který obsahuje data. V některých případech, například pokud souborový server, který obsluhuje data, je zařízení NAS, nemusí to být možné. V takovém případě je naprosto přijatelné připojit každý disk na počítači.

3. Ujistěte se, že je každá jednotka online, inicializovaná a má přiřazené písmeno jednotky. Chcete-li převést jednotku online, inicializovat a přiřadit písmeno jednotky, otevřete modul snap-in konzoly MMC Správa disků (diskmgmt. msc).

    - Pokud chcete převést disk do online režimu (Pokud ještě není online), klikněte pravým tlačítkem na disk v dolním podokně konzoly MMC Správa disků a vyberte online.
    - Pokud chcete inicializovat disk, klikněte pravým tlačítkem na disk v dolním podokně (po online disku) a vyberte inicializovat. Po zobrazení dotazu nezapomeňte vybrat GPT.

        ![Snímek obrazovky nabídky inicializovat disk v konzole MMC Správa disků](media/storage-files-deployment-guide/transferdata-importexport-1.PNG)

    - Pokud chcete disk přiřadit písmeno jednotky, klikněte pravým tlačítkem myši na nepřidělené místo v online a inicializovaném disku a klikněte na Nový jednoduchý svazek. To vám umožní přiřadit písmeno jednotky. Všimněte si, že tento svazek nemusíte formátovat, protože se to provede později.

        ![Snímek obrazovky s Průvodcem vytvořením jednoduchého svazku v konzole MMC Správa disků](media/storage-files-deployment-guide/transferdata-importexport-2.png)

4. Vytvořte soubor CSV s datovou sadou. Soubor CSV s datovou sadou je mapování mezi cestou k místnímu souboru a požadovanou sdílenou složkou Azure, do které se mají tato data zkopírovat. Například následující soubor CSV s datovou sadou mapuje místní sdílenou složku (F:\shares\scratch) do sdílené složky Azure ("MyAzureFileShare"):
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\shares\scratch\","MyAzureFileShare/",file,rename,"None",None
    ```

    Může být zadáno více sdílených složek s účtem úložiště. Další informace najdete v tématu [Příprava souboru CSV s datovou sadou](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import?toc=%252fazure%252fstorage%252ffiles%252ftoc.json) .

5. Vytvořte soubor CSV driveset. V souboru CSV driveset jsou uvedené disky, které jsou k dispozici pro místního exportního agenta. Například následující driveset soubory CSV obsahují seznam `X:` , `Y:` a `Z:` jednotky, které mají být použity v místní úloze exportu:

    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    X,Format,SilentMode,Encrypt,
    Y,Format,SilentMode,Encrypt,
    Z,Format,SilentMode,Encrypt,
    ```
    
    Další informace najdete v tématu [Příprava souboru CSV driveset](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import?toc=%252fazure%252fstorage%252ffiles%252ftoc.json) .

6. Pomocí [nástroje WAImportExport](https://www.microsoft.com/download/details.aspx?id=55280) zkopírujte data na jeden nebo více pevných disků.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

    > [!Warning]  
    > Po dokončení přípravy disku neměňte data na jednotkách pevného disku ani v souboru deníku.

7. [Vytvořte úlohu importu](../common/storage-import-export-data-to-files.md#step-2-create-an-import-job).
    
### <a name="robocopy"></a>Robocopy
Robocopy je známý nástroj pro kopírování, který je součástí Windows a Windows Serveru. Pomocí nástroje Robocopy můžete přenášet data do souborů Azure, a to tak, že sdílenou složku připojíte místně a potom v příkazu Robocopy použijete připojené umístění jako cíl. Použití nástroje Robocopy je poměrně jednoduché:

1. [Připojte sdílenou složku Azure](storage-how-to-use-files-windows.md). Pro zajištění optimálního výkonu doporučujeme místní připojení sdílené složky Azure na serveru, který obsahuje data. V některých případech, například pokud souborový server, který obsluhuje data, je zařízení NAS, nemusí to být možné. V takovém případě je naprosto přijatelné připojit sdílenou složku Azure na počítači. V tomto příkladu `net use` se k připojení sdílené složky používá na příkazovém řádku:

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. K `robocopy` přesunu dat do sdílené složky Azure použijte příkaz na příkazovém řádku:

    ```
    robocopy <path-to-local-share> <path-to-azure-file-share> /E /Z /MT:32
    ```
    
    Robocopy má významný počet možností, jak upravit chování kopírování podle potřeby. Další informace najdete na stránce nástroje [Robocopy](/windows-server/administration/windows-commands/robocopy) ručně.

### <a name="azcopy"></a>AzCopy
AzCopy je nástroj příkazového řádku určený ke kopírování dat do a ze souborů Azure a také jako úložiště objektů BLOB v Azure pomocí jednoduchých příkazů s optimálním výkonem. Použití AzCopy je snadné:

1. Stáhněte si [nejnovější verzi AzCopy v systému Windows](https://aka.ms/downloadazcopy) nebo [Linux](../common/storage-use-azcopy-v10.md?toc=/azure/storage/files/toc.json#download-azcopy).
2. K `azcopy` přesunu dat do sdílené složky Azure použijte příkaz na příkazovém řádku. Syntaxe ve Windows je následující: 

    ```
    azcopy /Source:<path-to-local-share> /Dest:https://<storage-account>.file.core.windows.net/<file-share>/ /DestKey:<storage-account-key> /S
    ```

    V systému Linux je syntaxe příkazu trochu odlišná:

    ```
    azcopy --source <path-to-local-share> --destination https://<storage-account>.file.core.windows.net/<file-share>/ --dest-key <storage-account-key> --recursive
    ```

    AzCopy má významný počet možností pro úpravu chování kopírování podle potřeby. Další informace najdete v tématu [Začínáme s AzCopy](../common/storage-use-azcopy-v10.md?toc=%252fazure%252fstorage%252ffiles%252ftoc.json).

## <a name="automatically-mount-on-needed-pcsservers"></a>Automaticky připojit k potřebným počítačům nebo serverům
Pokud chcete nahradit místní sdílenou složku, je vhodné tyto sdílené složky předem připojit na počítačích, na kterých se bude používat. To lze provést automaticky v seznamu počítačů.

> [!Note]  
> Připojení sdílené složky Azure vyžaduje, abyste jako heslo použili klíč účtu úložiště, a proto doporučujeme připojení jenom v důvěryhodných prostředích. 

### <a name="windows"></a>Windows
PowerShell se dá použít ke spuštění příkazu Mount na více počítačích. V následujícím příkladu `$computers` je ručně vyplněn, ale můžete vygenerovat seznam počítačů pro automatické připojení. Tuto proměnnou můžete například naplnit pomocí výsledků ze služby Active Directory.

```powershell
$computer = "MyComputer1", "MyComputer2", "MyComputer3", "MyComputer4"
$computer | ForEach-Object { Invoke-Command -ComputerName $_ -ScriptBlock { net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name> /PERSISTENT:YES } }
```

### <a name="linux"></a>Linux
Jednoduchý skript bash kombinovaný s SSH může vracet stejný výsledek jako v následujícím příkladu. `$computer`Proměnná je obdobně naplněna uživatelem:

```
computer = ("MyComputer1" "MyComputer2" "MyComputer3" "MyComputer4")
for item in "${computer[@]}"
do
    ssh $item "sudo bash -c 'echo \"//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino\" >> /etc/fstab'", "sudo mount -a"
done
```

## <a name="next-steps"></a>Další kroky
- [Plánování nasazení Azure File Sync](storage-sync-files-planning.md)
- [Řešení problémů se Soubory Azure ve Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Řešení problémů se Soubory Azure v Linuxu](storage-troubleshoot-linux-file-connection-problems.md)