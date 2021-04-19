---
title: Povolit obnovitelné odstranění – sdílené složky Azure
description: Naučte se, jak ve sdílených složkách Azure povolit obnovitelné odstranění pro obnovení dat a zabránit nechtěnému odstranění.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/05/2021
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: a0dff310ce4a40b7a66cc548f3c77213f4a10e00
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107716995"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Povolit obnovitelné odstranění u sdílených složek Azure

Azure Storage nabízí obnovitelné odstranění sdílených složek, abyste mohli snadněji obnovit data v případě, že je omylem odstranila aplikace nebo jiný uživatel účtu úložiště. Další informace o obnovitelném odstranění najdete v tématu [Jak zabránit nechtěnému odstranění sdílených složek Azure](storage-files-prevent-file-share-deletion.md).

V následujících částech se dozvíte, jak povolit a použít obnovitelné odstranění pro sdílené složky Azure v existujícím účtu úložiště:

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

## <a name="getting-started"></a>Začínáme

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Přejděte do svého účtu úložiště a v části **úložiště dat** vyberte **sdílené složky** .
1. Vyberte **povoleno** u pole **obnovitelné odstranění**.
1. **Pro všechny sdílené složky** vyberte možnost **povoleno** pro obnovitelné odstranění.
1. Vyberte možnost **Doba uchování sdílené složky ve dnech** a zadejte číslo, které si zvolíte.
1. Vyberte **Save (Uložit** ) a potvrďte nastavení uchovávání dat.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/files-enable-soft-delete-new-ui.png" alt-text="Snímek obrazovky s podoknem nastavení obnovitelného odstranění účtu úložiště Zvýrazníte část obnovitelného odstranění sdílených složek, povolíte přepínač, nastavíte dobu uchování a uložíte. Tím se povolí obnovitelné odstranění pro všechny sdílené složky v účtu úložiště.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Rutiny obnovitelného odstranění jsou k dispozici ve verzi 2.1.3 a novějším [modulu CLI Azure](/cli/azure/install-azure-cli).

## <a name="getting-started-with-cli"></a>Začínáme s rozhraním příkazového řádku

Chcete-li povolit obnovitelné odstranění, je nutné aktualizovat vlastnosti služby klienta souboru. Následující příklad povoluje obnovitelné odstranění pro všechny sdílené složky v účtu úložiště:

```azurecli
az storage account file-service-properties update --enable-delete-retention true -n yourStorageaccount -g yourResourceGroup
```

Můžete ověřit, jestli je povolené obnovitelné odstranění, a zobrazit jeho zásady uchovávání informací pomocí následujícího příkazu:

