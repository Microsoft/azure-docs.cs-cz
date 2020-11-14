---
title: Vytvoření sdílené složky Azure Premium
description: Naučte se, jak vytvořit sdílenou složku Azure Premium pomocí Azure Portal, Azure PowerShell modulu nebo rozhraní příkazového řádku Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/26/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 1ec8e4a945f8b8277d05c11bf3673d2e4ab15f9a
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2020
ms.locfileid: "94626788"
---
# <a name="how-to-create-an-azure-premium-file-share"></a>Jak vytvořit sdílenou složku Azure Premium

Soubory úrovně Premium jsou nabízeny na úložném médiu SSD (Solid-State disk) a jsou užitečné pro úlohy náročné na v/v, včetně hostitelských databází a prostředí HPC (High Performance Computing). Soubory úrovně Premium se hostují v typu účtu úložiště zvláštního účelu, který se nazývá účet úložiště. Prémiové sdílené složky jsou navržené pro vysoce výkonné a podnikové aplikace, které poskytují konzistentní nízkou latenci, vysoké IOPS a vysoce propustnost sdílených složek.

V tomto článku se dozvíte, jak vytvořit tento nový typ účtu pomocí [Azure Portal](https://portal.azure.com/), Azure PowerShell modulu a Azure CLI.

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- Pokud máte v úmyslu používat rozhraní příkazového řádku Azure, [nainstalujte nejnovější verzi](/cli/azure/install-azure-cli?view=azure-cli-latest).
- Pokud máte v úmyslu použít modul Azure PowerShell, [nainstalujte nejnovější verzi](/powershell/azure/install-az-ps?view=azps-4.6.0).

## <a name="create-a-filestorage-storage-account"></a>Vytvoření účtu úložiště úložiště

Každý účet úložiště musí patřit do nějaké skupiny prostředků Azure. Skupina prostředků je logický kontejner pro seskupení služeb Azure. Při vytváření účtu úložiště máte možnost buď vytvořit novou skupinu prostředků, nebo použít některou existující skupinu prostředků. Služba Premium File reshares vyžaduje účet úložiště souborů.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na [Azure Portal](https://portal.azure.com/).

Nyní jste připraveni vytvořit účet úložiště.

1. V Azure Portal v nabídce vlevo vyberte **účty úložiště** .

    ![Azure Portal hlavní stránka – Výběr účtu úložiště](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. V okně **Účty úložiště** , které se zobrazí, zvolte **Přidat**.
1. Vyberte předplatné, ve kterém chcete vytvořit účet úložiště.
1. Pod polem **Skupina prostředků** vyberte **Vytvořit novou**. Zadejte název nové skupiny prostředků, jak je znázorněno na následujícím obrázku.

1. Dále zadejte název účtu úložiště. Zvolený název musí být jedinečný v rámci Azure. Název také musí mít délku 3 až 24 znaků a může obsahovat jenom číslice a malá písmena.
1. Vyberte pro svůj účet úložiště nějaké umístění nebo použijte výchozí umístění.
1. Pro **výkon** vyberte **Premium**.

    V rozevíracím seznamu **druh účtu** musíte vybrat možnost **Premium** for **Storage** jako dostupnou.

1. Vyberte **druh účtu** a zvolte **úložiště**.
1. Ponechte **replikační** sadu nastavenou na výchozí hodnotu **místně redundantního úložiště (LRS)**.

    ![Jak vytvořit účet úložiště pro sdílenou složku Premium](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. Vyberte **Zkontrolovat a vytvořit** , zkontrolujte nastavení účtu úložiště a vytvořte účet.
1. Vyberte **Vytvořit**.

Po vytvoření prostředku účtu úložiště přejděte na něj.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

`Connect-AzAccount`K ověření použijte příkaz a postupujte podle pokynů na obrazovce.

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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete začít Azure Cloud Shell, přihlaste se k [Azure Portal](https://portal.azure.com).

Pokud se chcete přihlásit k místní instalaci rozhraní příkazového řádku, ujistěte se, že máte nejnovější verzi, a pak se přihlaste:

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
---

## <a name="create-a-premium-file-share"></a>Vytvoření sdílené složky úrovně Premium

Teď, když jste vytvořili účet úložiště souborů, můžete v rámci tohoto účtu úložiště vytvořit prémiovou sdílenou složku.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V levé nabídce účtu úložiště přejděte do části **souborové služby** a pak vyberte **soubory**.
1. Pokud chcete vytvořit sdílenou složku Premium, vyberte **sdílení souborů** .
1. Zadejte název a požadovanou kvótu pro sdílenou složku a pak vyberte **vytvořit**.

> [!NOTE]
> Velikost zřízených sdílených složek je určena kvótou sdílené složky. sdílené složky se účtují podle zřízené velikosti. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/storage/files/).

   ![Vytvoření sdílené složky úrovně Premium](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

K vytvoření sdílené složky Premium pomocí modulu Azure PowerShell použijte rutinu [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) .

> [!NOTE]
> Velikost zřízených sdílených složek je určena kvótou sdílené složky. sdílené složky se účtují podle zřízené velikosti. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/storage/files/).

```powershell
New-AzStorageShare `
   -Name myshare `
   -EnabledProtocol SMB `
   -Context $storageAcct.Context
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete vytvořit sdílenou složku Premium pomocí Azure CLI, použijte příkaz [AZ Storage Share Create](/cli/azure/storage/share) .

> [!NOTE]
> Velikost zřízených sdílených složek je určena kvótou sdílené složky. sdílené složky se účtují podle zřízené velikosti. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/storage/files/).

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --enabled-protocol SMB \
    --name "myshare" 
```
---

## <a name="clean-up-resources"></a>Vyčištění prostředků

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Pokud chcete vyčistit prostředky vytvořené v rámci tohoto článku, odstraňte skupinu prostředků. Odstraněním skupiny prostředků se odstraní i přidružený účet úložiště i všechny další prostředky přidružené ke skupině prostředků.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete vyčistit prostředky vytvořené v rámci tohoto článku, odstraňte skupinu prostředků. Odstraněním skupiny prostředků se odstraní i přidružený účet úložiště i všechny další prostředky přidružené ke skupině prostředků.

Pokud chcete odebrat skupinu prostředků a její přidružené prostředky, včetně nového účtu úložiště, použijte příkaz [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) : 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete vyčistit prostředky vytvořené v rámci tohoto článku, odstraňte skupinu prostředků. Odstraněním skupiny prostředků se odstraní i přidružený účet úložiště i všechny další prostředky přidružené ke skupině prostředků.

Pokud chcete odebrat skupinu prostředků a její přidružené prostředky, včetně nového účtu úložiště, použijte příkaz [az group delete](/cli/azure/group).

```azurecli-interactive
az group delete --name myResourceGroup
```
---

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili prémiovou sdílenou složku. Pokud se chcete dozvědět o výkonu, který tento účet nabízí, přejděte do části úroveň výkonu v příručce pro plánování.

> [!div class="nextstepaction"]
> [Vrstvy sdílení souborů](storage-files-planning.md#storage-tiers)