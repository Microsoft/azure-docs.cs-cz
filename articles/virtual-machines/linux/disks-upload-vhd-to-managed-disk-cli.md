---
title: Nahrání virtuálního pevného disku pomocí rozhraní příkazového příkazu Azure
description: Zjistěte, jak nahrát virtuální ho dhd na spravovaný disk Azure a zkopírovat spravovaný disk napříč oblastmi pomocí rozhraní příkazového příkazového příkazu Azure prostřednictvím přímého nahrávání.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 03/13/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: d89a4279d425e4b12e92aae81edfd6c1514c3eef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062666"
---
# <a name="upload-a-vhd-to-azure-using-azure-cli"></a>Nahrání virtuálního pevného disku do Azure pomocí rozhraní příkazového příkazu Azure

Tento článek vysvětluje, jak nahrát virtuální pevný disk z místního počítače na spravovaný disk Azure. Dříve jste museli sledovat složitější proces, který zahrnoval přípravu dat v účtu úložiště a správu tohoto účtu úložiště. Nyní již nemusíte spravovat účet úložiště nebo v něm nastavovat data, abyste mohli nahrát virtuální pevný disk. Místo toho vytvoříte prázdný spravovaný disk a nahrajete do něj virtuální disk přímo. To zjednodušuje nahrávání místních virtuálních počítačů do Azure a umožňuje nahrát virtuální pevný disk až 32 TiB přímo na velký spravovaný disk.

Pokud poskytujete řešení zálohování pro virtuální počítače IaaS v Azure, doporučujeme použít přímé nahrávání k obnovení záloh zákazníků na spravované disky. Pokud nahráváte virtuální pevný disk z externího počítače do Azure, rychlost bude záviset na místní šířce pásma. Pokud používáte virtuální počítač Azure, šířka pásma bude stejná jako u standardních pevných disků.

V současné době je přímé nahrávání podporováno pro standardní disky spravované pevným diskem, standardním SSD disky a prémiovými disky spravovanými ssd. To ještě není podporováno pro ultra SSD.

## <a name="prerequisites"></a>Požadavky

- Stáhněte si nejnovější [verzi AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Nainstalujte příkazové příkazové nebo tonové zaokrocené.](/cli/azure/install-azure-cli)
- Soubor vhd, uložený místně
- Pokud máte v úmyslu nahrát vhd z místního: Pevná velikost vhd, který [byl připraven pro Azure](../windows/prepare-for-upload-vhd-image.md), uložené místně.
- Nebo spravovaný disk v Azure, pokud máte v úmyslu provést akci kopírování.

## <a name="create-an-empty-managed-disk"></a>Vytvoření prázdného spravovaného disku

Chcete-li nahrát virtuální pevný disk do Azure, budete muset vytvořit prázdný spravovaný disk, který je nakonfigurovaný pro tento proces nahrávání. Před vytvořením, je tu nějaké další informace, které byste měli vědět o těchto disků.

Tento druh spravovaného disku má dva jedinečné stavy:

- ReadToUpload, což znamená, že disk je připraven k příjmu nahrát, ale nebyl vygenerován žádný [podpis zabezpečeného přístupu](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS).
- ActiveUpload, což znamená, že disk je připraven k přijetí nahrávání a SAS byla vygenerována.

Zatímco v jednom z těchto stavů bude spravovaný disk účtovány při [standardních cenách hdd](https://azure.microsoft.com/pricing/details/managed-disks/), bez ohledu na skutečný typ disku. Například P10 se bude účtovat jako S10. To bude platit, dokud `revoke-access` se volá na spravovaném disku, který je nutný k připojení disku k virtuálnímu počítači.

Než budete moci vytvořit prázdný standardní pevný disk pro nahrávání, budete muset mít velikost souboru vhd, který chcete nahrát, v bajtů. Chcete-li získat, můžete `wc -c <yourFileName>.vhd` `ls -al <yourFileName>.vhd`použít buď nebo . Tato hodnota se používá při zadávání **parametru --upload-size-bytes.**

Vytvořte prázdný standardní pevný disk pro nahrávání zadáním parametru **-–for-upload** a parametru **--upload-size-bytes** na [disku vytvořit](/cli/azure/disk#az-disk-create) rutinu:

```azurecli
az disk create -n mydiskname -g resourcegroupname -l westus2 --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Pokud chcete nahrát buď prémiový SSD nebo standardní SSD, nahraďte **standard_lrs** **buď premium_LRS** nebo **standardssd_lrs**. Ultra SSD ještě není podporováno.

Nyní jste vytvořili prázdný spravovaný disk, který je nakonfigurován pro proces nahrávání. Chcete-li na disk nahrát virtuální disk, budete potřebovat zapisovatelný SAS, abyste na něj mohli odkazovat jako na cíl pro nahrávání.

Chcete-li vygenerovat zapisovatelné SAS prázdného spravovaného disku, použijte následující příkaz:

```azurecli
az disk grant-access -n mydiskname -g resourcegroupname --access-level Write --duration-in-seconds 86400
```

Vrácená hodnota vzorku:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-vhd"></a>Nahrát vhd

Teď, když máte SAS pro prázdný spravovaný disk, můžete ho použít k nastavení spravovaného disku jako cíle pro příkaz pro nahrávání.

Pomocí aplikace AzCopy v10 můžete nahrát místní soubor VHD na spravovaný disk zadáním vygenerovaného identifikátoru URI SAS.

Toto nahrání má stejnou propustnost jako ekvivalentní [standardní pevný disk](disks-types.md#standard-hdd). Například pokud máte velikost, která se rovná S4, budete mít propustnost až 60 MiB/s. Ale pokud máte velikost, která se rovná S70, budete mít propustnost až 500 MiB / s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Po dokončení nahrávání a již není nutné zapisovat žádná další data na disk, odvolat SAS. Zrušením SAS změníte stav spravovaného disku a umožníte připojit disk k virtuálnímu počítače.

```azurecli
az disk revoke-access -n mydiskname -g resourcegroupname
```

## <a name="copy-a-managed-disk"></a>Kopírování spravovaného disku

Přímé nahrávání také zjednodušuje proces kopírování spravovaného disku. Můžete kopírovat v rámci stejné oblasti nebo mezi oblastmi (do jiné oblasti).

Postupujte skript to udělá za vás, proces je podobný krokům popsaným výše, s některými rozdíly, protože pracujete s existujícím diskem.

> [!IMPORTANT]
> Musíte přidat posun 512, když poskytujete velikost disku v bajtů spravovaného disku z Azure. Důvodem je, že Azure vynese zápatí při vrácení velikosti disku. Kopie se nezdaří, pokud tak neučiníte. Následující skript již dělá to pro vás.

Nahraďte `<sourceDiskNameHere>` `<targetDiskNameHere>`hodnoty `<targetResourceGroupHere>` `<sourceResourceGroupHere>`, `<yourTargetLocationHere>` , , , a (příklad hodnoty umístění uswest2) a spusťte následující skript, abyste zkopírovali spravovaný disk.

```azurecli
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

Teď, když jste úspěšně nahráli virtuální disk na spravovaný disk, můžete připojit disk jako [datový disk k existujícímu virtuálnímu počítače](add-disk.md) nebo připojit disk k [virtuálnímu virtuálnímu počítače jako disk operačního systému](upload-vhd.md#create-the-vm)a vytvořit tak nový virtuální virtuální počítače. 

