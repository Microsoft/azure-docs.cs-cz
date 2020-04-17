---
title: Povolení a vytváření velkých sdílených složek – soubory Azure
description: V tomto článku se dozvíte, jak povolit a vytvořit velké sdílené složky.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bd7726d2bbf2830d18d78b5f0b0d7202b734124d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537674"
---
# <a name="enable-and-create-large-file-shares"></a>Povolení a vytváření velkých sdílených složek souborů

Když v účtu úložiště povolíte velké sdílené složky, mohou sdílené složky škálovat až na 100 TiB. Toto škálování můžete povolit u stávajících účtů úložiště pro existující sdílené složky.

## <a name="prerequisites"></a>Požadavky

- Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.
- Pokud máte v úmyslu používat azure cli, [nainstalujte nejnovější verzi](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
- Pokud máte v úmyslu používat Azure PowerShell, [nainstalujte nejnovější verzi](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0).

## <a name="restrictions"></a>Omezení

Prozatím můžete používat pouze místně redundantní úložiště (LRS) nebo zónově redundantní úložiště (ZRS) na účtech s podporou velkých souborů. Nemůžete použít geograficky redundantní úložiště (GZRS), geograficky redundantní úložiště (GRS) nebo geograficky redundantní úložiště pro čtení (RA-GRS).
Povolení velkých sdílených složek v účtu je nevratný proces. Po jeho povolení nebudete moci převést svůj účet na GZRS, GRS nebo RA-GRS.

## <a name="create-a-new-storage-account"></a>Vytvoření nového účtu úložiště

### <a name="portal"></a>Portál

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na webu Azure Portal vyberte **Všechny služby**. 
1. V seznamu prostředků zadejte **účty úložiště**. Při psaní seznam filtruje na základě vašeho vstupu. Vyberte **účty úložiště**.
1. V okně **Účty úložiště,** které se zobrazí, vyberte **Přidat**.
1. Vyberte předplatné, které použijete k vytvoření účtu úložiště.
1. Pod polem **Skupina prostředků** vyberte **Vytvořit novou**. Zadejte název nové skupiny prostředků.

    ![Snímek obrazovky znázorňující postup vytvoření skupiny prostředků na portálu](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Dále zadejte název účtu úložiště. Název musí být jedinečný v rámci Azure. Název musí být také 3 až 24 znaků na délku a může mít pouze čísla a malá písmena.
1. Vyberte umístění pro svůj účet úložiště.
1. Nastavte replikaci na **místně redundantní úložiště** nebo **zónově redundantní úložiště**.
1. Tato pole ponechte ve výchozím hodnotách:

   |Pole  |Hodnota  |
   |---------|---------|
   |Model nasazení     |Resource Manager         |
   |Výkon     |Standard         |
   |Account kind (Druh účtu)     |StorageV2 (obecné účely v2)         |
   |Access tier (Vrstva přístupu)     |Hot         |

1. Vyberte **Upřesnit**a pak vyberte možnost **Povoleno** napravo od **velkých sdílených složek**.
1. Vyberte **Zkontrolovat a vytvořit**, zkontrolujte nastavení účtu úložiště a vytvořte účet.

    ![Snímek obrazovky s tlačítkem možnosti "povoleno" na novém účtu úložiště na webu Azure Portal](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Vyberte **Vytvořit**.

### <a name="cli"></a>Rozhraní příkazového řádku

Nejprve [nainstalujte nejnovější verzi příkazového příkazu k nastavení Azure,](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) abyste mohli povolit velké sdílené složky.

Chcete-li vytvořit účet úložiště s povolenými velkými sdílenými složkami, použijte následující příkaz. Nahraďte `<yourStorageAccountName>`, `<yourResourceGroup>`a `<yourDesiredRegion>` s vašimi informacemi.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Nejprve [nainstalujte nejnovější verzi prostředí PowerShell,](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) abyste mohli povolit velké sdílené složky.

Chcete-li vytvořit účet úložiště s povolenými velkými sdílenými složkami, použijte následující příkaz. Nahraďte `<yourStorageAccountName>`, `<yourResourceGroup>`a `<yourDesiredRegion>` s vašimi informacemi.

```powershell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-large-files-shares-on-an-existing-account"></a>Povolení sdílených složek velkých souborů na existujícím účtu

Můžete také povolit velké sdílené složky na stávajících účtech. Pokud povolíte velké sdílené složky, nebudete moci převést na GZRS, GRS nebo RA-GRS. Povolení velkých sdílených složek je v tomto účtu úložiště nevratné.

### <a name="portal"></a>Portál

1. Otevřete [portál Azure](https://portal.azure.com)a přejděte na účet úložiště, kde chcete povolit velké sdílené složky.
1. Otevřete účet úložiště a vyberte **Konfigurace**.
1. U **velkých sdílených složek**vyberte **Povoleno** a pak vyberte **Uložit**.
1. Vyberte **Přehled** a vyberte **Aktualizovat**.

![Výběr možnosti Povoleno u existujícího účtu úložiště na webu Azure Portal](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

Nyní jste povolili velké sdílené složky v účtu úložiště. Dále je nutné aktualizovat kvótu existující sdílené složky, abyste využili výhod zvýšené kapacity a škálování.

Pokud se zobrazí chybová zpráva "Velké sdílené složky ještě nejsou k dispozici pro účet", může být oblast uprostřed dokončení jeho zavedení. Pokud máte naléhavou potřebu velkých sdílených složek, obraťte se na podporu.

### <a name="cli"></a>Rozhraní příkazového řádku

Chcete-li povolit velké sdílené složky v existujícím účtu, použijte následující příkaz. Nahraďte `<yourStorageAccountName>` a `<yourResourceGroup>` s vašimi informacemi.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Chcete-li povolit velké sdílené složky v existujícím účtu, použijte následující příkaz. Nahraďte `<yourStorageAccountName>` a `<yourResourceGroup>` s vašimi informacemi.

```powershell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>Vytvoření velké sdílené složky

Po povolení velkých sdílených složek v účtu úložiště můžete v tomto účtu vytvořit sdílené složky s vyššími kvótami. 

### <a name="portal"></a>Portál

Vytvoření velké sdílené složky je téměř totožné s vytvořením standardní sdílené složky. Hlavní rozdíl je v tom, že můžete nastavit kvótu až 100 TiB.

1. V účtu úložiště vyberte **Sdílené položky souborů**.
1. Vyberte **+ Sdílená složka**.
1. Zadejte název sdílené složky. Můžete také nastavit velikost kvóty, kterou chcete, až 100 TiB. Pak vyberte **Vytvořit**. 

![Uživatelské uživatelské pole portálu Azure zobrazující pole Název a Kvóta](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>Rozhraní příkazového řádku

Chcete-li vytvořit velkou sdílenou složku, použijte následující příkaz. Nahraďte `<yourStorageAccountName>`, `<yourStorageAccountKey>`a `<yourFileShareName>` s vašimi informacemi.

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

Chcete-li vytvořit velkou sdílenou složku, použijte následující příkaz. Nahraďte `<YourStorageAccountName>`, `<YourStorageAccountKey>`a `<YourStorageAccountFileShareName>` s vašimi informacemi.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>Rozbalení existujících sdílených složek

Po povolení velkých sdílených složek v účtu úložiště můžete také rozšířit existující sdílené složky v tomto účtu na vyšší kvótu. 

### <a name="portal"></a>Portál

1. V účtu úložiště vyberte **Sdílené položky souborů**.
1. Klikněte pravým tlačítkem myši na sdílenou složku a potom vyberte **možnost Kvóta**.
1. Zadejte požadovanou novou velikost a pak vyberte **OK**.

![Ui portálu Azure s kvótou existujících sdílených složek souborů](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>Rozhraní příkazového řádku

Chcete-li nastavit kvótu na maximální velikost, použijte následující příkaz. Nahraďte `<yourStorageAccountName>`, `<yourStorageAccountKey>`a `<yourFileShareName>` s vašimi informacemi.

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

Chcete-li nastavit kvótu na maximální velikost, použijte následující příkaz. Nahraďte `<YourStorageAccountName>`, `<YourStorageAccountKey>`a `<YourStorageAccountFileShareName>` s vašimi informacemi.

```powershell
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
* [Připojení a připojení sdílené složky na Linuxu](storage-how-to-use-files-linux.md)
* [Připojení a připojení sdílené složky v macOS](storage-how-to-use-files-mac.md)
