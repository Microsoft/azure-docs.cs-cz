---
title: Povolení a vytváření velkých sdílených složek – soubory Azure
description: V tomto článku se dozvíte, jak povolit a vytvářet velké sdílené složky.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/20/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: a53f964020583c41e2400d97ad244bacd33813bc
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818255"
---
# <a name="enable-and-create-large-file-shares"></a>Povolit a vytvořit velké sdílené složky souborů
Po povolení velkých sdílených složek na vašem účtu úložiště se sdílené složky Azure můžou škálovat až na 100 TiB. Pokud povolíte velké sdílené složky, může to také zvýšit počet IOPS a omezení propustnosti sdílené složky. Toto škálování můžete taky povolit u stávajících účtů úložiště pro existující a nové sdílené složky. Podrobnosti o rozdílech v výkonu najdete v tématu [sdílené složky a cíle škálování souborů](storage-files-scale-targets.md#azure-files-scale-targets).

## <a name="prerequisites"></a>Požadavky
- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- Pokud máte v úmyslu používat rozhraní příkazového řádku Azure, [nainstalujte nejnovější verzi](/cli/azure/install-azure-cli).
- Pokud máte v úmyslu použít modul Azure PowerShell, [nainstalujte nejnovější verzi](/powershell/azure/install-az-ps).

## <a name="restrictions"></a>Omezení
Prozatím můžete v účtech úložiště s povolenými velkými sdílenými složkami použít jenom místně redundantní úložiště (LRS) nebo úložiště ZRS (zóna – redundantní úložiště). Nemůžete použít geograficky redundantní úložiště (GZRS), geograficky redundantní úložiště (GRS), geograficky redundantní úložiště s přístupem pro čtení (RA-GRS) nebo geograficky redundantní úložiště s přístupem pro čtení (RA-GZRS).

Povolení velkých sdílených složek na účtu je nevratný proces. Když ho povolíte, nebudete moct účet převést na GZRS, GRS, RA-GRS nebo RA-GZRS.

## <a name="create-a-new-storage-account"></a>Vytvoření nového účtu úložiště

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V Azure Portal vyberte **všechny služby**. 
1. V seznamu prostředků zadejte **účty úložiště**. Při psaní se seznam filtruje podle vašeho zadání. Vyberte **účty úložiště**.
1. V okně **účty úložiště** , které se zobrazí, vyberte **+ Nový**.
1. V okně základy vyplňte výběr podle vašich přání.
1. Ujistěte se, že je **výkon** nastavený na **standardní**.
1. Nastavte **redundanci** buď na **místně redundantní úložiště** , nebo na **redundantní úložiště v zóně**.
1. Vyberte okno **Upřesnit** a potom vyberte možnost **povoleno** napravo od **velkých sdílených složek**.
1. Vyberte **Zkontrolovat a vytvořit**, zkontrolujte nastavení účtu úložiště a vytvořte účet.
1. Vyberte **Vytvořit**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Pokud chcete vytvořit účet úložiště s povolenými velkými sdílenými soubory, použijte následující příkaz. Nahraďte `<yourStorageAccountName>` , `<yourResourceGroup>` a `<yourDesiredRegion>` informacemi.

```powershell
# This command creates a large file share–enabled account. It will not support changing the 
# redundancy to GRS, GZRS, RA-GRS, or RA-GZRS.
New-AzStorageAccount `
    -ResourceGroupName <yourResourceGroup> `
    -Name <yourStorageAccountName> `
    -Location <yourDesiredRegion> `
    -SkuName Standard_LRS `
    -EnableLargeFileShare
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Pokud chcete vytvořit účet úložiště s povolenými velkými sdílenými soubory, použijte následující příkaz. Nahraďte `<yourStorageAccountName>` , `<yourResourceGroup>` a `<yourDesiredRegion>` informacemi.

```azurecli-interactive
# This command creates a large file share–enabled account. It will not support changing the 
# redundancy to GRS, GZRS, RA-GRS, or RA-GZRS.
az storage account create \
    --name <yourStorageAccountName> \
    -g <yourResourceGroup> \
    -l <yourDesiredRegion> \
    --sku Standard_LRS \
    --kind StorageV2 \
    --enable-large-file-share
```

---

Po vytvoření účtu úložiště s povolenými velkými sdílenými složkami můžete vytvořit sdílenou složku, která bude využívat velkou kapacitu a omezení škálování. Další informace o tom, jak vytvořit účty úložiště a sdílené složky Azure, najdete v tématu [Vytvoření sdílené složky Azure](storage-how-to-create-file-share.md).

## <a name="enable-large-files-shares-on-an-existing-account"></a>Povolení sdílení velkých souborů v existujícím účtu
U stávajících účtů úložiště LRS a ZRS můžete povolit také velké sdílené složky. Pokud máte účet GRS, GZRS, RA-GRS nebo RA-GZRS, budete ho muset před pokračováním převést na účet LRS.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
1. Otevřete [Azure Portal](https://portal.azure.com)a přejděte do účtu úložiště, ve kterém chcete povolit velké sdílené složky souborů.
1. Otevřete účet úložiště a vyberte **sdílené složky**.
1. Zaškrtněte políčko **povoleno** u **velkých sdílených složek** a pak vyberte **Uložit**.
1. Vyberte **Přehled** a vyberte **aktualizovat**.
1. Vyberte možnost **sdílet kapacitu** a pak vyberte **100 TIB** a **Save (Uložit**).

    :::image type="content" source="media/storage-files-how-to-create-large-file-share/files-enable-large-file-share-existing-account.png" alt-text="Snímek obrazovky účtu úložiště Azure, okna sdílené složky s 100 TIB sdílené složky se zvýrazní.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Pokud chcete v existujícím účtu povolit velké sdílené složky, použijte následující příkaz. Nahraďte `<yourStorageAccountName>` a `<yourResourceGroup>` s vašimi informacemi.

```powershell
Set-AzStorageAccount `
    -ResourceGroupName <yourResourceGroup> `
    -Name <yourStorageAccountName> `
    -EnableLargeFileShare
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Pokud chcete v existujícím účtu povolit velké sdílené složky, použijte následující příkaz. Nahraďte `<yourStorageAccountName>` a `<yourResourceGroup>` s vašimi informacemi.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

---

V účtu úložiště jste teď povolili velké sdílené složky. V dalším kroku musíte [aktualizovat kvótu existující sdílené složky](#expand-existing-file-shares) , abyste mohli využít větší kapacitu a škálování. 

> [!Important]  
> Pokud se kvóta nezměnila, existující sdílené složky se nebudou škálovat až na inzerovaná omezení pro velké sdílené složky.

## <a name="expand-existing-file-shares"></a>Rozbalit existující sdílené složky
Po povolení velkých sdílených složek v účtu úložiště musíte rozbalit existující sdílené složky v tomto účtu úložiště, abyste mohli využít vyšší kapacitu a škálování. 

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
1. Z účtu úložiště vyberte **sdílené složky**.
1. Klikněte pravým tlačítkem na sdílenou složku a vyberte možnost **kvóta**.
1. Zadejte novou velikost, kterou chcete, a pak vyberte **OK**.

![ROZHRANÍ Azure Portal s kvótou stávajících sdílených složek](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Pokud chcete nastavit kvótu na maximální velikost, použijte následující příkaz. Nahraďte `<YourResourceGroupName>` , `<YourStorageAccountName>` a `<YourStorageAccountFileShareName>` informacemi.

```powershell
$resourceGroupName = "<YourResourceGroupName>"
$storageAccountName = "<YourStorageAccountName>"
$shareName="<YourStorageAccountFileShareName>"

# update quota
Set-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -QuotaGiB 102400
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Pokud chcete nastavit kvótu na maximální velikost, použijte následující příkaz. Nahraďte `<yourResourceGroupName>` , `<yourStorageAccountName>` a `<yourFileShareName>` informacemi.

```azurecli-interactive
az storage share-rm update \
    --resource-group <yourResourceGroupName> \
    --storage-account <yourStorageAccountName> \
    --name <yourFileShareName> \
    --quota 102400
```

---

## <a name="next-steps"></a>Další kroky
* [Připojení a připojení sdílené složky ve Windows](storage-how-to-use-files-windows.md)
* [Připojení a připojení sdílené složky v systému Linux](storage-how-to-use-files-linux.md)
* [Připojení a připojení sdílené složky na macOS](storage-how-to-use-files-mac.md)