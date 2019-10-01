---
title: Nahrání virtuálního pevného disku do Azure pomocí Azure CLI
description: Přečtěte si, jak nahrát VHD na spravovaný disk Azure pomocí Azure CLI.
services: virtual-machines-linux,storage
author: roygara
ms.author: rogarana
ms.date: 09/20/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 938f1696c95f8feb9aeebd28139870e3ce020613
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695453"
---
# <a name="upload-a-vhd-to-azure-using-azure-cli"></a>Nahrání virtuálního pevného disku do Azure pomocí Azure CLI

Tento článek vysvětluje, jak nahrát virtuální pevný disk z místního počítače do spravovaného disku Azure. Dříve museli byste postupovat podle dalšího Zahrnutého procesu, který zahrnuje přípravu vašich dat v účtu úložiště a správu tohoto účtu úložiště. Nyní už nebudete muset spravovat účet úložiště nebo data fáze v něm, abyste nahráli VHD. Místo toho vytvoříte prázdný spravovaný disk a nahrajete do něj VHD přímo. Tato možnost zjednodušuje nahrávání místních virtuálních počítačů do Azure a umožňuje odeslat virtuální pevný disk až 32 TiB přímo do velkého spravovaného disku.

Pokud poskytujete řešení zálohování pro virtuální počítače s IaaS v Azure, doporučujeme použít přímé nahrávání pro obnovení záloh zákazníka na spravované disky. Pokud nahráváte virtuální pevný disk z externího počítače mimo Azure, rychlosti závisí na vaší místní šířce pásma. Pokud používáte virtuální počítač Azure, Šířka pásma bude stejná jako u HDD Standard.

V současné době se podporuje přímé nahrávání pro disky Standard HDD, Standard SSD a Premium SSD. Pro ultra SSD se ještě nepodporuje.

## <a name="prerequisites"></a>Předpoklady

- Stáhněte si nejnovější [verzi nástroje AzCopy v10 za účelem](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Nainstalujte rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).
- Soubor VHD uložený místně

## <a name="create-an-empty-managed-disk"></a>Vytvoření prázdného spravovaného disku

Pokud chcete nahrát virtuální pevný disk do Azure, budete muset vytvořit prázdný spravovaný disk, který je nakonfigurovaný pro tento proces nahrávání. Před tím, než ho vytvoříte, se dozvíte o některých dalších informacích, které byste měli znát na těchto discích.

Tento druh spravovaného disku má dva jedinečné stavy:

- ReadToUpload, což znamená, že disk je připravený k přijetí nahrávání, ale nevytvořil se žádný [podpis zabezpečeného přístupu](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS).
- ActiveUpload, což znamená, že disk je připravený k přijetí nahrávání a vygeneroval se SAS.

V některém z těchto stavů se spravovaný disk bude účtovat podle [standardních cen HDD](https://azure.microsoft.com/pricing/details/managed-disks/), bez ohledu na skutečný typ disku. Například P10 bude účtován jako S10. To bude platit, dokud na spravovaném disku nevoláte `revoke-access`, což je potřeba k připojení disku k virtuálnímu počítači.

Než budete moct vytvořit prázdný standardní pevný disk pro nahrávání, budete muset mít velikost souboru virtuálního pevného disku, který chcete nahrát (v bajtech). To můžete provést buď pomocí `wc -c <yourFileName>.vhd`, nebo `ls -al <yourFileName>.vhd`. Tato hodnota se používá při zadání parametru **--Upload-Size-bytes** .

Vytvořte prázdný standardní pevný disk pro nahrávání zadáním parametru **--for-upload** a parametru **--Upload-Size-bytes** v rutině [Create disku](/cli/azure/disk#az-disk-create) :

```azurecli-interactive
az disk create -n mydiskname -g resourcegroupname -l westus2 --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Pokud chcete nahrát disk SSD úrovně Premium nebo standardní SSD, nahraďte **standard_lrs** buď **premium_LRS** , nebo **standardssd_lrs**. SSD úrovně Ultra ještě není podporovaný.

Nyní jste vytvořili prázdný spravovaný disk, který je nakonfigurován pro proces nahrávání. K nahrání virtuálního pevného disku na disk budete potřebovat SAS s možností zápisu, abyste na něj mohli odkazovat jako na cíl pro nahrání.

K vygenerování zapisovatelného SAS SAS svého prázdného spravovaného disku použijte tento příkaz:

```azurecli-interactive
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

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Pokud vaše SAS vyprší během nahrávání a ještě jste nevolali `revoke-access`, můžete získat nové SAS, abyste mohli pokračovat v nahrávání pomocí `grant-access`.

Po dokončení nahrávání a už nebudete muset na disk zapisovat další data, Odvolejte SAS. Odvoláním SAS dojde ke změně stavu spravovaného disku a budete moci připojit disk k virtuálnímu počítači.

```azurecli-interactive
az disk revoke-access -n mydiskname -g resourcegroupname
```

## <a name="next-steps"></a>Další kroky

Teď, když jste úspěšně nahráli VHD na spravovaný disk, můžete disk připojit k virtuálnímu počítači a začít ho používat.

Informace o tom, jak připojit disk k virtuálnímu počítači, najdete v našem článku na předmětu: [Přidání disku do virtuálního počítače se systémem Linux](add-disk.md).
