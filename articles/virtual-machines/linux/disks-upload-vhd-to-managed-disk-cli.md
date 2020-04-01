---
title: Nahrání virtuálního pevného disku do Azure nebo zkopírování disku napříč oblastmi – Azure CLI
description: Zjistěte, jak nahrát virtuální pevný disk na spravovaný disk Azure a zkopírovat spravovaný disk napříč oblastmi pomocí rozhraní příkazového příkazového příkazu Azure prostřednictvím přímého nahrávání.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 03/27/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 6813206aebe67e4e6d2afd0d9c78d03f0c20c952
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420975"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-cli"></a>Nahrání virtuálního pevného disku do Azure nebo zkopírování spravovaného disku do jiné oblasti – Azure CLI

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Požadavky

- Stáhněte si nejnovější [verzi AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Nainstalujte příkazové příkazové nebo tonové zaokrocené.](/cli/azure/install-azure-cli)
- Pokud máte v úmyslu nahrát virtuální pevný disk z místního prostředí: Pevný virtuální disk velikosti, který [byl připraven pro Azure](../windows/prepare-for-upload-vhd-image.md), uložený místně.
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

Než budete moci vytvořit prázdný standardní pevný disk pro nahrávání, budete potřebovat velikost souboru virtuálního pevného disku, který chcete nahrát, v bajtech. Chcete-li získat, můžete `wc -c <yourFileName>.vhd` `ls -al <yourFileName>.vhd`použít buď nebo . Tato hodnota se používá při zadávání **parametru --upload-size-bytes.**

Vytvořte prázdný standardní pevný disk pro nahrávání zadáním parametru **-–for-upload** a parametru **--upload-size-bytes** na [disku vytvořit](/cli/azure/disk#az-disk-create) rutinu:

Nahraďte `<yourdiskname>`, `<yourresourcegroupname>`, `<yourregion>` s hodnotami podle vašeho výběru. Parametr `--upload-size-bytes` obsahuje příkladnou `34359738880`hodnotu aplikace , nahraďte ji hodnotou, která je pro vás vhodná.

```azurecli
az disk create -n <yourdiskname> -g <yourresourcegroupname> -l <yourregion> --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Pokud chcete nahrát buď prémiový SSD nebo standardní SSD, nahraďte **standard_lrs** **buď premium_LRS** nebo **standardssd_lrs**. Ultra disky nejsou prozatím podporovány.

Teď, když jste vytvořili prázdný spravovaný disk, který je nakonfigurovaný pro proces nahrávání, můžete do něj nahrát virtuální pevný disk. Chcete-li na disk nahrát virtuální pevný disk, budete potřebovat zapisovatelný SAS, abyste na něj mohli odkazovat jako na cíl pro nahrávání.

Chcete-li vygenerovat zapisovatelné SAS prázdného spravovaného disku, nahraďte `<yourdiskname>`a `<yourresourcegroupname>`použijte následující příkaz:

```azurecli
az disk grant-access -n <yourdiskname> -g <yourresourcegroupname> --access-level Write --duration-in-seconds 86400
```

Vrácená hodnota vzorku:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-a-vhd"></a>Nahrání virtuálního pevného disku

Teď, když máte SAS pro prázdný spravovaný disk, můžete ho použít k nastavení spravovaného disku jako cíle pro příkaz pro nahrávání.

Pomocí aplikace AzCopy v10 můžete nahrát místní soubor VHD na spravovaný disk zadáním vygenerovaného identifikátoru URI SAS.

Toto nahrání má stejnou propustnost jako ekvivalentní [standardní pevný disk](disks-types.md#standard-hdd). Například pokud máte velikost, která se rovná S4, budete mít propustnost až 60 MiB/s. Ale pokud máte velikost, která se rovná S70, budete mít propustnost až 500 MiB / s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Po dokončení nahrávání a již není nutné zapisovat žádná další data na disk, odvolat SAS. Zrušením SAS změníte stav spravovaného disku a umožníte připojit disk k virtuálnímu počítače.

Nahraďte `<yourdiskname>`disk a `<yourresourcegroupname>`potom použijte následující příkaz, aby byl disk použitelný:

```azurecli
az disk revoke-access -n <yourdiskname> -g <yourresourcegroupname>
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

Teď, když jste úspěšně nahráli virtuální pevný disk na spravovaný disk, můžete připojit disk jako [datový disk k existujícímu virtuálnímu počítače](add-disk.md) nebo připojit disk k [virtuálnímu virtuálnímu počítače jako disk operačního systému](upload-vhd.md#create-the-vm)a vytvořit tak nový virtuální virtuální počítače. 

