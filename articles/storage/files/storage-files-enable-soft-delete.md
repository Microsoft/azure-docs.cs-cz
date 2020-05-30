---
title: Povolit obnovitelné odstranění – sdílené složky Azure
description: Naučte se, jak povolit obnovitelné odstranění (Preview) ve sdílených složkách Azure pro obnovení dat a zabránění nechtěnému odstranění.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 582a3603ad80ec1312429ed7cbd140d4310fadcb
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2020
ms.locfileid: "84196199"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Povolit obnovitelné odstranění u sdílených složek Azure

Azure Storage nabízí obnovitelné odstranění sdílených složek (Preview), abyste mohli snadněji obnovit data v případě, že je omylem odstranila aplikace nebo jiný uživatel účtu úložiště. Další informace o obnovitelném odstranění najdete v tématu [Jak zabránit nechtěnému odstranění sdílených složek Azure](storage-files-prevent-file-share-deletion.md).

V následujících částech se dozvíte, jak povolit a použít obnovitelné odstranění pro sdílené složky Azure v existujícím účtu úložiště:

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Přejděte do svého účtu úložiště a v části **Souborová služba**vyberte možnost **obnovitelné odstranění** .
1. Pro **částečný DELETE pro sdílení souborů**vyberte **povoleno** .
1. Vyberte možnost **Doba uchování sdílené složky ve dnech** a zadejte číslo, které si zvolíte.
1. Vyberte **Save (Uložit** ) a potvrďte nastavení uchovávání dat.

:::image type="content" source="media/storage-how-to-recover-deleted-account/enable-soft-delete-files.png" alt-text="Snímek obrazovky s podoknem nastavení obnovitelného odstranění účtu úložiště Zvýrazněte oddíl sdílené složky, povolte přepínač, nastavte dobu uchování a uložte. Tím se povolí obnovitelné odstranění pro všechny sdílené složky v účtu úložiště.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Chcete-li obnovit tichou odstraněnou sdílenou složku, použijte následující příkaz:

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>Zakázat obnovitelné odstranění

Pokud chcete ukončit použití obnovitelného odstranění nebo trvale odstranit sdílenou složku, postupujte podle těchto pokynů:

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Přejděte do svého účtu úložiště a v části **Nastavení**vyberte možnost **obnovitelné odstranění** .
1. V části **sdílené složky** vyberte **zakázáno** pro **obnovitelné odstranění sdílených složek**.
1. Vyberte **Save (Uložit** ) a potvrďte nastavení uchovávání dat.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/disable-soft-delete-files.png" alt-text="Vypnutí obnovitelného odstranění vám umožní okamžitě a trvale odstranit všechny sdílené složky v účtu úložiště ve vašem volném čase.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pomocí následujícího příkazu můžete v účtu úložiště zakázat obnovitelné odstranění:

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>Další kroky

Další informace o jiné formě ochrany a obnovení dat najdete v našem článku [Přehled snímků sdílených složek pro soubory Azure](storage-snapshots-files.md).