```azurecli
az storage account file-service-properties show -n yourStorageaccount -g yourResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="prerequisite"></a>Požadavek

Rutiny obnovitelného odstranění jsou k dispozici v 4.8.0 a novějších verzích modulu AZ. Storage. 

## <a name="getting-started-with-powershell"></a>Začínáme s PowerShellem

Chcete-li povolit obnovitelné odstranění, je nutné aktualizovat vlastnosti služby klienta souboru. Následující příklad povoluje obnovitelné odstranění pro všechny sdílené složky v účtu úložiště:

```azurepowershell-interactive
$rgName = "yourResourceGroupName"
$accountName = "yourStorageAccountName"

Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $true -ShareRetentionDays 7
```

Můžete ověřit, jestli je povolené obnovitelné odstranění, a zobrazit jeho zásady uchovávání informací pomocí následujícího příkazu:

```azurepowershell-interactive
Get-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName
```
---

## <a name="restore-soft-deleted-file-share"></a>Obnovit dočasná odstraněnou sdílenou složku

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Postup obnovení dočasné odstraněné složky pro sdílení souborů:

1. Přejděte do svého účtu úložiště a vyberte **sdílené složky**.
1. V okně sdílená složka povolte možnost **Zobrazit odstraněné sdílené složky** a zobrazte všechny odstraněné sdílené složky.

    Zobrazí se všechny sdílené složky, které jsou aktuálně ve stavu **odstraněno** .

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="Pokud je sloupec status (stav) sloupce vedle sloupce název nastavený na hodnotu odstraněno, bude vaše sdílená složka v nepodmíněném odstraněném stavu. A po uplynutí zadané doby uchování se trvale odstraní.":::

1. Vyberte sdílenou složku a vyberte možnost zrušit **odstranění**. Tato akce obnoví sdílenou složku.

    Můžete potvrdit, že se sdílená složka obnoví od jejího přepnutí stavu na **aktivní**.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="Pokud je sloupec status (stav) sloupce vedle sloupce název nastavený na aktivní, vaše sdílená složka se obnovila.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Rutiny obnovitelného odstranění jsou k dispozici ve verzi 2.1.3 rozhraní příkazového řádku Azure CLI. Chcete-li obnovit odstraněnou sdílenou složku, je nutné nejprve získat `--deleted-version` hodnotu sdílené složky. Pokud chcete získat tuto hodnotu, použijte následující příkaz, který vypíše všechny odstraněné sdílené složky pro váš účet úložiště:

```azurecli
az storage share-rm list --storage-account yourStorageaccount --include-deleted
```

Jakmile identifikujete sdílenou složku, kterou chcete obnovit, můžete ji použít s následujícím příkazem k jejímu obnovení:

```azurecli
az storage share-rm restore -n deletedshare --deleted-version 01D64EB9886F00C4 -g yourResourceGroup --storage-account yourStorageaccount
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Rutiny obnovitelného odstranění jsou k dispozici v 4.8.0 a novějších verzích modulu AZ. Storage. Chcete-li obnovit odstraněnou sdílenou složku, je nutné nejprve získat `-DeletedShareVersion` hodnotu sdílené složky. Pokud chcete získat tuto hodnotu, použijte následující příkaz, který vypíše všechny odstraněné sdílené složky pro váš účet úložiště:

```azurepowershell-interactive
Get-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -IncludeDeleted
```

Jakmile identifikujete sdílenou složku, kterou chcete obnovit, můžete ji použít s následujícím příkazem k jejímu obnovení:

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>Zakázat obnovitelné odstranění

Pokud chcete ukončit použití obnovitelného odstranění, postupujte podle těchto pokynů. Chcete-li trvale odstranit sdílenou složku, která byla odstraněna, je nutné ji zrušit, zakázat obnovitelné odstranění a pak ji znovu odstranit. 

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Přejděte do svého účtu úložiště a v části **úložiště dat** vyberte **sdílené složky** .
1. Vyberte odkaz vedle pole **obnovitelné odstranění**.
1. Vyberte možnost **zakázáno** pro **obnovitelné odstranění pro všechny sdílené složky**.
1. Vyberte **Save (Uložit** ) a potvrďte nastavení uchovávání dat.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/files-disable-soft-delete.png" alt-text="Vypnutí obnovitelného odstranění vám umožní okamžitě a trvale odstranit všechny sdílené složky v účtu úložiště ve vašem volném čase.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Rutiny obnovitelného odstranění jsou k dispozici ve verzi 2.1.3 rozhraní příkazového řádku Azure CLI. Pomocí následujícího příkazu můžete v účtu úložiště zakázat obnovitelné odstranění:

```azurecli
az storage account file-service-properties update --enable-delete-retention false -n yourStorageaccount -g yourResourceGroup
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Rutiny obnovitelného odstranění jsou k dispozici v 4.8.0 a novějších verzích modulu AZ. Storage. Pomocí následujícího příkazu můžete v účtu úložiště zakázat obnovitelné odstranění:

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>Další kroky

Další informace o jiné formě ochrany a obnovení dat najdete v našem článku [Přehled snímků sdílených složek pro soubory Azure](storage-snapshots-files.md).