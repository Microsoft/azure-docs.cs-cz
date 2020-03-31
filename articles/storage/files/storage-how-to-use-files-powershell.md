---
title: Rychlý start pro správu sdílených složek Azure pomocí Azure PowerShellu
description: V tomto rychlém startu zjistíte, jak spravovat sdílené složky Azure pomocí Azure PowerShellu.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c419c2127b1c5fe3aaa60c6e828ff0c5a6676c07
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77598540"
---
# <a name="quickstart-create-and-manage-an-azure-file-share-with-azure-powershell"></a>Rychlý start: Vytvoření a správa sdílené složky Azure pomocí Azure PowerShellu 
Tato příručka vás provede základy práce se [sdílenými složkami Azure](storage-files-introduction.md) pomocí PowerShellu. Sdílené složky Azure jsou stejné jako ostatní sdílené složky, ale jsou uložené v cloudu a využívají platformu Azure. Sdílené složky Azure podporují standardní průmyslový protokol SMB a umožňují sdílení souborů mezi různými počítači, aplikacemi a instancemi. 

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud chcete nainstalovat a používat Prostředí PowerShell místně, tato příručka vyžaduje modul Azure PowerShell Az verze 0.7 nebo novější. Verzi modulu Azure PowerShell, kterou používáte, zjistíte spuštěním rutiny `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzAccount` a přihlásit se ke svému účtu Azure.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Pokud ještě nemáte skupinu prostředků Azure, můžete vytvořit novou s rutinou [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) 

Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v oblasti Západní USA 2:

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$region = "westus2"

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $region | Out-Null
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště
Účet úložiště je sdílený fond úložiště, který můžete použít k nasazení sdílených složek Azure. Účet úložiště může obsahovat neomezený počet sdílených složek a sdílená složka můžete obsahovat neomezený počet souborů až do omezení kapacity účtu úložiště. Tento příklad vytvoří univerzální verzi 2 (účet úložiště GPv2), která může ukládat standardní sdílené složky Azure nebo jiné prostředky úložiště, jako jsou objekty BLOB nebo fronty, na rotačních médiích pevného disku (HDD). Soubory Azure také podporuje prémiové diskové jednotky SSD(SSD); prémiové sdílené složky Azure lze vytvářet v účtech úložiště Úložiště souborů FileStorage.

Tento příklad vytvoří účet úložiště pomocí rutiny [New-AzStorageAccount.](/powershell/module/az.storage/new-azstorageaccount) Účet úložiště se nazývá *mystorageaccount\<náhodné číslo>* a odkaz na tento účet úložiště je uložen v proměnné **$storageAcct**. Názvy účtů úložiště musí být jedinečné, proto k názvu pomocí rutiny `Get-Random` připojte číslo, aby byl jedinečný. 

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
> Sdílené položky větší než 5 TiB (maximálně 100 TiB na akcii) jsou k dispozici pouze v místně redundantních (LRS) a zónových redundantních účtech (ZRS). Chcete-li vytvořit účet úložiště geograficky redundantní (GRS) nebo geograficky `-EnableLargeFileShare` redundantní (GZRS), odeberte parametr.

## <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure
Teď můžete vytvořit svou první sdílenou složku Azure. Sdílenou složku můžete vytvořit pomocí rutiny [New-AzRmStorageShare.](/powershell/module/az.storage/New-AzRmStorageShare) Tento příklad vytvoří sdílenou složku `myshare`.

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $shareName `
    -QuotaGiB 1024 | Out-Null
```

