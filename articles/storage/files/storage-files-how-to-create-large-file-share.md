---
title: Povolení a vytváření velkých sdílených složek – soubory Azure
description: V tomto článku se dozvíte, jak povolit a vytvářet velké sdílené složky.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 94a376c01229de20e6a1264da3f29532becefa8a
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368656"
---
# <a name="enable-and-create-large-file-shares"></a>Povolit a vytvořit velké sdílené složky souborů

Když v účtu úložiště povolíte velké sdílené složky, sdílené složky se můžou škálovat až na 100 TiB. Toto škálování můžete povolit u svých stávajících účtů úložiště pro existující sdílené složky.

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- Pokud máte v úmyslu používat rozhraní příkazového řádku Azure, [nainstalujte nejnovější verzi](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
- Pokud máte v úmyslu použít Azure PowerShell, [nainstalujte nejnovější verzi](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0).

## <a name="restrictions"></a>Omezení

V současné době můžete použít jenom místně redundantní úložiště (LRS) nebo úložiště ZRS (zóna – redundantní úložiště) na účtech s povolenými velkými sdílenými soubory. Nemůžete použít geograficky redundantní úložiště (GZRS), geograficky redundantní úložiště (GRS) nebo geograficky redundantní úložiště s přístupem pro čtení (RA-GRS).
Povolení velkých sdílených složek na účtu je nevratný proces. Když ho povolíte, nebudete moct účet převést na GZRS, GRS nebo RA-GRS.

## <a name="create-a-new-storage-account"></a>Vytvoření nového účtu úložiště

### <a name="portal"></a>Portál

1. Přihlaste se k webu [Portál Azure](https://portal.azure.com).
1. Na webu Azure Portal vyberte **Všechny služby**. 
1. V seznamu prostředků zadejte **účty úložiště**. Při psaní se seznam filtruje podle vašeho zadání. Vyberte **Účty úložiště**.
1. V okně **účty úložiště** , které se zobrazí, vyberte **Přidat**.
1. Vyberte předplatné, které použijete k vytvoření účtu úložiště.
1. Pod polem **Skupina prostředků** vyberte **Vytvořit novou**. Zadejte název nové skupiny prostředků.

    ![Snímek obrazovky znázorňující postup vytvoření skupiny prostředků na portálu](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Dále zadejte název účtu úložiště. Název musí být v rámci Azure jedinečný. Název musí mít délku 3 až 24 znaků a může obsahovat jenom číslice a malá písmena.
1. Vyberte umístění účtu úložiště a ujistěte se, že se jedná [o jednu z podporovaných replikací pro velké sdílené složky](storage-files-planning.md#regional-availability).
1. Nastavte replikaci buď na **místně redundantní úložiště** , nebo na **redundantní úložiště v zóně**.
1. Ponechte tato pole na jejich výchozích hodnotách:

   |Pole  |Hodnota  |
   |---------|---------|
   |Model nasazení     |Správce prostředků         |
   |Výkon     |Standardní         |
   |Druh účtu     |StorageV2 (obecné účely v2)         |
   |Úroveň přístupu     |Horká         |

1. Vyberte **Upřesnit**a potom vyberte možnost **povoleno** napravo od **velkých sdílených složek**.
1. Vyberte **Zkontrolovat a vytvořit**, zkontrolujte nastavení účtu úložiště a vytvořte účet.

    ![Snímek obrazovky s přepínačem Enabled na novém účtu úložiště v Azure Portal](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Vyberte **Create** (Vytvořit).

### <a name="cli"></a>CLI

Nejdřív [nainstalujte nejnovější verzi rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) , abyste mohli povolit velké sdílené složky.

Pokud chcete vytvořit účet úložiště s povolenými velkými sdílenými soubory, použijte následující příkaz. Pomocí vašich informací nahraďte `<yourStorageAccountName>`, `<yourResourceGroup>`a `<yourDesiredRegion>`.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Nejdřív [nainstalujte nejnovější verzi PowerShellu](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) , abyste mohli povolit velké sdílené složky.

Pokud chcete vytvořit účet úložiště s povolenými velkými sdílenými soubory, použijte následující příkaz. Pomocí vašich informací nahraďte `<yourStorageAccountName>`, `<yourResourceGroup>`a `<yourDesiredRegion>`.

```PowerShell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-large-files-shares-on-an-existing-account"></a>Povolení sdílení velkých souborů v existujícím účtu

U stávajících účtů můžete také povolit velké sdílené složky. Pokud povolíte velké sdílené složky, nebudete se moct převést na GZRS, GRS nebo RA-GRS. Povolení velkých sdílených složek je na tomto účtu úložiště nevratné.

### <a name="portal"></a>Portál

1. Otevřete [Azure Portal](https://portal.azure.com)a přejít na účet úložiště, ve kterém chcete povolit velké sdílené složky souborů.
1. Otevřete účet úložiště a vyberte **Konfigurace**.
1. Zaškrtněte políčko **povoleno** u **velkých sdílených složek**a pak vyberte **Uložit**.
1. Vyberte **Přehled** a vyberte **aktualizovat**.

![Výběr přepínače Enabled na existujícím účtu úložiště v Azure Portal](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

V účtu úložiště jste teď povolili velké sdílené složky. V dalším kroku musíte aktualizovat kvótu existující sdílené složky, abyste mohli využít větší kapacitu a škálování.

Pokud se zobrazí chybová zpráva "pro účet ještě nejsou k dispozici žádné sdílené složky," může být vaše oblast uprostřed dokončení zavedení. Pokud máte naléhavou potřebu pro velké sdílené složky, obraťte se na podporu.

### <a name="cli"></a>CLI

Pokud chcete v existujícím účtu povolit velké sdílené složky, použijte následující příkaz. Nahraďte `<yourStorageAccountName>` a `<yourResourceGroup>` informacemi.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Pokud chcete v existujícím účtu povolit velké sdílené složky, použijte následující příkaz. Nahraďte `<yourStorageAccountName>` a `<yourResourceGroup>` informacemi.

```PowerShell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>Vytvoření velké sdílené složky

Po povolení velkých sdílených složek v účtu úložiště můžete v tomto účtu vytvořit sdílené složky s vyššími kvótami. 

### <a name="portal"></a>Portál

Vytváření velkých sdílených složek je skoro stejné jako vytvoření standardní sdílené složky. Hlavním rozdílem je, že můžete nastavit kvótu až 100 TiB.

1. Z účtu úložiště vyberte **sdílené složky**.
1. Vyberte **+ Sdílená složka**.
1. Zadejte název sdílené složky. Můžete také nastavit velikost kvóty, kterou byste chtěli, až 100 TiB. Potom vyberte **Vytvořit**. 

![Azure Portal uživatelské rozhraní zobrazující pole název a kvóta](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>CLI

Chcete-li vytvořit rozsáhlou sdílenou složku, použijte následující příkaz. Pomocí vašich informací nahraďte `<yourStorageAccountName>`, `<yourStorageAccountKey>`a `<yourFileShareName>`.

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

Chcete-li vytvořit rozsáhlou sdílenou složku, použijte následující příkaz. Pomocí vašich informací nahraďte `<YourStorageAccountName>`, `<YourStorageAccountKey>`a `<YourStorageAccountFileShareName>`.

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>Rozbalit existující sdílené složky

Až v účtu úložiště povolíte velké sdílené složky, můžete také rozšířit existující sdílené složky v tomto účtu na vyšší kvótu. 

### <a name="portal"></a>Portál

1. Z účtu úložiště vyberte **sdílené složky**.
1. Klikněte pravým tlačítkem na sdílenou složku a vyberte možnost **kvóta**.
1. Zadejte novou velikost, kterou chcete, a pak vyberte **OK**.

![ROZHRANÍ Azure Portal s kvótou stávajících sdílených složek](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>CLI

Pokud chcete nastavit kvótu na maximální velikost, použijte následující příkaz. Pomocí vašich informací nahraďte `<yourStorageAccountName>`, `<yourStorageAccountKey>`a `<yourFileShareName>`.

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

Pokud chcete nastavit kvótu na maximální velikost, použijte následující příkaz. Pomocí vašich informací nahraďte `<YourStorageAccountName>`, `<YourStorageAccountKey>`a `<YourStorageAccountFileShareName>`.

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```

## <a name="next-steps"></a>Další kroky

* [Připojení a připojení sdílené složky ve Windows](storage-how-to-use-files-windows.md)
* [Připojení a připojení sdílené složky v systému Linux](storage-how-to-use-files-linux.md)
* [Připojení a připojení sdílené složky na macOS](storage-how-to-use-files-mac.md)
