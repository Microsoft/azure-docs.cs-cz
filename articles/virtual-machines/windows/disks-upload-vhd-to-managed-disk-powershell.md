---
title: Nahrání virtuálního pevného disku do Azure pomocí Azure PowerShellu
description: Zjistěte, jak nahrát virtuální ho dhd na spravovaný disk Azure a zkopírovat spravovaný disk napříč oblastmi pomocí Azure PowerShellu pomocí přímého nahrávání.
author: roygara
ms.author: rogarana
ms.date: 03/13/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 883fea1e25ded26c35e96d11edd8f417e96db30e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369552"
---
# <a name="upload-a-vhd-to-azure-using-azure-powershell"></a>Nahrání virtuálního pevného disku do Azure pomocí Azure PowerShellu

Tento článek vysvětluje, jak nahrát virtuální pevný disk z místního počítače na spravovaný disk Azure. Dříve jste museli sledovat složitější proces, který zahrnoval přípravu dat v účtu úložiště a správu tohoto účtu úložiště. Nyní již nemusíte spravovat účet úložiště nebo v něm nastavovat data, abyste mohli nahrát virtuální pevný disk. Místo toho vytvoříte prázdný spravovaný disk a nahrajete do něj virtuální disk přímo. To zjednodušuje nahrávání místních virtuálních počítačů do Azure a umožňuje nahrát virtuální pevný disk až 32 TiB přímo na velký spravovaný disk.

Pokud poskytujete řešení zálohování pro virtuální počítače IaaS v Azure, doporučujeme použít přímé nahrávání k obnovení záloh zákazníků na spravované disky. Pokud nahráváte virtuální pevný disk z externího počítače do Azure, rychlostzávisí na místní šířce pásma. Pokud používáte virtuální počítač Azure, šířka pásma bude stejná jako u standardních pevných disků.

V současné době je přímé nahrávání podporováno pro standardní disky spravované pevným diskem, standardním SSD disky a prémiovými disky spravovanými ssd. To ještě není podporováno pro ultra SSD.

## <a name="prerequisites"></a>Požadavky

- Stáhněte si nejnovější [verzi AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Nainstalujte modul Azure PowerShell](/powershell/azure/install-Az-ps).
- Pokud máte v úmyslu nahrát virtuální pevný disk z místního prostředí: Pevný virtuální disk velikosti, který [byl připraven pro Azure](prepare-for-upload-vhd-image.md), uložený místně.
- Nebo spravovaný disk v Azure, pokud máte v úmyslu provést akci kopírování.

## <a name="create-an-empty-managed-disk"></a>Vytvoření prázdného spravovaného disku

Chcete-li nahrát virtuální pevný disk do Azure, budete muset vytvořit prázdný spravovaný disk, který je nakonfigurovaný pro tento proces nahrávání. Před vytvořením, je tu nějaké další informace, které byste měli vědět o těchto disků.

Tento druh spravovaného disku má dva jedinečné stavy:

- ReadToUpload, což znamená, že disk je připraven k příjmu nahrát, ale nebyl vygenerován žádný [podpis zabezpečeného přístupu](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS).
- ActiveUpload, což znamená, že disk je připraven k přijetí nahrávání a SAS byla vygenerována.

