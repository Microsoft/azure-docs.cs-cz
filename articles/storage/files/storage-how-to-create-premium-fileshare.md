---
title: Vytvoření prémiové sdílené složky Azure
description: V tomto článku se dozvíte, jak vytvořit prémiovou sdílenou složku Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7680a28b165dc252159cf95311439508d3c867e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529103"
---
# <a name="how-to-create-an-premium-azure-file-share"></a>Jak vytvořit prémiovou sdílenou složku Azure
Sdílené složky Premium jsou nabízeny na paměťových médiích SSD disků s ssd a jsou užitečné pro úlohy náročné na VO, včetně hostingových databází a vysoce výkonné výpočetní techniky (HPC). Sdílené složky Premium jsou hostovány v druhu účtu úložiště pro speciální účely, nazývaného účet FileStorage. Sdílené složky Premium jsou navrženy pro aplikace s vysokým výkonem a podnikovéškálování, které poskytují konzistentní nízké latence, vysoké iOPS a vysoké propustnosti.

Tento článek ukazuje, jak vytvořit tento nový typ účtu pomocí [portálu Azure](https://portal.azure.com/), Azure PowerShell a Azure CLI.

## <a name="prerequisites"></a>Požadavky

Chcete-li získat přístup k prostředkům Azure, včetně prémiových sdílených složek Azure, budete potřebovat předplatné Azure. Pokud ještě nemáte předplatné, vytvořte si před zahájením [bezplatný účet.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>Vytvoření sdílené složky premium pomocí portálu Azure

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [portálu Azure](https://portal.azure.com/).

### <a name="create-a-filestorage-storage-account"></a>Vytvoření účtu úložiště souborů

Teď jste připraveni k vytvoření účtu úložiště.

Každý účet úložiště musí patřit do nějaké skupiny prostředků Azure. Skupina prostředků je logický kontejner pro seskupení služeb Azure. Při vytváření účtu úložiště máte možnost buď vytvořit novou skupinu prostředků, nebo použít některou existující skupinu prostředků. Tento článek ukazuje, jak vytvořit novou skupinu prostředků.

1. Na webu Azure Portal vyberte v levé nabídce **účty úložiště.**

    ![Hlavní stránka portálu Azure vyberte účet úložiště](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. V okně **Účty úložiště**, které se zobrazí, zvolte **Přidat**.
1. Vyberte předplatné, ve kterém chcete vytvořit účet úložiště.
1. Pod polem **Skupina prostředků** vyberte **Vytvořit novou**. Zadejte název nové skupiny prostředků, jak je znázorněno na následujícím obrázku.

1. Dále zadejte název účtu úložiště. Zvolený název musí být jedinečný v rámci Azure. Název také musí mít délku 3 až 24 znaků a může obsahovat jenom číslice a malá písmena.
1. Vyberte pro svůj účet úložiště nějaké umístění nebo použijte výchozí umístění.
1. Pro **výkon** vyberte **Premium**.
1. Vyberte **Typ účtu** a zvolte **Úložiště souborů**.
1. Ponechat **replikaci** nastavenou na výchozí hodnotu **místně redundantního úložiště (LRS).**

    ![Jak vytvořit účet úložiště pro sdílenou složku premium](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. Vyberte **Zkontrolovat a vytvořit**, zkontrolujte nastavení účtu úložiště a vytvořte účet.
1. Vyberte **Vytvořit**.

Po vytvoření prostředku účtu úložiště přejděte na něj.

### <a name="create-a-premium-file-share"></a>Vytvoření sdílené složky úrovně Premium

1. V levé nabídce účtu úložiště přejděte do části **Souborová služba** a vyberte **Soubory**.
1. Vyberte **Sdílená složka,** chcete-li vytvořit sdílenou složku premium.
1. Zadejte název a požadovanou kvótu pro sdílenou složku a pak vyberte **Vytvořit**.

> [!NOTE]
> Zřízené velikosti sdílených složek jsou určeny kvótou sdílení, sdílené složky se účtují podle zřízené velikosti, další podrobnosti najdete na [stránce s cenami.](https://azure.microsoft.com/pricing/details/storage/files/)

   ![Vytvoření sdílené složky úrovně Premium](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky vytvořené v tomto článku, můžete jednoduše odstranit skupinu prostředků. Odstraněním skupiny prostředků také odstraníte přidružený účet úložiště a také všechny další prostředky přidružené ke skupině prostředků.

## <a name="create-a-premium-file-share-using-powershell"></a>Vytvoření sdílené složky premium pomocí PowerShellu

### <a name="create-an-account-using-powershell"></a>Vytvoření účtu pomocí PowerShellu

Nejprve nainstalujte nejnovější verzi modulu [PowerShellGet.](/powershell/scripting/gallery/installing-psget)

Potom upgradujte modul PowerShellu, přihlaste se k předplatnému Azure, vytvořte skupinu prostředků a pak vytvořte účet úložiště.

### <a name="upgrade-your-powershell-module"></a>Upgrade modulu PowerShellu

Chcete-li pracovat se sdílenou složkou premium s prostředím PowerShell, budete muset nainstalovat modul Az.Storage verze 1.4.0 nebo nejnovější modul Az.Storage.

Začněte otevřením relace Prostředí PowerShell se zvýšenými oprávněními.

Nainstalujte modul Az.Storage:

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Přihlášení k předplatnému Azure

Použijte `Connect-AzAccount` příkaz a postupujte podle pokynů na obrazovce k ověření.

```powershell
Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Chcete-li vytvořit novou skupinu prostředků pomocí prostředí PowerShell, použijte příkaz [New-AzResourceGroup:](/powershell/module/az.resources/new-azresourcegroup)

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-how-to-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-filestorage-storage-account"></a>Vytvoření účtu úložiště Úložiště úložiště úložiště Souboru

To create a FileStorage storage account from PowerShell, use the [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) command:

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

### <a name="create-a-premium-file-share"></a>Vytvoření sdílené složky úrovně Premium

Nyní, když máte účet FileStorage, můžete vytvořit sdílenou složku premium. Pomocí rutiny [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) ji vytvořte.

> [!NOTE]
> Zřízené velikosti sdílených složek jsou určeny kvótou sdílení, sdílené složky se účtují podle zřízené velikosti, další podrobnosti najdete na [stránce s cenami.](https://azure.microsoft.com/pricing/details/storage/files/)

```powershell
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

### <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odebrat skupinu prostředků a její přidružené prostředky, včetně nového účtu úložiště, použijte příkaz [Odebrat AzResourceGroup:](/powershell/module/az.resources/remove-azresourcegroup) 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-a-premium-file-share-using-azure-cli"></a>Vytvoření sdílené složky premium pomocí příkazového příkazového příkazu Azure

Pokud chcete spustit Azure Cloud Shell, přihlaste se na [portál Azure](https://portal.azure.com).

Pokud se chcete přihlásit k místní instalaci příkazu k příkazu cli, nejprve se ujistěte, že máte nejnovější verzi, a pak spusťte příkaz pro přihlášení:

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Pokud chcete vytvořit novou skupinu prostředků pomocí rozhraní příkazového řádku Azure, použijte příkaz [az group create](/cli/azure/group).

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-storage-account"></a>Vytvoření účtu úložiště Úložiště úložiště úložiště Souboru

Chcete-li vytvořit účet úložiště Úložiště souboru z Azure CLI, použijte příkaz [vytvořit účet úložiště az.](/cli/azure/storage/account)

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>Získání klíče účtu úložiště

Klíče účtu úložiště řídí přístup k prostředkům v účtu úložiště, v tomto článku používáme klíč k vytvoření sdílené složky premium. Vytváří se automaticky při vytvoření účtu úložiště. Klíče vašeho účtu úložiště můžete získat pomocí příkazu [az storage account keys list](/cli/azure/storage/account/keys):

```azurecli-interactive
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

### <a name="create-a-premium-file-share"></a>Vytvoření sdílené složky úrovně Premium

Nyní, když máte účet úložiště souborů, můžete vytvořit sdílenou složku premium. Pomocí příkazu [az storage share create](/cli/azure/storage/share) jej vytvořte.

> [!NOTE]
> Zřízené velikosti sdílených složek jsou určeny kvótou sdílení, sdílené složky se účtují podle zřízené velikosti, další podrobnosti najdete na [stránce s cenami.](https://azure.microsoft.com/pricing/details/storage/files/)

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

### <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat skupinu prostředků a její přidružené prostředky, včetně nového účtu úložiště, použijte příkaz [az group delete](/cli/azure/group).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili sdílenou složku premium. Chcete-li se dozvědět o výkonu, který tento účet nabízí, pokračujte v části úrovně výkonu v průvodci plánováním.

> [!div class="nextstepaction"]
> [Vrstvy sdílené složky](storage-files-planning.md#storage-tiers)