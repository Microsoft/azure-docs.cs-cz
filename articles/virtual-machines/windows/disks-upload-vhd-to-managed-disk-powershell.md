---
title: Nahrání virtuálního pevného disku do Azure pomocí Azure PowerShell
description: Přečtěte si, jak nahrát virtuální pevný disk do spravovaného disku Azure a zkopírovat spravovaný disk do různých oblastí pomocí Azure PowerShell.
author: roygara
ms.author: rogarana
ms.date: 05/06/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: de9975151270ccce8d4a7abd58210c6550d40464
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720344"
---
# <a name="upload-a-vhd-to-azure-using-azure-powershell"></a>Nahrání virtuálního pevného disku do Azure pomocí Azure PowerShell

Tento článek vysvětluje, jak nahrát virtuální pevný disk z místního počítače do spravovaného disku Azure. Dříve museli byste postupovat podle dalšího Zahrnutého procesu, který zahrnuje přípravu vašich dat v účtu úložiště a správu tohoto účtu úložiště. Nyní už nebudete muset spravovat účet úložiště nebo data fáze v něm, abyste nahráli VHD. Místo toho vytvoříte prázdný spravovaný disk a nahrajete do něj VHD přímo. Tato možnost zjednodušuje nahrávání místních virtuálních počítačů do Azure a umožňuje odeslat virtuální pevný disk až 32 TiB přímo do velkého spravovaného disku.

Pokud poskytujete řešení zálohování pro virtuální počítače s IaaS v Azure, doporučujeme použít přímé nahrávání pro obnovení záloh zákazníka na spravované disky. Pokud nahráváte virtuální pevný disk z externího počítače mimo Azure, rychlosti závisí na vaší místní šířce pásma. Pokud používáte virtuální počítač Azure, Šířka pásma bude stejná jako u HDD Standard.

V současné době se podporuje přímé nahrávání pro disky Standard HDD, Standard SSD a Premium SSD. Pro ultra SSD se ještě nepodporuje.

## <a name="prerequisites"></a>Předpoklady

- Stáhněte si nejnovější [verzi nástroje AzCopy v10 za účelem](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Nainstalujte modul Azure PowerShell](/powershell/azure/install-Az-ps).
- Pokud máte v úmyslu nahrát VHD z PEM: virtuální pevný disk [připravený pro Azure](prepare-for-upload-vhd-image.md), uložený místně.
- Nebo spravovaný disk v Azure, pokud máte v úmyslu provést akci kopírování.

## <a name="create-an-empty-managed-disk"></a>Vytvoření prázdného spravovaného disku

Pokud chcete nahrát virtuální pevný disk do Azure, budete muset vytvořit prázdný spravovaný disk, který je nakonfigurovaný pro tento proces nahrávání. Před tím, než ho vytvoříte, se dozvíte o některých dalších informacích, které byste měli znát na těchto discích.

Tento druh spravovaného disku má dva jedinečné stavy:

- ReadToUpload, což znamená, že disk je připravený k přijetí nahrávání, ale nevytvořil se žádný [podpis zabezpečeného přístupu](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS).
- ActiveUpload, což znamená, že disk je připravený k přijetí nahrávání a vygeneroval se SAS.

V některém z těchto stavů se spravovaný disk bude účtovat podle [standardních cen HDD](https://azure.microsoft.com/pricing/details/managed-disks/), bez ohledu na skutečný typ disku. Například P10 bude účtován jako S10. To bude platit, dokud na spravovaném disku nevoláte `revoke-access`, což je potřeba k připojení disku k virtuálnímu počítači.

Před vytvořením prázdného standardního pevného disku pro nahrávání budete potřebovat velikost souboru v bajtech pro virtuální pevný disk, který chcete nahrát. Ukázkový kód vám poskytne za vás, ale k tomu můžete použít: `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`. Tato hodnota se používá při zadání parametru **-UploadSizeInBytes** .

Teď v místním prostředí vytvořte prázdný standardní pevný disk pro nahrávání zadáním nastavení **nahrávání** v parametru **-CreateOption** a také parametru **-UploadSizeInBytes** v rutině [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) . Pak zavolejte [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0) k vytvoření disku:

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location 'West US' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName' -Disk $diskconfig
```

Pokud chcete nahrát disk SSD úrovně Premium nebo standardní SSD, nahraďte **Standard_LRS** buď **Premium_LRS** , nebo **StandardSSD_LRS**. SSD úrovně Ultra ještě není podporovaný.

Nyní jste vytvořili prázdný spravovaný disk, který je nakonfigurován pro proces nahrávání. K nahrání virtuálního pevného disku na disk budete potřebovat SAS s možností zápisu, abyste na něj mohli odkazovat jako na cíl pro nahrání.

K vygenerování zapisovatelného SAS SAS svého prázdného spravovaného disku použijte tento příkaz:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName 'myResouceGroup' -DiskName 'myDiskName' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="upload-vhd"></a>Nahrát VHD

Teď, když máte na svém prázdném spravovaném disku SAS, můžete ho použít k nastavení spravovaného disku jako cíle pro váš příkaz pro nahrání.

Pomocí AzCopy v10 za účelem nahrajte místní soubor VHD na spravovaný disk zadáním identifikátoru URI SAS, který jste vygenerovali.

Toto nahrávání má stejnou propustnost jako ekvivalentní [standardní pevný disk](disks-types.md#standard-hdd). Například pokud máte velikost, která je rovna S4, budete mít propustnost až 60 MiB/s. Pokud ale máte velikost, která je rovna S70, budete mít propustnost až 500 MiB/s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas --blob-type PageBlob
```

Pokud vaše SAS vyprší během nahrávání a ještě jste nevolali `revoke-access`, můžete získat nové SAS, abyste mohli pokračovat v nahrávání pomocí `grant-access`.

Po dokončení nahrávání a už nebudete muset na disk zapisovat další data, Odvolejte SAS. Odvoláním SAS dojde ke změně stavu spravovaného disku a budete moci připojit disk k virtuálnímu počítači.

```powershell
Revoke-AzDiskAccess -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="copy-a-managed-disk"></a>Kopírování spravovaného disku

Přímé nahrávání také zjednodušuje proces kopírování spravovaného disku. Můžete buď zkopírovat ve stejné oblasti, nebo mezi oblastí (do jiné oblasti).

Následující skript to provede za vás, což je postup podobný dříve popsaným krokům, a to s některými rozdíly, protože pracujete s existujícím diskem.

> [!IMPORTANT]
> Pokud zadáváte velikost disku v bajtech spravovaného disku z Azure, musíte přidat posun 512. Je to proto, že Azure při vracení velikosti disku vynechá zápatí. Pokud to neuděláte, kopie se nezdaří. Následující skript to pro vás už dělá.

Nahraďte `<sourceResourceGroupHere>`, `<sourceDiskNameHere>` `<targetDiskNameHere>`, `<targetResourceGroupHere>`, `<yourOSTypeHere>` a `<yourTargetLocationHere>` (jako příklad hodnoty Location by se uswest2) hodnotami, a potom spuštěním následujícího skriptu zkopírujte spravovaný disk.

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

Teď, když jste úspěšně nahráli VHD na spravovaný disk, můžete disk připojit k virtuálnímu počítači a začít ho používat.

Informace o tom, jak připojit disk k virtuálnímu počítači, najdete v našem článku na předmětu: [připojení datového disku k virtuálnímu počítači s Windows pomocí PowerShellu](attach-disk-ps.md).
