---
title: Nahrání virtuálního pevného disku do Azure nebo kopírování disku do různých oblastí – Azure PowerShell
description: Přečtěte si, jak nahrát VHD na spravovaný disk Azure a zkopírovat spravovaný disk do různých oblastí pomocí Azure PowerShell přes přímé nahrávání.
author: roygara
ms.author: rogarana
ms.date: 06/15/2020
ms.topic: how-to
ms.service: virtual-machines
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 675e99797a507cdcf96ad33ab13c4f386f6f372c
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169024"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-powershell"></a>Nahrání virtuálního pevného disku do Azure nebo zkopírování spravovaného disku do jiné oblasti – Azure PowerShell

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Požadavky

- Stáhněte si nejnovější [verzi nástroje AzCopy v10 za účelem](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Nainstalujte modul Azure PowerShell](/powershell/azure/install-Az-ps).
- Pokud máte v úmyslu nahrát VHD z místního prostředí: virtuální pevný disk s pevnou velikostí, který [je připravený pro Azure](prepare-for-upload-vhd-image.md), je uložený místně.
- Nebo spravovaný disk v Azure, pokud máte v úmyslu provést akci kopírování.

## <a name="getting-started"></a>Začínáme

Pokud byste chtěli nahrávat disky prostřednictvím grafického uživatelského rozhraní, můžete to udělat pomocí Průzkumník služby Azure Storage. Podrobnosti najdete v tématu: [použití Průzkumník služby Azure Storage ke správě služby Azure Managed disks](../disks-use-storage-explorer-managed-disks.md) .

Pokud chcete nahrát virtuální pevný disk do Azure, budete muset vytvořit prázdný spravovaný disk, který je nakonfigurovaný pro tento proces nahrávání. Před tím, než ho vytvoříte, se dozvíte o některých dalších informacích, které byste měli znát na těchto discích.

Tento druh spravovaného disku má dva jedinečné stavy:

- ReadyToUpload, což znamená, že disk je připravený k přijetí nahrávání, ale nevytvořil se žádný [podpis zabezpečeného přístupu](../../storage/common/storage-sas-overview.md) (SAS).
- ActiveUpload, což znamená, že disk je připravený k přijetí nahrávání a vygeneroval se SAS.

> [!NOTE]
> V některém z těchto stavů se spravovaný disk bude účtovat podle [standardních cen HDD](https://azure.microsoft.com/pricing/details/managed-disks/), bez ohledu na skutečný typ disku. Například P10 bude účtován jako S10. To bude platit až do chvíle `revoke-access` , kdy se zavolá na spravovaný disk, který je potřeba k připojení disku k virtuálnímu počítači.

## <a name="create-an-empty-managed-disk"></a>Vytvoření prázdného spravovaného disku

Než budete moct vytvořit prázdný standardní pevný disk pro nahrávání, budete potřebovat velikost souboru virtuálního pevného disku, který chcete nahrát (v bajtech). Ukázkový kód vám poskytne za vás, ale k tomu můžete použít: `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length` . Tato hodnota se používá při zadání parametru **-UploadSizeInBytes** .

Teď v místním prostředí vytvořte prázdný standardní pevný disk pro nahrávání zadáním nastavení **nahrávání** v parametru **-CreateOption** a také parametru **-UploadSizeInBytes** v rutině [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) . Pak zavolejte [New-AzDisk](/powershell/module/az.compute/new-azdisk) a vytvořte disk.

Nahraďte `<yourdiskname>` , `<yourresourcegroupname>` a `<yourregion>` poté spusťte následující příkazy:

> [!TIP]
> Pokud vytváříte disk s operačním systémem, přidejte `-HyperVGeneration '<yourGeneration>'` do `New-AzDiskConfig` .

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location '<yourregion>' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -Disk $diskconfig
```

Pokud chcete nahrát disk SSD úrovně Premium nebo standardní SSD, nahraďte **Standard_LRS** buď pomocí **Premium_LRS** nebo **StandardSSD_LRS**. Disky Ultra nejsou ještě podporovány.

Teď, když jste vytvořili prázdný spravovaný disk, který je nakonfigurovaný pro proces nahrávání, můžete do něj nahrát VHD. K nahrání virtuálního pevného disku na disk budete potřebovat SAS s možností zápisu, abyste na něj mohli odkazovat jako na cíl pro nahrání.

Pokud chcete vygenerovat zapisovatelný SAS pro váš prázdný spravovaný disk, nahraďte `<yourdiskname>` a a `<yourresourcegroupname>` pak použijte následující příkazy:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="upload-a-vhd"></a>Nahrání virtuálního pevného disku

Teď, když máte na svém prázdném spravovaném disku SAS, můžete ho použít k nastavení spravovaného disku jako cíle pro váš příkaz pro nahrání.

Pomocí AzCopy v10 za účelem nahrajte místní soubor VHD na spravovaný disk zadáním identifikátoru URI SAS, který jste vygenerovali.

Toto nahrávání má stejnou propustnost jako ekvivalentní [standardní pevný disk](../disks-types.md#standard-hdd). Například pokud máte velikost, která je rovna S4, budete mít propustnost až 60 MiB/s. Pokud ale máte velikost, která je rovna S70, budete mít propustnost až 500 MiB/s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

Po dokončení nahrávání a už nebudete muset na disk zapisovat další data, Odvolejte SAS. Odvoláním SAS dojde ke změně stavu spravovaného disku a budete moci připojit disk k virtuálnímu počítači.

Nahraďte `<yourdiskname>` a a `<yourresourcegroupname>` pak spusťte následující příkaz:

```powershell
Revoke-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="copy-a-managed-disk"></a>Kopírování spravovaného disku

Přímé nahrávání také zjednodušuje proces kopírování spravovaného disku. Můžete buď zkopírovat ve stejné oblasti, nebo zkopírovat spravovaný disk do jiné oblasti.

Následující skript to provede za vás, postup je podobný dříve popsaným krokům, a to kvůli nějakým rozdílům, protože pracujete s existujícím diskem.

> [!IMPORTANT]
> Pokud zadáváte velikost disku v bajtech spravovaného disku z Azure, musíte přidat posun 512. Je to proto, že Azure při vracení velikosti disku vynechá zápatí. Pokud to neuděláte, kopie se nezdaří. Následující skript to pro vás už dělá.

Nahraďte `<sourceResourceGroupHere>` `<sourceDiskNameHere>` hodnotu, `<targetDiskNameHere>` , `<targetResourceGroupHere>` , `<yourOSTypeHere>` a `<yourTargetLocationHere>` (příkladem hodnoty Location by se uswest2) hodnotami a spuštěním následujícího skriptu zkopírujte spravovaný disk.

> [!TIP]
> Pokud vytváříte disk s operačním systémem, přidejte-HyperVGeneration <yourGeneration> do `New-AzDiskConfig` .

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

Informace o tom, jak připojit datový disk k virtuálnímu počítači, najdete v našem článku na předmětu: [připojení datového disku k virtuálnímu počítači s Windows pomocí PowerShellu](attach-disk-ps.md). Postup použití disku jako disku s operačním systémem najdete v tématu [Vytvoření virtuálního počítače s Windows z specializovaného disku](create-vm-specialized.md#create-the-new-vm).
