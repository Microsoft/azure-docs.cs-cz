---
title: Nahrání virtuálního pevného disku pomocí Azure CLI
description: Přečtěte si, jak nahrát VHD na spravovaný disk Azure a zkopírovat spravovaný disk do různých oblastí pomocí Azure CLI přes přímé nahrání.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 09/20/2019
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 2a5bfec08546d6cf00b1e04017b3879db8f016ee
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970343"
---
# <a name="upload-a-vhd-to-azure-using-azure-cli"></a>Nahrání virtuálního pevného disku do Azure pomocí Azure CLI

Tento článek vysvětluje, jak nahrát virtuální pevný disk z místního počítače do spravovaného disku Azure. Dříve museli byste postupovat podle dalšího Zahrnutého procesu, který zahrnuje přípravu vašich dat v účtu úložiště a správu tohoto účtu úložiště. Nyní už nebudete muset spravovat účet úložiště nebo data fáze v něm, abyste nahráli VHD. Místo toho vytvoříte prázdný spravovaný disk a nahrajete do něj VHD přímo. Tato možnost zjednodušuje nahrávání místních virtuálních počítačů do Azure a umožňuje odeslat virtuální pevný disk až 32 TiB přímo do velkého spravovaného disku.

Pokud poskytujete řešení zálohování pro virtuální počítače s IaaS v Azure, doporučujeme použít přímé nahrávání pro obnovení záloh zákazníka na spravované disky. Pokud nahráváte virtuální pevný disk z počítače, který je externí pro Azure, bude rychlost záviset na vaší místní šířce pásma. Pokud používáte virtuální počítač Azure, Šířka pásma bude stejná jako u HDD Standard.

V současné době se podporuje přímé nahrávání pro disky Standard HDD, Standard SSD a Premium SSD. Pro ultra SSD se ještě nepodporuje.

## <a name="prerequisites"></a>Předpoklady

- Stáhněte si nejnovější [verzi nástroje AzCopy v10 za účelem](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Nainstalujte rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).
- Soubor VHD uložený místně
- Pokud máte v úmyslu nahrát VHD z místního prostředí: virtuální pevný disk [připravený pro Azure](../windows/prepare-for-upload-vhd-image.md), uložený místně.
- Nebo spravovaný disk v Azure, pokud máte v úmyslu provést akci kopírování.

## <a name="create-an-empty-managed-disk"></a>Vytvoření prázdného spravovaného disku

Pokud chcete nahrát virtuální pevný disk do Azure, budete muset vytvořit prázdný spravovaný disk, který je nakonfigurovaný pro tento proces nahrávání. Před tím, než ho vytvoříte, se dozvíte o některých dalších informacích, které byste měli znát na těchto discích.

Tento druh spravovaného disku má dva jedinečné stavy:

- ReadToUpload, což znamená, že disk je připravený k přijetí nahrávání, ale nevytvořil se žádný [podpis zabezpečeného přístupu](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS).
- ActiveUpload, což znamená, že disk je připravený k přijetí nahrávání a vygeneroval se SAS.

