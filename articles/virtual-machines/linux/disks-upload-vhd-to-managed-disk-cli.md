---
title: Nahrání virtuálního pevného disku do Azure nebo kopírování disku v různých oblastech – Azure CLI
description: Přečtěte si, jak nahrát VHD na spravovaný disk Azure a zkopírovat spravovaný disk do různých oblastí pomocí Azure CLI přes přímé nahrání.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 06/15/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 285f0acd5097ce68cddee6f732b17944dffb0eba
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762564"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-cli"></a>Nahrání virtuálního pevného disku do Azure nebo zkopírování spravovaného disku do jiné oblasti – Azure CLI

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Požadavky

- Stáhněte si nejnovější [verzi nástroje AzCopy v10 za účelem](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Nainstalujte rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).
- Pokud máte v úmyslu nahrát VHD z místního prostředí: virtuální pevný disk s pevnou velikostí, který [je připravený pro Azure](../windows/prepare-for-upload-vhd-image.md), je uložený místně.
- Nebo spravovaný disk v Azure, pokud máte v úmyslu provést akci kopírování.

## <a name="getting-started"></a>Začínáme

Pokud byste chtěli nahrávat disky prostřednictvím grafického uživatelského rozhraní, můžete to udělat pomocí Průzkumník služby Azure Storage. Podrobnosti najdete v tématu: [použití Průzkumník služby Azure Storage ke správě služby Azure Managed disks](../disks-use-storage-explorer-managed-disks.md) .

Pokud chcete nahrát virtuální pevný disk do Azure, budete muset vytvořit prázdný spravovaný disk, který je nakonfigurovaný pro tento proces nahrávání. Před tím, než ho vytvoříte, se dozvíte o některých dalších informacích, které byste měli znát na těchto discích.

Tento druh spravovaného disku má dva jedinečné stavy:

- ReadToUpload, což znamená, že disk je připravený k přijetí nahrávání, ale nevytvořil se žádný [podpis zabezpečeného přístupu](../../storage/common/storage-sas-overview.md) (SAS).
- ActiveUpload, což znamená, že disk je připravený k přijetí nahrávání a vygeneroval se SAS.

