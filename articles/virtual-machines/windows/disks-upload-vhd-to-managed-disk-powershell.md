---
title: Nahrání virtuálního pevného disku do Azure nebo zkopírování disku napříč oblastmi – Azure PowerShell
description: Zjistěte, jak nahrát virtuální pevný disk na spravovaný disk Azure a zkopírovat spravovaný disk napříč oblastmi pomocí Azure PowerShellu pomocí přímého nahrávání.
author: roygara
ms.author: rogarana
ms.date: 03/27/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 55606aeeb9f6445027f5da49821dbc4970764ade
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421047"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-powershell"></a>Nahrání virtuálního pevného disku do Azure nebo zkopírování spravovaného disku do jiné oblasti – Azure PowerShell

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Požadavky

- Stáhněte si nejnovější [verzi AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Nainstalujte modul Azure PowerShell](/powershell/azure/install-Az-ps).
- Pokud máte v úmyslu nahrát virtuální pevný disk z místního prostředí: Pevný virtuální disk velikosti, který [byl připraven pro Azure](prepare-for-upload-vhd-image.md), uložený místně.
- Nebo spravovaný disk v Azure, pokud máte v úmyslu provést akci kopírování.

## <a name="getting-started"></a>Začínáme

Pokud dáváte přednost nahrávání disků přes GUI, můžete tak učinit pomocí Průzkumníka úložiště Azure. Podrobnosti najdete v [části: Správa spravovaných disků Azure pomocí Průzkumníka Azure](disks-use-storage-explorer-managed-disks.md)

Chcete-li nahrát virtuální pevný disk do Azure, budete muset vytvořit prázdný spravovaný disk, který je nakonfigurovaný pro tento proces nahrávání. Před vytvořením, je tu nějaké další informace, které byste měli vědět o těchto disků.

Tento druh spravovaného disku má dva jedinečné stavy:

- ReadToUpload, což znamená, že disk je připraven k příjmu nahrát, ale nebyl vygenerován žádný [podpis zabezpečeného přístupu](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS).
- ActiveUpload, což znamená, že disk je připraven k přijetí nahrávání a SAS byla vygenerována.

> [!NOTE]
> Zatímco v jednom z těchto stavů bude spravovaný disk účtovány při [standardních cenách hdd](https://azure.microsoft.com/pricing/details/managed-disks/), bez ohledu na skutečný typ disku. Například P10 se bude účtovat jako S10. To bude platit, dokud `revoke-access` se volá na spravovaném disku, který je nutný k připojení disku k virtuálnímu počítači.

## <a name="create-an-empty-managed-disk"></a>Vytvoření prázdného spravovaného disku

Než budete moci vytvořit prázdný standardní pevný disk pro nahrávání, budete potřebovat velikost souboru virtuálního pevného disku, který chcete nahrát, v bajtech. Příklad kódu dostane, že pro vás, ale k `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`tomu sami můžete použít: . Tato hodnota se používá při zadávání parametru **-UploadSizeInBytes.**

Nyní v místním prostředí vytvořte prázdný standardní pevný disk pro nahrávání zadáním nastavení **Upload** v parametru **-CreateOption** a parametru **-UploadSizeInBytes** v rutině [New-AzDiskConfig.](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) Pak volejte [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0) vytvořit disk.

Nahraďte `<yourdiskname>`a `<yourresourcegroupname>` `<yourregion>` spusťte následující příkazy:

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location '<yourregion>' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName '<yourresourcegroupname' -DiskName '<yourdiskname>' -Disk $diskconfig
```

Pokud chcete nahrát buď prémiový SSD nebo standardní SSD, nahraďte **Standard_LRS** **buď Premium_LRS** nebo **StandardSSD_LRS**. Ultra disky ještě nejsou podporovány.

Teď, když jste vytvořili prázdný spravovaný disk, který je nakonfigurovaný pro proces nahrávání, můžete do něj nahrát virtuální pevný disk. Chcete-li na disk nahrát virtuální pevný disk, budete potřebovat zapisovatelný SAS, abyste na něj mohli odkazovat jako na cíl pro nahrávání.

Chcete-li vygenerovat zapisovatelné sazby prázdného spravovaného disku, nahraďte `<yourdiskname>`a `<yourresourcegroupname>`použijte následující příkazy:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="upload-a-vhd"></a>Nahrání virtuálního pevného disku

Teď, když máte SAS pro prázdný spravovaný disk, můžete ho použít k nastavení spravovaného disku jako cíle pro příkaz pro nahrávání.

Pomocí aplikace AzCopy v10 můžete nahrát místní soubor VHD na spravovaný disk zadáním vygenerovaného identifikátoru URI SAS.

Toto nahrání má stejnou propustnost jako ekvivalentní [standardní pevný disk](disks-types.md#standard-hdd). Například pokud máte velikost, která se rovná S4, budete mít propustnost až 60 MiB/s. Ale pokud máte velikost, která se rovná S70, budete mít propustnost až 500 MiB / s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

Po dokončení nahrávání a již není nutné zapisovat žádná další data na disk, odvolat SAS. Zrušením SAS změníte stav spravovaného disku a umožníte připojit disk k virtuálnímu počítače.

Nahraďte `<yourdiskname>`a `<yourresourcegroupname>`spusťte následující příkaz:

```powershell
Revoke-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="copy-a-managed-disk"></a>Kopírování spravovaného disku

Přímé nahrávání také zjednodušuje proces kopírování spravovaného disku. Můžete buď kopírovat v rámci stejné oblasti nebo zkopírovat spravovaný disk do jiné oblasti.

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

Teď, když jste úspěšně nahráli virtuální pevný disk na spravovaný disk, můžete připojit disk k virtuálnímu počítači a začít ho používat.

Informace o připojení datového disku k virtuálnímu počítači najdete v článku o tématu: [Připojení datového disku k virtuálnímu počítači windows pomocí prostředí PowerShell](attach-disk-ps.md). Pokud chcete disk použít jako disk operačního [systému, přečtěte si informace o vytvoření virtuálního počítače se systémem Windows ze specializovaného disku](create-vm-specialized.md#create-the-new-vm).
