---
title: Jak nasadit soubory Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak nasadit soubory Azure od začátku do konce.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 38339defc9d06f3e809bc24f957ebbb30abb46d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598778"
---
# <a name="how-to-deploy-azure-files"></a>Nasazení služby Soubory Azure
[Azure Files](storage-files-introduction.md) nabízí plně spravované sdílené složky v cloudu, které jsou přístupné prostřednictvím standardního protokolu SMB. V tomto článku se zobrazí, jak prakticky nasadit soubory Azure v rámci vaší organizace.

Důrazně doporučujeme přečíst [plánování pro nasazení souborů Azure](storage-files-planning.md) před provedením kroků v tomto článku.

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že jste již dokončili následující kroky:

- Vytvoření účtu úložiště Azure s požadovanou odolností a možnostmi šifrování v oblasti, kterou si přejete. Podrobné pokyny k vytvoření účtu úložiště najdete v tématu [Vytvoření účtu úložiště.](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
- Ve vašem účtu úložiště jste vytvořili sdílenou složku Azure s požadovanou kvótou. Podrobné pokyny k vytvoření sdílené složky najdete v tématu [Vytvoření sdílené složky.](storage-how-to-create-file-share.md)

## <a name="transfer-data-into-azure-files"></a>Přenos dat do souborů Azure
Možná budete chtít migrovat existující sdílené složky, jako jsou ty uložené místně, do nové sdílené složky Azure. Tato část vám ukáže, jak přesunout data do sdílené složky Azure pomocí několika oblíbených metod podrobně popsaných v [průvodci plánováním](storage-files-planning.md#migration)

### <a name="azure-file-sync"></a>Synchronizace souborů Azure
Synchronizace souborů Azure umožňuje centralizovat sdílené složky organizace ve službě Soubory Azure bez ztráty flexibility, výkonu a kompatibility místního souborového serveru. Je to tím, že transformuje vaše servery Windows do rychlé mezipaměti sdílené složky Azure. Pro místní přístup k datům můžete použít jakýkoli protokol dostupný ve Windows Serveru (včetně SMB, NFS a FTPS) a můžete mít libovolný počet mezipamětí po celém světě.

Azure File Sync se dá použít k migraci dat do sdílené složky Azure, i když mechanismus synchronizace není požadovaný pro dlouhodobé použití. Další informace o tom, jak pomocí Azure File Sync k přenosu dat do sdílené složky Azure, najdete v [článku Plánování pro nasazení Synchronizace souborů Azure](storage-sync-files-planning.md) a Jak [nasadit Azure File Sync](storage-sync-files-deployment-guide.md).

### <a name="azure-importexport"></a>Azure Import/Export
Služba Import a export Azure umožňuje bezpečně přenášet velké objemy dat do sdílené složky Azure odesláním pevných disků do datového centra Azure. Podrobnější přehled o službě najdete [v článku Použití služby Import a export Microsoft Azure k přenosu dat do úložiště Azure.](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

> [!Note]  
> Služba Import a export Azure v tuto chvíli nepodporuje export souborů ze sdílené složky Azure.

Následující kroky budou importovat data z místního umístění do sdílené složky Azure.

1. Získat požadovaný počet pevných disků pro poštu do Azure. Pevné disky mohou mít libovolnou velikost disku, ale musí být 2,5" nebo 3,5" SSD nebo HDD podporující standard SATA II nebo SATA III. 

2. Připojte a připojte každý disk na serveru / PC, který provádí přenos dat. Pro optimální výkon doporučujeme spustit úlohu místního exportu místně na serveru, který obsahuje data. V některých případech, například když je souborový server, který slouží data, zařízení NAS, nemusí to být možné. V takovém případě je naprosto přijatelné připojit každý disk na PC.

3. Ujistěte se, že každá jednotka je online, inicializována a je mu přiřazeno písmeno jednotky. Chcete-li jednotku uvést do režimu online, inicializovat ji a přiřadit písmeno jednotky, otevřete modul snap-in MMC pro správu disků (diskmgmt.msc).

    - Chcete-li disk převést do režimu online (pokud ještě není online), klikněte pravým tlačítkem myši na disk v dolním podokně konzoly MMC pro správu disků a vyberte možnost Online.
    - Chcete-li inicializovat disk, klikněte pravým tlačítkem myši na disk v dolním podokně (po online nastavení disku) a vyberte možnost Inicializovat. Nezapomeňte vybrat "GPT" na požádání.

        ![Snímek obrazovky s nabídkou Inicializovat disk v konzoli MMC pro správu disků](media/storage-files-deployment-guide/transferdata-importexport-1.PNG)

    - Chcete-li na disk přiřadit písmeno jednotky, klikněte pravým tlačítkem myši na místo "nepřiděleno" online a inicializovaného disku a klikněte na tlačítko Nový jednoduchý svazek. To vám umožní přiřadit písmeno jednotky. Všimněte si, že není nutné formátovat svazek, protože to bude provedeno později.

        ![Snímek obrazovky průvodce novým jednoduchým svazkem v konzoli MMC pro správu disků](media/storage-files-deployment-guide/transferdata-importexport-2.png)

4. Vytvořte soubor CSV datové sady. Soubor CSV datové sady je mapování mezi cestou k místním datům a požadovanou sdílenou sisy Azure, do které by se měla data zkopírovat. Například následující soubor CSV datové sady mapuje místní sdílenou složku ("F:\shares\scratch") na sdílenou složku Azure ("MyAzureFileShare"):
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\shares\scratch\","MyAzureFileShare/",file,rename,"None",None
    ```

    Může být zadáno více sdílených složek s účtem úložiště. Další informace naleznete [v tématu Příprava souboru CSV datové sady.](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

5. Vytvořte soubor CSV sady jednotek. Soubor CSV sady jednotek obsahuje seznam disků, které jsou k dispozici místnímu agentovi exportu. Například následující seznamy `X:` `Y:`souborů CSV sady `Z:` jednotek a jednotky, které mají být použity v místní úloze exportu:

    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    X,Format,SilentMode,Encrypt,
    Y,Format,SilentMode,Encrypt,
    Z,Format,SilentMode,Encrypt,
    ```
    
    Další informace naleznete [v tématu Příprava souboru CSV sady jednotek.](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

6. Pomocí [nástroje WAImportExport](https://www.microsoft.com/download/details.aspx?id=55280) zkopírujte data na jeden nebo více pevných disků.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

    > [!Warning]  
    > Po dokončení přípravy disku neupravujte data na pevných discích ani v souboru deníku.

7. [Vytvořte úlohu importu](../common/storage-import-export-data-to-files.md#step-2-create-an-import-job).
    
### <a name="robocopy"></a>Robocopy
Robocopy je dobře známý nástroj pro kopírování, který je dodáván s Windows a Windows Server. Robocopy lze použít k přenosu dat do souborů Azure připojením sdílené složky místně a pak pomocí připojené umístění jako cíl v příkazu Robocopy. Použití Robocopy je poměrně jednoduché:

1. [Připojte sdílenou složku Azure](storage-how-to-use-files-windows.md). Pro optimální výkon doporučujeme připojení sdílené složky Azure místně na serveru, který obsahuje data. V některých případech, například když je souborový server, který slouží data, zařízení NAS, nemusí to být možné. V takovém případě je naprosto přijatelné připojit sdílenou složku Azure na počítači. V tomto `net use` příkladu se používá na příkazovém řádku pro připojení sdílené složky:

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. Přesunutí `robocopy` dat do sdílené složky Azure se používá na příkazovém řádku:

    ```
    robocopy <path-to-local-share> <path-to-azure-file-share> /E /Z /MT:32
    ```
    
    Robocopy má značný počet možností, jak upravit chování kopírování podle potřeby. Další informace naleznete na manuálové stránce [Robocopy.](https://technet.microsoft.com/library/cc733145.aspx)

### <a name="azcopy"></a>AzCopy
AzCopy je nástroj příkazového řádku určený pro kopírování dat do a z Azure Files, stejně jako úložiště objektů Blob Azure pomocí jednoduchých příkazů s optimálním výkonem. Použití AzCopy je snadné:

1. Stáhněte si [nejnovější verzi AzCopy na Windows](https://aka.ms/downloadazcopy) nebo [Linux](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux#download-and-install-azcopy).
2. Na `azcopy` příkazovém řádku můžete přesunout data do sdílené složky Azure. Syntaxe systému Windows je následující: 

    ```
    azcopy /Source:<path-to-local-share> /Dest:https://<storage-account>.file.core.windows.net/<file-share>/ /DestKey:<storage-account-key> /S
    ```

    Na Linuxu je syntaxe příkazu trochu jiná:

    ```
    azcopy --source <path-to-local-share> --destination https://<storage-account>.file.core.windows.net/<file-share>/ --dest-key <storage-account-key> --recursive
    ```

    AzCopy má značný počet možností, jak upravit chování kopírování podle potřeby. Další informace naleznete [v azCopy ve Windows](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) a [AzCopy v Linuxu](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="automatically-mount-on-needed-pcsservers"></a>Automatické připojení k potřebným počítačům/serverům
Chcete-li nahradit místní sdílenou složku, je užitečné předem připojit sdílené složky na počítačích, na kterých bude použita. To lze provést automaticky na seznamu strojů.

> [!Note]  
> Připojení sdílené složky Azure vyžaduje použití klíče účtu úložiště jako heslo, proto doporučujeme pouze připojení v důvěryhodných prostředích. 

### <a name="windows"></a>Windows
PowerShell lze použít spustit příkaz připojit na více počítačích. V následujícím příkladu `$computers` je ručně vyplněna, ale můžete vygenerovat seznam počítačů připojit automaticky. Tuto proměnnou můžete například naplnit výsledky ze služby Active Directory.

```powershell
$computer = "MyComputer1", "MyComputer2", "MyComputer3", "MyComputer4"
$computer | ForEach-Object { Invoke-Command -ComputerName $_ -ScriptBlock { net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name> /PERSISTENT:YES } }
```

### <a name="linux"></a>Linux
Jednoduchý bash skript v kombinaci s SSH může přinést stejný výsledek v následujícím příkladu. Proměnná `$computer` je podobně ponechána, aby byla naplněna uživatelem:

```
computer = ("MyComputer1" "MyComputer2" "MyComputer3" "MyComputer4")
for item in "${computer[@]}"
do
    ssh $item "sudo bash -c 'echo \"//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino\" >> /etc/fstab'", "sudo mount -a"
done
```

## <a name="next-steps"></a>Další kroky
- [Plánování nasazení synchronizace souborů Azure](storage-sync-files-planning.md)
- [Řešení problémů se Soubory Azure ve Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Řešení problémů se Soubory Azure v Linuxu](storage-troubleshoot-linux-file-connection-problems.md)
