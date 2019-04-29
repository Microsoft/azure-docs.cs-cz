---
title: Vytvoření účtu služby Azure premium storage souboru
description: V tomto článku se dozvíte, jak vytvořit účet úložiště Azure úrovně premium souborů a sdílenou složkou premium.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 72dec14dde47580313e57bb3b8d7315604929277
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844516"
---
# <a name="how-to-create-an-azure-premium-file-share"></a>Jak vytvořit sdílenou složku Azure úrovně premium

Typ účtu úložiště úložiště souborů (preview) představuje novou úroveň pro soubory Azure, abyste mohli vytvořit sdílené složky s charakteristikami výkonu premium. Tyto sdílené složky jsou navržené pro vysoký výkon a škálování pro podniky, stálá nízká latence, vysoké IOPS a vysokou propustnost sdílené složky.

V tomto článku se dozvíte, jak vytvořit nový typ účtu pomocí [webu Azure portal](https://portal.azure.com/), prostředí Azure PowerShell a rozhraní příkazového řádku Azure.

## <a name="prerequisites"></a>Požadavky

Pro přístup k úložišti Azure, budete potřebovat předplatné Azure. Pokud ještě nemáte předplatné, vytvořte [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) předtím, než začnete.

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>Vytvoření sdílené složky premium pomocí webu Azure portal

### <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

### <a name="create-a-filestorage-preview-storage-account"></a>Vytvoření účtu úložiště úložiště souborů (preview)

Nyní jste připraveni vytvořit účet úložiště.

Každý účet úložiště musí patřit do nějaké skupiny prostředků Azure. Skupina prostředků je logický kontejner pro seskupení služeb Azure. Při vytváření účtu úložiště máte možnost buď vytvořit novou skupinu prostředků, nebo použít některou existující skupinu prostředků. Tento článek ukazuje, jak vytvořit novou skupinu prostředků.

1. Na webu Azure Portal, vyberte **účty úložiště** v nabídce vlevo.

    ![Hlavní stránky Azure portal vyberte účet úložiště](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. V okně **Účty úložiště**, které se zobrazí, zvolte **Přidat**.
1. Vyberte předplatné, ve kterém chcete vytvořit účet úložiště.
1. Pod polem **Skupina prostředků** vyberte **Vytvořit novou**. Zadejte název nové skupiny prostředků, jak je znázorněno na následujícím obrázku.

1. Dále zadejte název účtu úložiště. Zvolený název musí být jedinečný v rámci Azure. Název také musí mít délku 3 až 24 znaků a může obsahovat jenom číslice a malá písmena.
1. Vyberte pro svůj účet úložiště nějaké umístění nebo použijte výchozí umístění.
1. Pro **výkonu** vyberte **Premium**.
1. Vyberte **druh účtu** a zvolte **úložiště souborů (preview)**.
1. Ponechte **replikace** nastavit na výchozí hodnotu **místně redundantní úložiště (LRS)**.

    ![Postup vytvoření účtu služby premium storage soubory](media/storage-how-to-create-premium-fileshare/premium-files-storage-account.png)

1. Vyberte **Zkontrolovat a vytvořit**, zkontrolujte nastavení účtu úložiště a vytvořte účet.
1. Vyberte **Vytvořit**.

Jakmile se vytvořil váš prostředek účtu úložiště, přejděte na ni.

### <a name="create-a-premium-file-share"></a>Vytvoření sdílené složky úrovně Premium

1. V levé nabídce pro účet úložiště, přejděte **Souborová služba** a potom vyberte **soubory (preview)**.
1. Vyberte **+ sdílená** k vytvoření sdílené složky premium.
1. Zadejte název a požadované kvóty pro svou sdílenou složku a pak vyberte **vytvořit**.

> [!NOTE]
> Velikosti zřízené sdílené složky je určená kvóta pro sdílenou složku, sdílené složky se účtují na zřízená velikost, odkazovat [stránce s cenami](https://azure.microsoft.com/pricing/details/storage/files/) další podrobnosti.

   ![Vytvoření sdílené složky úrovně Premium](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky vytvořené v tomto článku, můžete jednoduše odstranit skupinu prostředků. Odstraněním skupiny prostředků odstraníte také přidružený účet úložiště, stejně jako všechny další prostředky spojené s vybranou skupinou prostředků.

## <a name="create-a-premium-file-share-using-powershell"></a>Vytvoření sdílené složky premium pomocí Powershellu

### <a name="create-an-account-using-powershell"></a>Vytvoření účtu pomocí PowerShellu

Nejdřív nainstalujte nejnovější verzi [PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget) modulu.

Potom upgradovat modul prostředí powershell, přihlaste se ke svému předplatnému Azure, vytvořte skupinu prostředků a vytvořte účet úložiště.

### <a name="upgrade-your-powershell-module"></a>Upgrade modulu PowerShell

K interakci s soubory typu premium pomocí Powershellu, budete muset nainstalovat nejnovější modul Az.Storage.

Začněte tak, že otevřete relaci Powershellu se zvýšenými oprávněními.

Instalace modulu Az.Storage:

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowPrerelease -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Přihlaste se ke svému předplatnému Azure

Použití `Login-AzAccount` příkaz a postupujte podle pokynů na obrazovce pokynů k ověření.

```powershell
Login-AzAccount
```

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Chcete-li vytvořit novou skupinu prostředků pomocí Powershellu, použijte [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) příkaz:

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-how-to-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-filestorage-preview-storage-account"></a>Vytvoření účtu úložiště úložiště souborů (preview)

Chcete-li vytvořit účet úložiště úložiště souborů (preview) v Powershellu, použijte [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) příkaz:

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

### <a name="create-a-premium-file-share"></a>Vytvoření sdílené složky úrovně Premium

Teď, když máte účet úložiště souborů, můžete vytvořit sdílenou složku premium. Použití [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) rutiny k jejímu vytvoření.

> [!NOTE]
> Velikosti zřízené sdílené složky je určená kvóta pro sdílenou složku, sdílené složky se účtují na zřízená velikost, odkazovat [stránce s cenami](https://azure.microsoft.com/pricing/details/storage/files/) další podrobnosti.

```powershell
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

### <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odebrat skupinu prostředků a její přidružené prostředky, včetně nového účtu úložiště, použijte [odebrat AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) příkaz: 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-a-premium-file-share-using-azure-cli"></a>Vytvoření sdílené složky premium pomocí Azure CLI

Pokud chcete spustit Azure Cloud Shell, přihlaste se k [webu Azure portal](https://portal.azure.com).

Pokud se chcete přihlásit k místní instalaci rozhraní příkazového řádku, spusťte přihlašovací příkaz:

```cli
az login
```

### <a name="add-the-cli-extension-for-azure-premium-files"></a>Přidat rozšíření rozhraní příkazového řádku pro soubory Azure typu premium

K interakci s soubory typu premium pomocí rozhraní příkazového řádku, budete muset přidat rozšíření do vašeho prostředí.

K tomu, zadejte následující příkaz s použitím službě Cloud Shell nebo místní prostředí: `az extension add --name storage-preview`

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Pokud chcete vytvořit novou skupinu prostředků pomocí Azure CLI, použijte příkaz [az group create](/cli/azure/group).

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-preview-storage-account"></a>Vytvoření účtu úložiště úložiště souborů (preview)

Chcete-li vytvořit účet úložiště úložiště souborů (preview) z příkazového řádku Azure, použijte [vytvořit účet úložiště az](/cli/azure/storage/account) příkazu.

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>Získání klíče účtu úložiště

Klíče účtu úložiště řídí přístup k prostředkům v účtu úložiště, v tomto článku, chcete-li vytvořit sdílenou složku premium používáme klíč. Vytváří se automaticky při vytvoření účtu úložiště. Klíče vašeho účtu úložiště můžete získat pomocí příkazu [az storage account keys list](/cli/azure/storage/account/keys):

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

### <a name="create-a-premium-file-share"></a>Vytvoření sdílené složky úrovně Premium

Teď, když máte účet úložiště souborů, můžete vytvořit sdílenou složku premium. Použití [vytvořit sdílenou složku úložiště az](/cli/azure/storage/share) příkaz k jejímu vytvoření.

> [!NOTE]
> Velikosti zřízené sdílené složky je určená kvóta pro sdílenou složku, sdílené složky se účtují na zřízená velikost, odkazovat [stránce s cenami](https://azure.microsoft.com/pricing/details/storage/files/) další podrobnosti.

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

## <a name="next-steps"></a>Další postup

V tomto článku jste vytvořili účet premium storage soubory. Další informace o výkonu, které nabízí tento účet, pokračujte k části úroveň výkonu příručky plánování.

> [!div class="nextstepaction"]
> [Úrovně výkonu sdílené složky souborů](storage-files-planning.md#file-share-performance-tiers)