Názvy sdílených složek můžou obsahovat pouze malá písmena, číslice a spojovníky a nesmí začínat na spojovník. Kompletní informace o zadávání názvů sdílených složek a souborů najdete v tématu [Pojmenování a odkazování na sdílené složky, soubory a metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Použití sdílené složky Azure
Služba Soubory Azure nabízí dva způsoby práce se soubory a složkami ve sdílené složce Azure: standardní průmyslový [protokol SMB (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) a [souborový protokol REST](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

Informace o připojení sdílené složky s využitím protokolu SMB najdete v následujících dokumentech v závislosti na vašem operačním systému:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [Macos](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Použití sdílené složky Azure se souborovým protokolem REST 
Je možné pracovat s protokolem File REST přímo (tj. ruční volání REST HTTP volání sami), ale nejběžnější způsob, jak používat protokol File REST je použití modulu Azure PowerShell, [Azure CLI](storage-how-to-use-files-cli.md)nebo Azure Storage SDK, které poskytují pěkný obal kolem protokolu FILE REST v skriptovacím/programovacím jazyce podle vašeho výběru.  

Ve většině případů budete se sdílenou složkou Azure pracovat přes protokol SMB, protože vám to umožní používat stávající aplikace a nástroje, které očekáváte, že budete moct použít. Existuje však několik důvodů, proč může být výhodnější místo protokolu SMB použít souborové rozhraní REST API, jako například:

- Procházíte sdílenou složku z PowerShellu ve službě Cloud Shell (který nedokáže připojit sdílené složky přes protokol SMB).
- Využíváte bezserverové prostředky, jako je služba [Azure Functions](../../azure-functions/functions-overview.md). 
- Vytváříte službu s přidanou hodnotou, která bude pracovat s mnoha sdílenými složkami souborů Azure, jako je například zálohování nebo antivirové kontroly.

Následující příklady ukazují, jak použít modul Azure PowerShell k manipulaci se správou souborů Azure s protokolem FILE REST. Parametr `-Context` se používá k načtení klíče účtu úložiště k provedení uvedených akcí proti sdílené složce. Chcete-li načíst klíč účtu úložiště, musíte `Owner` mít roli RBAC na účtu úložiště.

#### <a name="create-directory"></a>Vytvoření adresáře
Chcete-li vytvořit nový adresář s názvem *myDirectory* v kořenovém adresáři sdílené složky Azure, použijte rutinu [New-AzStorageDirectory.](/powershell/module/az.storage/New-AzStorageDirectory)

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Path "myDirectory"
```

#### <a name="upload-a-file"></a>Nahrání souboru
Chcete-li ukázat, jak nahrát soubor pomocí rutiny [Set-AzStorageFileContent,](/powershell/module/az.storage/Set-AzStorageFileContent) musíme nejprve vytvořit soubor uvnitř odkládací jednotky prostředí PowerShell, který chcete nahrát. 

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

Po nahrání souboru můžete pomocí rutiny [Get-AzStorageFile](/powershell/module/Az.Storage/Get-AzStorageFile) zkontrolovat, zda byl soubor nahrán do sdílené složky Azure. 

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\" 
```

#### <a name="download-a-file"></a>Stažení souboru
Pomocí rutiny [Get-AzStorageFileContent](/powershell/module/az.storage/Get-AzStorageFilecontent) můžete stáhnout kopii souboru, který jste právě nahráli na nulovací jednotku prostředí Cloud Shell.

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
Jednou z běžných úloh je kopírování souborů z jedné sdílené složky do jiné sdílené složky. Chcete-li tuto funkci předvést, můžete vytvořit novou sdílenou složku a zkopírovat soubor, který jste právě odeslali do této nové sdílené složky, pomocí rutiny [Start-AzStorageFileCopy.](/powershell/module/az.storage/Start-AzStorageFileCopy) 

```azurepowershell-interactive
$otherShareName = "myshare2"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $otherShareName `
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

Zatímco `Start-AzStorageFileCopy` rutina je vhodná pro přesuny souborů ad hoc mezi sdílenými složkami `robocopy` Azure, `rsync` pro migrace a větší přesuny dat doporučujeme ve Windows a na macOS a Linuxu. `robocopy`a `rsync` použít SMB k provádění přesunů dat namísto rozhraní FILEREST API.

## <a name="create-and-manage-share-snapshots"></a>Vytváření a správa snímků sdílených složek
Další užitečnou úlohou, kterou se sdílenými složkami Azure můžete provádět, je vytváření snímků sdílených složek. Snímek uchovává sdílenou složku Azure k určitému bodu v čase. Snímky sdílených složek jsou podobné technologiím operačního systému, které už možná znáte, jako například:

- [Služba Stínová kopie svazku (VSS)](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal) pro systémy souborů Windows, jako jsou ntfs a refs.
- [Snímky Správce logických svazků (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) pro systémy Linux.
- Snímky [systému souborů Apple (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) v případě macOS. 

Snímek sdílené složky pro sdílenou `Snapshot` složku můžete vytvořit pomocí metody na objektu PowerShell pro sdílenou složku, která se načte pomocí rutiny [Get-AzStorageShare.](/powershell/module/az.storage/Get-AzStorageShare) 

```azurepowershell-interactive
$share = Get-AzStorageShare -Context $storageAcct.Context -Name $shareName
$snapshot = $share.Snapshot()
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
Snímek sdílené složky můžete odstranit pomocí rutiny [Remove-AzStorageShare](/powershell/module/az.storage/Remove-AzStorageShare) s `$snapshot` proměnnou `-Share` obsahující odkaz na parametr.

```azurepowershell-interactive
Remove-AzStorageShare `
    -Share $snapshot `
    -Confirm:$false `
    -Force
```

## <a name="clean-up-resources"></a>Vyčištění prostředků
Po dokončení můžete pomocí rutiny [Odebrat azResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) odebrat skupinu prostředků a všechny související prostředky. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

Alternativně můžete prostředky odebrat jeden po druhém:

- Odebrání sdílených složek Azure, které jsme vytvořili pro účely tohoto rychlého startu:

    ```azurepowershell-interactive
    Get-AzRmStorageShare -StorageAccount $storageAcct | Remove-AzRmStorageShare -Force
    ```

    > [!Note]  
    > Před odstraněním sdílené složky Azure je nutné odstranit všechny snímky sdílené složky pro sdílené složky Azure.

- Odebrání samotného účtu úložiště (tím se implicitně odeberou sdílené složky Azure, které jsme vytvořili, i všechny další prostředky úložiště, které jste mohli vytvořit, jako je například kontejner úložiště objektů blob v Azure):

    ```azurepowershell-interactive
    Remove-AzStorageAccount `
        -ResourceGroupName $storageAcct.ResourceGroupName `
        -Name $storageAcct.StorageAccountName
    ```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Co je služba Soubory Azure?](storage-files-introduction.md)