---
title: Rychlý start pro správu sdílených složek Azure pomocí Azure PowerShellu
description: V tomto rychlém startu zjistíte, jak spravovat sdílené složky Azure pomocí Azure PowerShellu.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8b4bd9ece5f010f1294356ad4673543834e5076a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94626907"
---
# <a name="quickstart-create-and-manage-an-azure-file-share-with-azure-powershell"></a>Rychlý start: Vytvoření a správa sdílené složky Azure pomocí Azure PowerShellu 
Tato příručka vás provede základy práce se [sdílenými složkami Azure](storage-files-introduction.md) pomocí PowerShellu. Sdílené složky Azure jsou stejné jako ostatní sdílené složky, ale jsou uložené v cloudu a využívají platformu Azure. Sdílené složky Azure podporují standardní průmyslový protokol SMB (Server Message Block), protokol NFS (Network File System) (verze Preview) a umožňuje sdílení souborů napříč několika počítači, aplikacemi a instancemi. 

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud chcete nainstalovat a používat PowerShell místně, je nutné, aby tato příručka Azure PowerShell modul AZ verze 0,7 nebo novější. Verzi modulu Azure PowerShell, kterou používáte, zjistíte spuštěním rutiny `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzAccount` a přihlásit se ke svému účtu Azure.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Pokud ještě nemáte skupinu prostředků Azure, můžete vytvořit novou pomocí rutiny [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . 

Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v oblasti západní USA 2:

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$region = "westus2"

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $region | Out-Null
```

## <a name="create-a-storage-account"></a>Vytvoření účtu úložiště
Účet úložiště je sdílený fond úložiště, který můžete použít k nasazení sdílených složek Azure. Účet úložiště může obsahovat neomezený počet sdílených složek a sdílená složka můžete obsahovat neomezený počet souborů až do omezení kapacity účtu úložiště. Tento příklad vytvoří obecné účely verze 2 (účet úložiště GPv2), který může Storage úrovně Standard nebo jiné prostředky úložiště, jako jsou objekty blob nebo fronty, na rotačním médiu pevného disku (HDD). Soubory Azure také podporují jednotky SSD (Solid-State disk Drive) (SSD). Služba Azure File Shares úrovně Premium se dá vytvořit v účtech úložiště úložiště.

Tento příklad vytvoří účet úložiště pomocí rutiny [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) . Účet úložiště bude mít název *mystorageaccount\<random number>* a odkaz na tento účet úložiště se uloží do proměnné **$storageAcct**. Názvy účtů úložiště musí být jedinečné, proto k názvu pomocí rutiny `Get-Random` připojte číslo, aby byl jedinečný. 

```azurepowershell-interactive 
$storageAccountName = "mystorageacct$(Get-Random)"

$storageAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $region `
    -Kind StorageV2 `
    -SkuName Standard_ZRS `
    -EnableLargeFileShare
```

> [!Note]  
> Sdílené složky větší než 5 TiB (až do maximálního počtu 100 TiB na sdílenou složku) jsou k dispozici pouze v místně redundantním úložišti (LRS) a zóně redundantního úložiště (ZRS). Pokud chcete vytvořit geograficky redundantní (GRS) nebo účet úložiště GZRS (GEO-Zone-redundantní), odeberte `-EnableLargeFileShare` parametr.

## <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure
Teď můžete vytvořit svou první sdílenou složku Azure. Sdílenou složku můžete vytvořit pomocí rutiny [New-AzRmStorageShare](/powershell/module/az.storage/New-AzRmStorageShare) . Tento příklad vytvoří sdílenou složku `myshare`.

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $shareName `
    -EnabledProtocol SMB `
    -QuotaGiB 1024 | Out-Null
```

Názvy sdílených složek můžou obsahovat pouze malá písmena, číslice a spojovníky a nesmí začínat na spojovník. Kompletní informace o zadávání názvů sdílených složek a souborů najdete v tématu [Pojmenování a odkazování na sdílené složky, soubory a metadata](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Použití sdílené složky Azure
Služba Soubory Azure nabízí dva způsoby práce se soubory a složkami ve sdílené složce Azure: standardní průmyslový [protokol SMB (Server Message Block)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) a [souborový protokol REST](/rest/api/storageservices/file-service-rest-api). 

Informace o připojení sdílené složky s využitím protokolu SMB najdete v následujících dokumentech v závislosti na vašem operačním systému:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Použití sdílené složky Azure se souborovým protokolem REST 
Je možné pracovat přímo s protokolem REST (tj. handcrafting REST HTTP), ale nejběžnějším způsobem, jak používat protokol REST, je použít modul Azure PowerShell, rozhraní příkazového [řádku Azure](storage-how-to-use-files-cli.md)nebo sadu Azure Storage SDK, které nabízí dobrý obálku pro protokol REST v skriptovacím nebo programovacím jazyce podle vašeho výběru.  

Ve většině případů budete se sdílenou složkou Azure pracovat přes protokol SMB, protože vám to umožní používat stávající aplikace a nástroje, které očekáváte, že budete moct použít. Existuje však několik důvodů, proč může být výhodnější místo protokolu SMB použít souborové rozhraní REST API, jako například:

- Procházíte sdílenou složku z PowerShellu ve službě Cloud Shell (který nedokáže připojit sdílené složky přes protokol SMB).
- Využíváte bezserverové prostředky, jako je služba [Azure Functions](../../azure-functions/functions-overview.md). 
- Vytváříte službu pro přidávání hodnot, která bude pracovat s mnoha sdílenými složkami Azure, jako je třeba zálohování nebo prohledávání antivirové ochrany.

Následující příklady ukazují, jak používat modul Azure PowerShell k manipulaci s sdílenou složkou Azure pomocí protokolu File REST. `-Context`Parametr se používá k načtení klíče účtu úložiště, který provede označené akce proti sdílené složce. Pokud chcete načíst klíč účtu úložiště, musíte mít roli Azure `Owner` v účtu úložiště.

#### <a name="create-directory"></a>Vytvoření adresáře
Pokud chcete vytvořit nový adresář s názvem *myDirectory* v kořenovém adresáři sdílené složky Azure, použijte rutinu [New-AzStorageDirectory](/powershell/module/az.storage/New-AzStorageDirectory) .

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Path "myDirectory"
```

#### <a name="upload-a-file"></a>Nahrání souboru
Abychom předvedli, jak nahrát soubor pomocí rutiny [set-AzStorageFileContent](/powershell/module/az.storage/Set-AzStorageFileContent) , nejdřív musíme v Cloud Shell prostředí PowerShell vytvořit soubor k nahrání. 

Tento příklad do nového souboru v pomocné jednotce vloží aktuální datum a čas a pak soubor nahraje do sdílené složky.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
cd "~/CloudDrive/"
Get-Date | Out-File -FilePath "SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Source "SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

Pokud používáte PowerShell místně, musíte nahradit `~/CloudDrive/` za cestu, která existuje na vašem počítači.

Po nahrání souboru můžete pomocí rutiny [Get-AzStorageFile](/powershell/module/Az.Storage/Get-AzStorageFile) zkontrolovat, jestli se soubor nahrál do sdílené složky Azure. 

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\" 
```

#### <a name="download-a-file"></a>Stažení souboru
Pomocí rutiny [Get-AzStorageFileContent](/powershell/module/az.storage/Get-AzStorageFilecontent) můžete stáhnout kopii souboru, který jste právě Nahráli do pomocné jednotky vašeho Cloud Shell.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item `
    -Path "SampleDownload.txt" `
    -Force `
    -ErrorAction SilentlyContinue

Get-AzStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt" `
    -Destination "SampleDownload.txt"
```

Po stažení souboru můžete pomocí rutiny `Get-ChildItem` zkontrolovat stažení souboru do pomocné jednotky vaší služby Cloud Shell s PowerShellem.

```azurepowershell-interactive
Get-ChildItem | Where-Object { $_.Name -eq "SampleDownload.txt" }
``` 

#### <a name="copy-files"></a>Kopírování souborů
Jednou z běžných úloh je kopírování souborů z jedné sdílené složky do jiné sdílené složky. Chcete-li předvést tuto funkci, můžete vytvořit novou sdílenou složku a zkopírovat soubor, který jste právě Nahráli do této nové sdílené složky pomocí rutiny [Start-AzStorageFileCopy](/powershell/module/az.storage/Start-AzStorageFileCopy) . 

```azurepowershell-interactive
$otherShareName = "myshare2"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $otherShareName `
    -EnabledProtocol SMB `
    -QuotaGiB 1024 | Out-Null
  
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $otherShareName `
   -Path "myDirectory2"

Start-AzStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName $shareName `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName $otherShareName `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

Pokud teď vypíšete soubory v nové sdílené složce, měl by se zobrazit váš zkopírovaný soubor.

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $otherShareName `
    -Path "myDirectory2" 
```

I když `Start-AzStorageFileCopy` je rutina praktická pro ad hoc přesun souborů mezi sdílenými složkami Azure, pro migrace a větší přesuny dat doporučujeme `robocopy` na Windows a `rsync` na MacOS a Linux. `robocopy` a `rsync` k provádění přesunů dat místo rozhraní REST API použijte protokol SMB.

## <a name="create-and-manage-share-snapshots"></a>Vytváření a správa snímků sdílených složek
Další užitečnou úlohou, kterou se sdílenými složkami Azure můžete provádět, je vytváření snímků sdílených složek. Snímek uchovává sdílenou složku Azure k určitému bodu v čase. Snímky sdílených složek jsou podobné technologiím operačního systému, které už možná znáte, jako například:

- [Služba Stínová kopie svazku (VSS)](/windows/desktop/VSS/volume-shadow-copy-service-portal) pro systémy souborů Windows, jako je třeba NTFS a ReFS.
- Snímky [Správce logických svazků (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) pro systémy Linux.
- Snímky [systému souborů Apple (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) v případě macOS. 

Snímek sdílené složky pro sdílenou složku můžete vytvořit pomocí `Snapshot` metody v objektu PowerShellu pro sdílenou složku, která se načte pomocí rutiny [Get-AzStorageShare](/powershell/module/az.storage/Get-AzStorageShare) . 

```azurepowershell-interactive
$share = Get-AzStorageShare -Context $storageAcct.Context -Name $shareName
$snapshot = $share.CloudFileShare.Snapshot()
```

### <a name="browse-share-snapshots"></a>Procházení snímků sdílené složky
Obsah snímku sdílené složky můžete procházet tak, že do parametru `-Share` rutiny `Get-AzStorageFile` předáte odkaz na snímek (`$snapshot`).

```azurepowershell-interactive
Get-AzStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>Výpis snímků sdílené složky
Seznam snímků, které jste pořídili pro svou sdílenou složku, můžete zobrazit pomocí následujícího příkazu.

```azurepowershell-interactive
Get-AzStorageShare `
        -Context $storageAcct.Context | `
    Where-Object { $_.Name -eq $shareName -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>Obnovení ze snímku sdílené složky
Soubor můžete obnovit pomocí příkazu `Start-AzStorageFileCopy`, který jsme použili dříve. Pro účely tohoto rychlého startu nejprve odstraníme soubor `SampleUpload.txt`, který jsme nahráli dříve, abychom ho pak mohli obnovit ze snímku.

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt"

# Restore SampleUpload.txt from the share snapshot
Start-AzStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName $shareName `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Odstranění snímku sdílené složky
Snímek sdílené složky můžete odstranit pomocí rutiny [Remove-AzStorageShare](/powershell/module/az.storage/Remove-AzStorageShare) s proměnnou obsahující `$snapshot` odkaz na `-Share` parametr.

```azurepowershell-interactive
Remove-AzStorageShare `
    -Share $snapshot `
    -Confirm:$false `
    -Force
```

## <a name="clean-up-resources"></a>Vyčištění prostředků
Až budete hotovi, můžete k odebrání skupiny prostředků a všech souvisejících prostředků použít rutinu [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) . 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

Alternativně můžete prostředky odebrat jeden po druhém:

- Odebrání sdílených složek Azure, které jsme vytvořili pro účely tohoto rychlého startu:

    ```azurepowershell-interactive
    Get-AzRmStorageShare -StorageAccount $storageAcct | Remove-AzRmStorageShare -Force
    ```

    > [!Note]  
    > Před odstraněním sdílené složky Azure je nutné odstranit všechny snímky sdílené složky Azure, které jste vytvořili.

- Odebrání samotného účtu úložiště (tím se implicitně odeberou sdílené složky Azure, které jsme vytvořili, i všechny další prostředky úložiště, které jste mohli vytvořit, jako je například kontejner úložiště objektů blob v Azure):

    ```azurepowershell-interactive
    Remove-AzStorageAccount `
        -ResourceGroupName $storageAcct.ResourceGroupName `
        -Name $storageAcct.StorageAccountName
    ```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Co je Azure Files?](storage-files-introduction.md)