Zatímco v jednom z těchto stavů bude spravovaný disk účtovány při [standardních cenách hdd](https://azure.microsoft.com/pricing/details/managed-disks/), bez ohledu na skutečný typ disku. Například P10 se bude účtovat jako S10. To bude platit, dokud `revoke-access` se volá na spravovaném disku, který je nutný k připojení disku k virtuálnímu počítači.

Před vytvořením prázdného standardního pevného disku pro nahrávání budete potřebovat velikost souboru v bajtech virtuálního pevného disku, který chcete nahrát. Příklad kódu dostane, že pro vás, ale k `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`tomu sami můžete použít: . Tato hodnota se používá při zadávání parametru **-UploadSizeInBytes.**

Nyní v místním prostředí vytvořte prázdný standardní pevný disk pro nahrávání zadáním nastavení **Upload** v parametru **-CreateOption** a parametru **-UploadSizeInBytes** v rutině [New-AzDiskConfig.](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) Potom zavolejte [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0) k vytvoření disku:

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location 'West US' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName' -Disk $diskconfig
```

Pokud chcete nahrát buď prémiový SSD nebo standardní SSD, nahraďte **Standard_LRS** **buď Premium_LRS** nebo **StandardSSD_LRS**. Ultra SSD ještě není podporováno.

Nyní jste vytvořili prázdný spravovaný disk, který je nakonfigurován pro proces nahrávání. Chcete-li na disk nahrát virtuální disk, budete potřebovat zapisovatelný SAS, abyste na něj mohli odkazovat jako na cíl pro nahrávání.

Chcete-li vygenerovat zapisovatelné SAS prázdného spravovaného disku, použijte následující příkaz:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName 'myResouceGroup' -DiskName 'myDiskName' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="upload-vhd"></a>Nahrát vhd

Teď, když máte SAS pro prázdný spravovaný disk, můžete ho použít k nastavení spravovaného disku jako cíle pro příkaz pro nahrávání.

Pomocí aplikace AzCopy v10 můžete nahrát místní soubor VHD na spravovaný disk zadáním vygenerovaného identifikátoru URI SAS.

Toto nahrání má stejnou propustnost jako ekvivalentní [standardní pevný disk](disks-types.md#standard-hdd). Například pokud máte velikost, která se rovná S4, budete mít propustnost až 60 MiB/s. Ale pokud máte velikost, která se rovná S70, budete mít propustnost až 500 MiB / s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

Po dokončení nahrávání a již není nutné zapisovat žádná další data na disk, odvolat SAS. Zrušením SAS změníte stav spravovaného disku a umožníte připojit disk k virtuálnímu počítače.

```powershell
Revoke-AzDiskAccess -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="copy-a-managed-disk"></a>Kopírování spravovaného disku

Přímé nahrávání také zjednodušuje proces kopírování spravovaného disku. Můžete kopírovat v rámci stejné oblasti nebo mezi oblastmi (do jiné oblasti).

Postupujte skript to udělá za vás, proces je podobný krokům popsaným výše, s některými rozdíly, protože pracujete s existujícím diskem.

> [!IMPORTANT]
> Musíte přidat posun 512, když poskytujete velikost disku v bajtů spravovaného disku z Azure. Důvodem je, že Azure vynese zápatí při vrácení velikosti disku. Kopie se nezdaří, pokud tak neučiníte. Následující skript již dělá to pro vás.

Nahraďte `<sourceDiskNameHere>` `<targetDiskNameHere>`hodnoty `<targetResourceGroupHere>` `<yourOSTypeHere>` `<sourceResourceGroupHere>`, `<yourTargetLocationHere>` , , , a (příklad hodnoty umístění uswest2) a spusťte následující skript, abyste zkopírovali spravovaný disk.

```powershell

$sourceRG = <sourceResourceGroupHere>
$sourceDiskName = <sourceDiskNameHere>
$targetDiskName = <targetDiskNameHere>
$targetRG = <targetResourceGroupHere>
$targetLocate = <yourTargetLocationHere>
#Expected value for OS is either "Windows" or "Linux"
$targetOS = <yourOSTypeHere>

$sourceDisk = Get-AzDisk -ResourceGroupName $sourceRG -DiskName $sourceDiskName

# Adding the sizeInBytes with the 512 offset, and the -Upload flag
$targetDiskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -osType $targetOS -UploadSizeInBytes $($sourceDisk.DiskSizeBytes+512) -Location $targetLocate -CreateOption 'Upload'

$targetDisk = New-AzDisk -ResourceGroupName $targetRG -DiskName $targetDiskName -Disk $targetDiskconfig

$sourceDiskSas = Grant-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName -DurationInSecond 86400 -Access 'Read'

$targetDiskSas = Grant-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName -DurationInSecond 86400 -Access 'Write'

azcopy copy $sourceDiskSas.AccessSAS $targetDiskSas.AccessSAS --blob-type PageBlob

Revoke-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName

Revoke-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName 
```

## <a name="next-steps"></a>Další kroky

Teď, když jste úspěšně nahráli virtuální disk na spravovaný disk, můžete připojit disk k virtuálnímu počítači a začít ho používat.

Informace o připojení datového disku k virtuálnímu počítači najdete v článku o tématu: [Připojení datového disku k virtuálnímu počítači windows pomocí prostředí PowerShell](attach-disk-ps.md). Pokud chcete disk použít jako disk operačního [systému, přečtěte si informace o vytvoření virtuálního počítače se systémem Windows ze specializovaného disku](create-vm-specialized.md#create-the-new-vm).
