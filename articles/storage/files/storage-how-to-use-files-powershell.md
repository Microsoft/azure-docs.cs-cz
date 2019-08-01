---
title: Rychlý start pro správu sdílených složek Azure pomocí Azure PowerShellu
description: V tomto rychlém startu zjistíte, jak spravovat sdílené složky Azure pomocí Azure PowerShellu.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 802ad497f95a43665665d7e7dbd06c9081eba74a
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699505"
---
# <a name="quickstart-create-and-manage-an-azure-file-share-with-azure-powershell"></a>Rychlý start: Vytvoření a Správa sdílené složky Azure pomocí Azure PowerShell 
Tato příručka vás provede základy práce se [sdílenými složkami Azure](storage-files-introduction.md) pomocí PowerShellu. Sdílené složky Azure jsou stejné jako ostatní sdílené složky, ale jsou uložené v cloudu a využívají platformu Azure. Sdílené složky Azure podporují standardní průmyslový protokol SMB a umožňují sdílení souborů mezi různými počítači, aplikacemi a instancemi. 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud chcete nainstalovat a používat PowerShell místně, je nutné, aby tato příručka Azure PowerShell modul AZ verze 0,7 nebo novější. Verzi modulu Azure PowerShell, kterou používáte, zjistíte spuštěním rutiny `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzAccount` a přihlásit se ke svému účtu Azure.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Pokud ještě nemáte skupinu prostředků Azure, můžete vytvořit novou pomocí rutiny [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . 

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v oblasti USA – východ:

```azurepowershell-interactive
New-AzResourceGroup `
    -Name myResourceGroup `
    -Location EastUS
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště
Účet úložiště je sdílený fond úložiště, který můžete použít k nasazování sdílených složek Azure nebo jiných prostředků úložiště, jako jsou objekty blob nebo fronty. Účet úložiště může obsahovat neomezený počet sdílených složek a sdílená složka můžete obsahovat neomezený počet souborů až do omezení kapacity účtu úložiště.

Tento příklad vytvoří účet úložiště pomocí rutiny [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) . Účet úložiště má název *mystorageaccount\<Random Number >* a odkaz na tento účet úložiště je uložený v **$storageAcct**proměnné. Názvy účtů úložiště musí být jedinečné, proto k názvu pomocí rutiny `Get-Random` připojte číslo, aby byl jedinečný. 

```azurepowershell-interactive 
$storageAcct = New-AzStorageAccount `
                  -ResourceGroupName "myResourceGroup" `
                  -Name "mystorageacct$(Get-Random)" `
                  -Location eastus `
                  -SkuName Standard_LRS 
```

## <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure
Teď můžete vytvořit svou první sdílenou složku Azure. Sdílenou složku můžete vytvořit pomocí rutiny [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) . Tento příklad vytvoří sdílenou složku `myshare`.

```azurepowershell-interactive
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

Názvy sdílených složek můžou obsahovat pouze malá písmena, číslice a spojovníky a nesmí začínat na spojovník. Kompletní informace o zadávání názvů sdílených složek a souborů najdete v tématu [Pojmenování a odkazování na sdílené složky, soubory a metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Použití sdílené složky Azure
Služba Soubory Azure nabízí dva způsoby práce se soubory a složkami ve sdílené složce Azure: standardní průmyslový [protokol SMB (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) a [souborový protokol REST](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

Informace o připojení sdílené složky s využitím protokolu SMB najdete v následujících dokumentech v závislosti na vašem operačním systému:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Použití sdílené složky Azure se souborovým protokolem REST 
Je možné pracovat přímo s protokolem REST protokolu přímo (tj. handcrafting REST HTTP), ale nejběžnějším způsobem použití protokolu REST je použít modul Azure PowerShell, rozhraní příkazového [řádku Azure](storage-how-to-use-files-cli.md)nebo sadu Azure Storage SDK. Poskytněte dobrý obálku k protokolu REST ve skriptovacím nebo programovacím jazyce podle vašeho výběru.  

Ve většině případů budete se sdílenou složkou Azure pracovat přes protokol SMB, protože vám to umožní používat stávající aplikace a nástroje, které očekáváte, že budete moct použít. Existuje však několik důvodů, proč může být výhodnější místo protokolu SMB použít souborové rozhraní REST API, jako například:

- Procházíte sdílenou složku z PowerShellu ve službě Cloud Shell (který nedokáže připojit sdílené složky přes protokol SMB).
- Potřebujete spustit skript nebo aplikaci z klienta, který nedokáže připojit sdílené složky SMB, jako jsou například místní klienti s blokovaným portem 445.
- Využíváte bezserverové prostředky, jako je služba [Azure Functions](../../azure-functions/functions-overview.md). 

Následující příklady ukazují, jak používat modul Azure PowerShell k manipulaci s sdílenou složkou Azure pomocí protokolu File REST. 

#### <a name="create-directory"></a>Vytvořit adresář
Pokud chcete vytvořit nový adresář s názvem *myDirectory* v kořenovém adresáři sdílené složky Azure, použijte rutinu [New-AzStorageDirectory](/powershell/module/az.storage/New-AzStorageDirectory) .

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Path "myDirectory"
```

#### <a name="upload-a-file"></a>Nahrát soubor
Abychom předvedli, jak nahrát soubor pomocí rutiny [set-AzStorageFileContent](/powershell/module/az.storage/Set-AzStorageFileContent) , nejdřív musíme v Cloud Shell prostředí PowerShell vytvořit soubor k nahrání. 

Tento příklad do nového souboru v pomocné jednotce vloží aktuální datum a čas a pak soubor nahraje do sdílené složky.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
Get-Date | Out-File -FilePath "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Source "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

Pokud používáte PowerShell místně, musíte nahradit `C:\Users\ContainerAdministrator\CloudDrive\` za cestu, která existuje na vašem počítači.

Po nahrání souboru můžete pomocí rutiny [Get-AzStorageFile](/powershell/module/Az.Storage/Get-AzStorageFile) zkontrolovat, jestli se soubor nahrál do sdílené složky Azure. 

```azurepowershell-interactive
Get-AzStorageFile -Context $storageAcct.Context -ShareName "myshare" -Path "myDirectory" 
```

#### <a name="download-a-file"></a>Stáhnout soubor
Pomocí rutiny [Get-AzStorageFileContent](/powershell/module/az.storage/Get-AzStorageFilecontent) můžete stáhnout kopii souboru, který jste právě Nahráli do pomocné jednotky vašeho Cloud Shell.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item `
     -Path "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt" `
     -Force `
     -ErrorAction SilentlyContinue

Get-AzStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt" ` 
    -Destination "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt"
```

Po stažení souboru můžete pomocí rutiny `Get-ChildItem` zkontrolovat stažení souboru do pomocné jednotky vaší služby Cloud Shell s PowerShellem.

```azurepowershell-interactive
Get-ChildItem -Path "C:\Users\ContainerAdministrator\CloudDrive"
``` 

#### <a name="copy-files"></a>Kopírování souborů
Jednou z běžných úloh je kopírování souborů z jedné sdílené složky do jiné nebo do a z kontejneru úložiště objektů blob v Azure. Chcete-li předvést tuto funkci, můžete vytvořit novou sdílenou složku a zkopírovat soubor, který jste právě Nahráli do této nové sdílené složky pomocí rutiny [Start-AzStorageFileCopy](/powershell/module/az.storage/Start-AzStorageFileCopy) . 

```azurepowershell-interactive
New-AzStorageShare `
    -Name "myshare2" `
    -Context $storageAcct.Context
  
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare2" `
   -Path "myDirectory2"

Start-AzStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName "myshare" `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName "myshare2" `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

Pokud teď vypíšete soubory v nové sdílené složce, měl by se zobrazit váš zkopírovaný soubor.

```azurepowershell-interactive
Get-AzStorageFile -Context $storageAcct.Context -ShareName "myshare2" -Path "myDirectory2" 
```

I když `Start-AzStorageFileCopy` je rutina praktická pro přesun souborů mezi sdílenými složkami Azure a kontejnery úložiště objektů BLOB v Azure, doporučujeme, abyste AzCopy pro větší přesuny (s ohledem na počet a velikost přesunutých souborů). Další informace o [AzCopy pro Windows](../common/storage-use-azcopy.md) a [AzCopy pro Linux](../common/storage-use-azcopy-linux.md). Nástroj AzCopy musí být nainstalovaný místně – ve službě Cloud Shell není dostupný. 

## <a name="create-and-manage-share-snapshots"></a>Vytváření a správa snímků sdílených složek
Další užitečnou úlohou, kterou se sdílenými složkami Azure můžete provádět, je vytváření snímků sdílených složek. Snímek uchovává sdílenou složku Azure k určitému bodu v čase. Snímky sdílených složek jsou podobné technologiím operačního systému, které už možná znáte, jako například:
- [Služba Stínová kopie svazku (VSS)](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal) v případě systémů souborů Windows, jako jsou NTFS a ReFS.
- Snímky [Správce logických svazků (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) v případě systémů Linux.
- Snímky [systému souborů Apple (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) v případě macOS. 
 Snímek sdílené složky pro sdílenou složku můžete vytvořit pomocí `Snapshot` metody v objektu PowerShellu pro sdílenou složku, která se načte pomocí rutiny [Get-AzStorageShare](/powershell/module/az.storage/Get-AzStorageShare) . 

```azurepowershell-interactive
$share = Get-AzStorageShare -Context $storageAcct.Context -Name "myshare"
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
Get-AzStorageShare -Context $storageAcct.Context | Where-Object { $_.Name -eq "myshare" -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>Obnovení ze snímku sdílené složky
Soubor můžete obnovit pomocí příkazu `Start-AzStorageFileCopy`, který jsme použili dříve. Pro účely tohoto rychlého startu nejprve odstraníme soubor `SampleUpload.txt`, který jsme nahráli dříve, abychom ho pak mohli obnovit ze snímku.

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt"
 # Restore SampleUpload.txt from the share snapshot
Start-AzStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName "myshare" `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Odstranění snímku sdílené složky
Snímek sdílené složky můžete odstranit pomocí rutiny [Remove-AzStorageShare](/powershell/module/az.storage/Remove-AzStorageShare) s proměnnou obsahující `$snapshot` odkaz na `-Share` parametr.

```azurepowershell-interactive
Remove-AzStorageShare -Share $snapshot
```

## <a name="clean-up-resources"></a>Vyčištění prostředků
Až budete hotovi, můžete k odebrání skupiny prostředků a všech souvisejících prostředků použít rutinu [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) . 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

Alternativně můžete prostředky odebrat jeden po druhém:

- Odebrání sdílených složek Azure, které jsme vytvořili pro účely tohoto rychlého startu:

    ```azurepowershell-interactive
    Get-AzStorageShare -Context $storageAcct.Context | Where-Object { $_.IsSnapshot -eq $false } | ForEach-Object { 
        Remove-AzStorageShare -Context $storageAcct.Context -Name $_.Name
    }
    ```

- Odebrání samotného účtu úložiště (tím se implicitně odeberou sdílené složky Azure, které jsme vytvořili, i všechny další prostředky úložiště, které jste mohli vytvořit, jako je například kontejner úložiště objektů blob v Azure):

    ```azurepowershell-interactive
    Remove-AzStorageAccount -ResourceGroupName $storageAcct.ResourceGroupName -Name $storageAcct.StorageAccountName
    ```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Co je služba Soubory Azure?](storage-files-introduction.md)