V některém z těchto stavů se spravovaný disk bude účtovat podle [standardních cen HDD](https://azure.microsoft.com/pricing/details/managed-disks/), bez ohledu na skutečný typ disku. Například P10 bude účtován jako S10. To bude platit, dokud `revoke-access` nebudete volat na spravovaném disku, který je potřeba k připojení disku k virtuálnímu počítači.

Než budete moct vytvořit prázdný standardní pevný disk pro nahrávání, budete muset mít velikost souboru virtuálního pevného disku, který chcete nahrát (v bajtech). K získání toho můžete použít buď `wc -c <yourFileName>.vhd`, nebo `ls -al <yourFileName>.vhd`. Tato hodnota se používá při zadání parametru **--Upload-Size-bytes** .

Vytvořte prázdný standardní pevný disk pro nahrávání zadáním parametru **--for-upload** a parametru **--Upload-Size-bytes** v rutině [Create disku](/cli/azure/disk#az-disk-create) :

```bash
az disk create -n mydiskname -g resourcegroupname -l westus2 --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Pokud chcete nahrát disk SSD úrovně Premium nebo standardní SSD, nahraďte **standard_lrs** buď pomocí **premium_LRS** nebo **standardssd_lrs**. SSD úrovně Ultra ještě není podporovaný.

Nyní jste vytvořili prázdný spravovaný disk, který je nakonfigurován pro proces nahrávání. K nahrání virtuálního pevného disku na disk budete potřebovat SAS s možností zápisu, abyste na něj mohli odkazovat jako na cíl pro nahrání.

K vygenerování zapisovatelného SAS SAS svého prázdného spravovaného disku použijte tento příkaz:

```bash
az disk grant-access -n mydiskname -g resourcegroupname --access-level Write --duration-in-seconds 86400
```

Ukázka vracené hodnoty:

```
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-vhd"></a>Nahrát VHD

Teď, když máte na svém prázdném spravovaném disku SAS, můžete ho použít k nastavení spravovaného disku jako cíle pro váš příkaz pro nahrání.

Pomocí AzCopy v10 za účelem nahrajte místní soubor VHD na spravovaný disk zadáním identifikátoru URI SAS, který jste vygenerovali.

Toto nahrávání má stejnou propustnost jako ekvivalentní [standardní pevný disk](disks-types.md#standard-hdd). Například pokud máte velikost, která je rovna S4, budete mít propustnost až 60 MiB/s. Pokud ale máte velikost, která je rovna S70, budete mít propustnost až 500 MiB/s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Pokud vaše SAS vyprší během nahrávání a zatím jste se nevolali `revoke-access`, můžete získat nové SAS, abyste mohli pokračovat v nahrávání pomocí `grant-access`.

Po dokončení nahrávání a už nebudete muset na disk zapisovat další data, Odvolejte SAS. Odvoláním SAS dojde ke změně stavu spravovaného disku a budete moci připojit disk k virtuálnímu počítači.

```bash
az disk revoke-access -n mydiskname -g resourcegroupname
```

## <a name="copy-a-managed-disk"></a>Kopírování spravovaného disku

Přímé nahrávání také zjednodušuje proces kopírování spravovaného disku. Můžete buď zkopírovat ve stejné oblasti, nebo mezi oblastí (do jiné oblasti).

Následující skript to provede za vás, což je postup podobný dříve popsaným krokům, a to s některými rozdíly, protože pracujete s existujícím diskem.

> [!IMPORTANT]
> Pokud zadáváte velikost disku v bajtech spravovaného disku z Azure, musíte přidat posun 512. Je to proto, že Azure při vracení velikosti disku vynechá zápatí. Pokud to neuděláte, kopie se nezdaří. Následující skript to pro vás už dělá.

Nahraďte `<sourceResourceGroupHere>`, `<sourceDiskNameHere>`, `<targetDiskNameHere>`, `<targetResourceGroupHere>`a `<yourTargetLocationHere>` (příkladem hodnoty Location by uswest2) hodnotami a spuštěním následujícího skriptu zkopírujte spravovaný disk.

```bash
sourceDiskName = <sourceDiskNameHere>
sourceRG = <sourceResourceGroupHere>
targetDiskName = <targetDiskNameHere>
targetRG = <targetResourceGroupHere>
targetLocale = <yourTargetLocationHere>

sourceDiskSizeBytes= $(az disk show -g $sourceRG -n $sourceDiskName --query '[uniqueId]' -o tsv)

az disk create -g $targetRG -n $targetDiskName -l $targetLocale --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI = $(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n $sourceDiskName -g $sourceRG --duration-in-seconds 86400 --query [accessSas] -o tsv)

.\azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>Další kroky

Teď, když jste úspěšně nahráli VHD na spravovaný disk, můžete disk připojit jako [datový disk k existujícímu virtuálnímu počítači](add-disk.md) nebo [připojit disk k virtuálnímu počítači jako disk s operačním systémem](upload-vhd.md#create-the-vm)a vytvořit nový virtuální počítač. 

