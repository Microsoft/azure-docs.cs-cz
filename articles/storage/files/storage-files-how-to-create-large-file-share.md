---
title: Povolení a vytváření velkých sdílených složek – soubory Azure
description: V tomto článku se dozvíte, jak povolit a vytvářet velké sdílené složky.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/29/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 118c08c8ede369eecd86fe8e6db24473b67ff306
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102214435"
---
# <a name="enable-and-create-large-file-shares"></a>Povolit a vytvořit velké sdílené složky souborů

Když ve svém účtu úložiště povolíte velké sdílené složky, sdílené složky se můžou škálovat až na 100 TiB a zároveň se zvýšily i omezení počtu vstupně-výstupních operací a propustnosti pro standardní sdílené složky. Toto škálování můžete taky povolit u svých stávajících účtů úložiště pro existující sdílené složky. Podrobnosti najdete v tématu [sdílení souborů a cíle pro škálování souborů](storage-files-scale-targets.md#azure-files-scale-targets) . 

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- Pokud máte v úmyslu používat rozhraní příkazového řádku Azure, [nainstalujte nejnovější verzi](/cli/azure/install-azure-cli).
- Pokud máte v úmyslu použít modul Azure PowerShell, [nainstalujte nejnovější verzi](/powershell/azure/install-az-ps?view=azps-4.6.0).

## <a name="restrictions"></a>Omezení

V současné době můžete použít jenom místně redundantní úložiště (LRS) nebo úložiště ZRS (zóna – redundantní úložiště) na účtech s povolenými velkými sdílenými soubory. Nemůžete použít geograficky redundantní úložiště (GZRS), geograficky redundantní úložiště (GRS), geograficky redundantní úložiště s přístupem pro čtení (RA-GRS) nebo geograficky redundantní úložiště s přístupem pro čtení (RA-GZRS).

Povolení velkých sdílených složek na účtu je nevratný proces. Když ho povolíte, nebudete moct účet převést na GZRS, GRS, RA-GRS nebo RA-GZRS.

## <a name="create-a-new-storage-account"></a>Vytvoření nového účtu úložiště

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V Azure Portal vyberte **všechny služby**. 
1. V seznamu prostředků zadejte **účty úložiště**. Při psaní se seznam filtruje podle vašeho zadání. Vyberte **účty úložiště**.
1. V okně **účty úložiště** , které se zobrazí, vyberte **Přidat**.
1. Vyberte předplatné, které použijete k vytvoření účtu úložiště.
1. Pod polem **Skupina prostředků** vyberte **Vytvořit novou**. Zadejte název nové skupiny prostředků.

    ![Snímek obrazovky znázorňující postup vytvoření skupiny prostředků na portálu](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Dále zadejte název účtu úložiště. Název musí být v rámci Azure jedinečný. Název musí mít délku 3 až 24 znaků a může obsahovat jenom číslice a malá písmena.
1. Vyberte umístění pro váš účet úložiště.
1. Nastavte replikaci buď na **místně redundantní úložiště** , nebo na **redundantní úložiště v zóně**.
1. Ponechte tato pole na jejich výchozích hodnotách:

   |Pole  |Hodnota  |
   |---------|---------|
   |Model nasazení     |Resource Manager         |
   |Výkon     |Standard         |
   |Druh účtu     |StorageV2 (obecné účely v2)         |
   |Úroveň přístupu     |Horká         |

1. Vyberte **Upřesnit** a potom vyberte možnost **povoleno** napravo od **velkých sdílených složek**.
1. Vyberte **Zkontrolovat a vytvořit**, zkontrolujte nastavení účtu úložiště a vytvořte účet.

    ![Snímek obrazovky s přepínačem Enabled na novém účtu úložiště v Azure Portal](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Vyberte **Vytvořit**.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Nejdřív [nainstalujte nejnovější verzi rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli) , abyste mohli povolit velké sdílené složky.

Pokud chcete vytvořit účet úložiště s povolenými velkými sdílenými soubory, použijte následující příkaz. Nahraďte `<yourStorageAccountName>` , `<yourResourceGroup>` a `<yourDesiredRegion>` informacemi.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Nejdřív [nainstalujte nejnovější verzi PowerShellu](/powershell/azure/install-az-ps?view=azps-3.0.0) , abyste mohli povolit velké sdílené složky.

Pokud chcete vytvořit účet úložiště s povolenými velkými sdílenými soubory, použijte následující příkaz. Nahraďte `<yourStorageAccountName>` , `<yourResourceGroup>` a `<yourDesiredRegion>` informacemi.

```powershell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```
---

## <a name="enable-large-files-shares-on-an-existing-account"></a>Povolení sdílení velkých souborů v existujícím účtu

U stávajících účtů můžete také povolit velké sdílené složky. Pokud povolíte velké sdílené složky, nebudete se moct převést na GZRS, GRS, RA-GRS ani RA-GZRS. Povolení velkých sdílených složek je na tomto účtu úložiště nevratné.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Otevřete [Azure Portal](https://portal.azure.com)a přejít na účet úložiště, ve kterém chcete povolit velké sdílené složky souborů.
1. Otevřete účet úložiště a vyberte **Konfigurace**.
1. Zaškrtněte políčko **povoleno** u **velkých sdílených složek** a pak vyberte **Uložit**.
1. Vyberte **Přehled** a vyberte **aktualizovat**.

![Výběr přepínače Enabled na existujícím účtu úložiště v Azure Portal](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

V účtu úložiště jste teď povolili velké sdílené složky. V dalším kroku musíte [aktualizovat kvótu existující sdílené složky](#expand-existing-file-shares) , abyste mohli využít větší kapacitu a škálování.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete v existujícím účtu povolit velké sdílené složky, použijte následující příkaz. Nahraďte `<yourStorageAccountName>` a `<yourResourceGroup>` s vašimi informacemi.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

V účtu úložiště jste teď povolili velké sdílené složky. V dalším kroku musíte [aktualizovat kvótu existující sdílené složky](#expand-existing-file-shares) , abyste mohli využít větší kapacitu a škálování.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete v existujícím účtu povolit velké sdílené složky, použijte následující příkaz. Nahraďte `<yourStorageAccountName>` a `<yourResourceGroup>` s vašimi informacemi.

```powershell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

V účtu úložiště jste teď povolili velké sdílené složky. V dalším kroku musíte [aktualizovat kvótu existující sdílené složky](#expand-existing-file-shares) , abyste mohli využít větší kapacitu a škálování.

---

## <a name="create-a-large-file-share"></a>Vytvoření velké sdílené složky

Po povolení velkých sdílených složek v účtu úložiště můžete v tomto účtu vytvořit sdílené složky s vyššími kvótami. 

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Vytváření velkých sdílených složek je skoro stejné jako vytvoření standardní sdílené složky. Hlavním rozdílem je, že můžete nastavit kvótu až 100 TiB.

1. Z účtu úložiště vyberte **sdílené složky**.
1. Vyberte **+ Sdílená složka**.
1. Zadejte název sdílené složky. Můžete také nastavit velikost kvóty, kterou byste chtěli, až 100 TiB. Potom vyberte **Vytvořit**. 

![Azure Portal uživatelské rozhraní zobrazující pole název a kvóta](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li vytvořit rozsáhlou sdílenou složku, použijte následující příkaz. Nahraďte `<yourStorageAccountName>` , `<yourStorageAccountKey>` a `<yourFileShareName>` informacemi.

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Chcete-li vytvořit rozsáhlou sdílenou složku, použijte následující příkaz. Nahraďte `<YourStorageAccountName>` , `<YourStorageAccountKey>` a `<YourStorageAccountFileShareName>` informacemi.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```
---

## <a name="expand-existing-file-shares"></a>Rozbalit existující sdílené složky

Až v účtu úložiště povolíte velké sdílené složky, můžete také rozšířit existující sdílené složky v tomto účtu na vyšší kvótu. 

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Z účtu úložiště vyberte **sdílené složky**.
1. Klikněte pravým tlačítkem na sdílenou složku a vyberte možnost **kvóta**.
1. Zadejte novou velikost, kterou chcete, a pak vyberte **OK**.

![ROZHRANÍ Azure Portal s kvótou stávajících sdílených složek](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete nastavit kvótu na maximální velikost, použijte následující příkaz. Nahraďte `<yourStorageAccountName>` , `<yourStorageAccountKey>` a `<yourFileShareName>` informacemi.

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete nastavit kvótu na maximální velikost, použijte následující příkaz. Nahraďte `<YourStorageAccountName>` , `<YourStorageAccountKey>` a `<YourStorageAccountFileShareName>` informacemi.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```
---

## <a name="next-steps"></a>Další kroky

* [Připojení a připojení sdílené složky ve Windows](storage-how-to-use-files-windows.md)
* [Připojení a připojení sdílené složky v systému Linux](storage-how-to-use-files-linux.md)
* [Připojení a připojení sdílené složky na macOS](storage-how-to-use-files-mac.md)