> [!NOTE]
> V některém z těchto stavů se spravovaný disk bude účtovat podle [standardních cen HDD](https://azure.microsoft.com/pricing/details/managed-disks/), bez ohledu na skutečný typ disku. Například P10 bude účtován jako S10. To bude platit až do chvíle `revoke-access` , kdy se zavolá na spravovaný disk, který je potřeba k připojení disku k virtuálnímu počítači.

## <a name="create-an-empty-managed-disk"></a>Vytvoření prázdného spravovaného disku

Než budete moct vytvořit prázdný standardní pevný disk pro nahrávání, budete potřebovat velikost souboru virtuálního pevného disku, který chcete nahrát (v bajtech). K získání toho můžete použít buď `wc -c <yourFileName>.vhd` nebo `ls -al <yourFileName>.vhd` . Tato hodnota se používá při zadání parametru **--Upload-Size-bytes** .

Vytvořte prázdný standardní pevný disk pro nahrávání zadáním parametru **--for-upload** a parametru **--Upload-Size-bytes** v rutině [Create disku](/cli/azure/disk#az_disk_create) :

Nahraďte `<yourdiskname>` `<yourresourcegroupname>` `<yourregion>` hodnotu hodnotou dle vašeho výběru. `--upload-size-bytes`Parametr obsahuje ukázkovou hodnotu `34359738880` , nahraďte ji hodnotou, která je pro vás vhodná.

> [!TIP]
> Pokud vytváříte disk s operačním systémem, přidejte do nástroje--Hyper-v-Generation <yourGeneration> `az disk create` .

```azurecli
az disk create -n <yourdiskname> -g <yourresourcegroupname> -l <yourregion> --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Pokud chcete nahrát disk SSD úrovně Premium nebo standardní SSD, nahraďte **standard_lrs** buď pomocí **premium_LRS** nebo **standardssd_lrs**. Disky Ultra nejsou aktuálně podporovány.

Teď, když jste vytvořili prázdný spravovaný disk, který je nakonfigurovaný pro proces nahrávání, můžete do něj nahrát VHD. K nahrání virtuálního pevného disku na disk budete potřebovat SAS s možností zápisu, abyste na něj mohli odkazovat jako na cíl pro nahrání.

Pokud chcete vygenerovat zapisovatelný SAS pro váš prázdný spravovaný disk, nahraďte `<yourdiskname>` a a `<yourresourcegroupname>` pak použijte následující příkaz:

```azurecli
az disk grant-access -n <yourdiskname> -g <yourresourcegroupname> --access-level Write --duration-in-seconds 86400
```

Ukázka vracené hodnoty:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-a-vhd"></a>Nahrání virtuálního pevného disku

Teď, když máte na svém prázdném spravovaném disku SAS, můžete ho použít k nastavení spravovaného disku jako cíle pro váš příkaz pro nahrání.

Pomocí AzCopy v10 za účelem nahrajte místní soubor VHD na spravovaný disk zadáním identifikátoru URI SAS, který jste vygenerovali.

Toto nahrávání má stejnou propustnost jako ekvivalentní [standardní pevný disk](../disks-types.md#standard-hdd). Například pokud máte velikost, která je rovna S4, budete mít propustnost až 60 MiB/s. Pokud ale máte velikost, která je rovna S70, budete mít propustnost až 500 MiB/s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Po dokončení nahrávání a už nebudete muset na disk zapisovat další data, Odvolejte SAS. Odvoláním SAS dojde ke změně stavu spravovaného disku a budete moci připojit disk k virtuálnímu počítači.

Nahraďte `<yourdiskname>` a `<yourresourcegroupname>` pak pomocí následujícího příkazu nastavte disk jako použitelný:

```azurecli
az disk revoke-access -n <yourdiskname> -g <yourresourcegroupname>
```

## <a name="copy-a-managed-disk"></a>Kopírování spravovaného disku

Přímé nahrávání také zjednodušuje proces kopírování spravovaného disku. Můžete buď zkopírovat ve stejné oblasti, nebo mezi oblastí (do jiné oblasti).

Následující skript to provede za vás, což je postup podobný dříve popsaným krokům, a to s některými rozdíly, protože pracujete s existujícím diskem.

> [!IMPORTANT]
> Pokud zadáváte velikost disku v bajtech spravovaného disku z Azure, musíte přidat posun 512. Je to proto, že Azure při vracení velikosti disku vynechá zápatí. Pokud to neuděláte, kopie se nezdaří. Následující skript to pro vás už dělá.

Nahraďte `<sourceResourceGroupHere>` `<sourceDiskNameHere>` hodnotu,, `<targetDiskNameHere>` , `<targetResourceGroupHere>` a `<yourTargetLocationHere>` (příkladem hodnoty Location by se uswest2) hodnotami a spuštěním následujícího skriptu zkopírujte spravovaný disk.

> [!TIP]
> Pokud vytváříte disk s operačním systémem, přidejte do nástroje--Hyper-v-Generation <yourGeneration> `az disk create` .

```azurecli
sourceDiskName=<sourceDiskNameHere>
sourceRG=<sourceResourceGroupHere>
targetDiskName=<targetDiskNameHere>
targetRG=<targetResourceGroupHere>
targetLocation=<yourTargetLocationHere>

sourceDiskSizeBytes=$(az disk show -g $sourceRG -n $sourceDiskName --query '[diskSizeBytes]' -o tsv)

az disk create -g $targetRG -n $targetDiskName -l $targetLocation --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI=$(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n $sourceDiskName -g $sourceRG --duration-in-seconds 86400 --query [accessSas] -o tsv)

.\azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>Další kroky

Teď, když jste úspěšně nahráli VHD na spravovaný disk, můžete disk připojit jako [datový disk k existujícímu virtuálnímu počítači](add-disk.md) nebo [připojit disk k virtuálnímu počítači jako disk s operačním systémem](upload-vhd.md#create-the-vm)a vytvořit nový virtuální počítač.