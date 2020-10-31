---
title: Povolit obnovitelné odstranění – sdílené složky Azure
description: Naučte se, jak ve sdílených složkách Azure povolit obnovitelné odstranění pro obnovení dat a zabránit nechtěnému odstranění.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/28/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 7defa8611080027a67a0d1db1daa4c4a9d44edfe
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93126137"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Povolit obnovitelné odstranění u sdílených složek Azure

Azure Storage nabízí obnovitelné odstranění sdílených složek, abyste mohli snadněji obnovit data v případě, že je omylem odstranila aplikace nebo jiný uživatel účtu úložiště. Další informace o obnovitelném odstranění najdete v tématu [Jak zabránit nechtěnému odstranění sdílených složek Azure](storage-files-prevent-file-share-deletion.md).

V následujících částech se dozvíte, jak povolit a použít obnovitelné odstranění pro sdílené složky Azure v existujícím účtu úložiště:

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

## <a name="getting-started"></a>Začínáme

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).
1. Přejděte do svého účtu úložiště a v části **Souborová služba** vyberte možnost **obnovitelné odstranění** .
1. Pro **částečný DELETE pro sdílení souborů** vyberte **povoleno** .
1. Vyberte možnost **Doba uchování sdílené složky ve dnech** a zadejte číslo, které si zvolíte.
1. Vyberte **Save (Uložit** ) a potvrďte nastavení uchovávání dat.

:::image type="content" source="media/storage-how-to-recover-deleted-account/enable-soft-delete-files.png" alt-text="Snímek obrazovky s podoknem nastavení obnovitelného odstranění účtu úložiště Zvýrazněte oddíl sdílené složky, povolte přepínač, nastavte dobu uchování a uložte. Tím se povolí obnovitelné odstranění pro všechny sdílené složky v účtu úložiště.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="prerequisite"></a>Požadavek

Rutiny obnovitelného odstranění jsou k dispozici ve verzi [3.0.0](https://www.powershellgallery.com/packages/Az.Storage/3.0.0) modulu AZ. Storage. 

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

1. Přejděte do svého účtu úložiště a vyberte **sdílené složky** .
1. V okně sdílená složka povolte možnost **Zobrazit odstraněné sdílené složky** a zobrazte všechny odstraněné sdílené složky.

    Zobrazí se všechny sdílené složky, které jsou aktuálně ve stavu **odstraněno** .

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="Snímek obrazovky s podoknem nastavení obnovitelného odstranění účtu úložiště Zvýrazněte oddíl sdílené složky, povolte přepínač, nastavte dobu uchování a uložte. Tím se povolí obnovitelné odstranění pro všechny sdílené složky v účtu úložiště.":::

1. Vyberte sdílenou složku a vyberte možnost zrušit **odstranění** . Tato akce obnoví sdílenou složku.

    Můžete potvrdit, že se sdílená složka obnoví od jejího přepnutí stavu na **aktivní** .

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="Snímek obrazovky s podoknem nastavení obnovitelného odstranění účtu úložiště Zvýrazněte oddíl sdílené složky, povolte přepínač, nastavte dobu uchování a uložte. Tím se povolí obnovitelné odstranění pro všechny sdílené složky v účtu úložiště.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Rutiny obnovitelného odstranění jsou k dispozici ve verzi 3.0.0 modulu AZ. Storage. Chcete-li obnovit tichou odstraněnou sdílenou složku, použijte následující příkaz:

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>Zakázat obnovitelné odstranění

Pokud chcete ukončit použití obnovitelného odstranění nebo trvale odstranit sdílenou složku, postupujte podle těchto pokynů:

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Přejděte do svého účtu úložiště a v části **Nastavení** vyberte možnost **obnovitelné odstranění** .
1. V části **sdílené složky** vyberte **zakázáno** pro **obnovitelné odstranění sdílených složek** .
1. Vyberte **Save (Uložit** ) a potvrďte nastavení uchovávání dat.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/disable-soft-delete-files.png" alt-text="Snímek obrazovky s podoknem nastavení obnovitelného odstranění účtu úložiště Zvýrazněte oddíl sdílené složky, povolte přepínač, nastavte dobu uchování a uložte. Tím se povolí obnovitelné odstranění pro všechny sdílené složky v účtu úložiště.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Rutiny obnovitelného odstranění jsou k dispozici ve verzi 3.0.0 modulu AZ. Storage. Pomocí následujícího příkazu můžete v účtu úložiště zakázat obnovitelné odstranění:

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>Další kroky

Další informace o jiné formě ochrany a obnovení dat najdete v našem článku [Přehled snímků sdílených složek pro soubory Azure](storage-snapshots-files.md).
