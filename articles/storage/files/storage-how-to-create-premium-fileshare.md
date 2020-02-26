---
title: Vytvoření sdílené složky Azure Premium
description: V tomto článku se dozvíte, jak vytvořit sdílenou složku Azure na úrovni Premium.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 237846ec3adda208126aeb22e7900cbf5118ee95
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598659"
---
# <a name="how-to-create-an-premium-azure-file-share"></a>Vytvoření sdílené složky Azure Premium
Soubory úrovně Premium jsou nabízeny na úložném médiu SSD (Solid-State disk) a jsou užitečné pro úlohy náročné na v/v, včetně hostitelských databází a prostředí HPC (High Performance Computing). Soubory úrovně Premium se hostují v typu účtu úložiště zvláštního účelu, který se nazývá účet úložiště. Prémiové sdílené složky jsou navržené pro vysoce výkonné a podnikové aplikace, které poskytují konzistentní nízkou latenci, vysoké IOPS a vysoce propustnost sdílených složek.

V tomto článku se dozvíte, jak vytvořit tento nový typ účtu pomocí [Azure Portal](https://portal.azure.com/), Azure PowerShell a Azure CLI.

## <a name="prerequisites"></a>Předpoklady

Pokud chcete získat přístup k prostředkům Azure, včetně sdílených složek Azure Premium, budete potřebovat předplatné Azure. Pokud ještě nemáte předplatné, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>Vytvoření sdílené složky Premium pomocí Azure Portal

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na web [Azure Portal ](https://portal.azure.com/).

### <a name="create-a-filestorage-storage-account"></a>Vytvoření účtu úložiště úložiště

Nyní jste připraveni vytvořit účet úložiště.

Každý účet úložiště musí patřit do nějaké skupiny prostředků Azure. Skupina prostředků je logický kontejner pro seskupení služeb Azure. Při vytváření účtu úložiště máte možnost buď vytvořit novou skupinu prostředků, nebo použít některou existující skupinu prostředků. V tomto článku se dozvíte, jak vytvořit novou skupinu prostředků.

1. V Azure Portal v nabídce vlevo vyberte **účty úložiště** .

    ![Azure Portal hlavní stránka – Výběr účtu úložiště](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. V okně **Účty úložiště**, které se zobrazí, zvolte **Přidat**.
1. Vyberte předplatné, ve kterém chcete vytvořit účet úložiště.
1. Pod polem **Skupina prostředků** vyberte **Vytvořit novou**. Zadejte název nové skupiny prostředků, jak je znázorněno na následujícím obrázku.

1. Dále zadejte název účtu úložiště. Zvolený název musí být jedinečný v rámci Azure. Název také musí mít délku 3 až 24 znaků a může obsahovat jenom číslice a malá písmena.
1. Vyberte pro svůj účet úložiště nějaké umístění nebo použijte výchozí umístění.
1. Pro **výkon** vyberte **Premium**.
1. Vyberte **druh účtu** a zvolte **úložiště**.
1. Ponechte **replikační** sadu nastavenou na výchozí hodnotu **místně redundantního úložiště (LRS)** .

    ![Jak vytvořit účet úložiště pro sdílenou složku Premium](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. Vyberte **Zkontrolovat a vytvořit**, zkontrolujte nastavení účtu úložiště a vytvořte účet.
1. Vyberte **Vytvořit**.

Po vytvoření prostředku účtu úložiště přejděte na něj.

### <a name="create-a-premium-file-share"></a>Vytvoření sdílené složky úrovně Premium

1. V levé nabídce účtu úložiště přejděte do části **souborové služby** a pak vyberte **soubory**.
1. Pokud chcete vytvořit sdílenou složku Premium, vyberte **sdílení souborů** .
1. Zadejte název a požadovanou kvótu pro sdílenou složku a pak vyberte **vytvořit**.

> [!NOTE]
> Zřízené velikosti sdílených složek určuje kvóta sdílené složky. sdílené složky se účtují podle zřízené velikosti. Další podrobnosti najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/storage/files/) .

   ![Vytvoření sdílené složky úrovně Premium](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky vytvořené v rámci tohoto článku, můžete jednoduše odstranit skupinu prostředků. Odstraněním skupiny prostředků se odstraní i přidružený účet úložiště i všechny další prostředky přidružené ke skupině prostředků.

## <a name="create-a-premium-file-share-using-powershell"></a>Vytvoření sdílené složky Premium pomocí PowerShellu

### <a name="create-an-account-using-powershell"></a>Vytvoření účtu pomocí PowerShellu

Nejdřív nainstalujte nejnovější verzi modulu [PowerShellGet](/powershell/scripting/gallery/installing-psget) .

Pak upgradujte modul PowerShellu, přihlaste se ke svému předplatnému Azure, vytvořte skupinu prostředků a pak vytvořte účet úložiště.

### <a name="upgrade-your-powershell-module"></a>Upgrade modulu PowerShellu

Pokud chcete s PowerShellem pracovat ve sdílené složce Premium, budete muset nainstalovat modul AZ. Storage verze 1.4.0 nebo nejnovější modul AZ. Storage.

Začněte tak, že otevřete relaci Powershellu se zvýšenými oprávněními.

Nainstalujte modul AZ. Storage:

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Přihlaste se ke svému předplatnému Azure

Použijte příkaz `Connect-AzAccount` a proveďte ověření podle pokynů na obrazovce.

```powershell
Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Pokud chcete vytvořit novou skupinu prostředků pomocí PowerShellu, použijte příkaz [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) :

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-how-to-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-filestorage-storage-account"></a>Vytvoření účtu úložiště úložiště

K vytvoření účtu úložiště úložiště v prostředí PowerShell použijte příkaz [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) :

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

### <a name="create-a-premium-file-share"></a>Vytvoření sdílené složky úrovně Premium

Teď, když máte účet úložiště souborů, můžete vytvořit prémiovou sdílenou složku. Pomocí rutiny [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) ji vytvořte.

> [!NOTE]
> Zřízené velikosti sdílených složek určuje kvóta sdílené složky. sdílené složky se účtují podle zřízené velikosti. Další podrobnosti najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/storage/files/) .

```powershell
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

### <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat skupinu prostředků a její přidružené prostředky, včetně nového účtu úložiště, použijte příkaz [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) : 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-a-premium-file-share-using-azure-cli"></a>Vytvoření sdílené složky Premium pomocí Azure CLI

Pokud chcete začít Azure Cloud Shell, přihlaste se k [Azure Portal](https://portal.azure.com).

Pokud se chcete přihlásit k místní instalaci rozhraní příkazového řádku, nejprve se ujistěte, že máte nejnovější verzi, a potom spusťte příkaz pro přihlášení:

```cli
az login
```

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Pokud chcete vytvořit novou skupinu prostředků pomocí Azure CLI, použijte příkaz [az group create](/cli/azure/group).

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-storage-account"></a>Vytvoření účtu úložiště úložiště

Pokud chcete vytvořit účet úložiště úložiště v Azure CLI, použijte příkaz [AZ Storage Account Create](/cli/azure/storage/account) .

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>Získání klíče účtu úložiště

Klíče účtu úložiště řídí přístup k prostředkům v účtu úložiště. v tomto článku používáme tento klíč, aby bylo možné vytvořit prémiovou sdílenou složku. Vytváří se automaticky při vytvoření účtu úložiště. Klíče vašeho účtu úložiště můžete získat pomocí příkazu [az storage account keys list](/cli/azure/storage/account/keys):

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

### <a name="create-a-premium-file-share"></a>Vytvoření sdílené složky úrovně Premium

Teď, když máte účet úložiště souborů, můžete vytvořit prémiovou sdílenou složku. Pomocí příkazu [AZ Storage Share Create](/cli/azure/storage/share) vytvořte jednu z nich.

> [!NOTE]
> Zřízené velikosti sdílených složek určuje kvóta sdílené složky. sdílené složky se účtují podle zřízené velikosti. Další podrobnosti najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/storage/files/) .

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

V tomto článku jste vytvořili prémiovou sdílenou složku. Pokud se chcete dozvědět o výkonu, který tento účet nabízí, přejděte do části úroveň výkonu v příručce pro plánování.

> [!div class="nextstepaction"]
> [Vrstvy sdílení souborů](storage-files-planning.md#storage-tiers)