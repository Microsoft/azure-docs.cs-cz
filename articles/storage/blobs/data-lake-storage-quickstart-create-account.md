---
title: Create an Azure Data Lake Storage Gen2 storage account | Microsoft Docs
description: Quickly learn to create a new storage account with access to Data Lake Storage Gen2 using the Azure portal, Azure PowerShell, or the Azure CLI.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 57350bd00a33c36b5aef3a0ccd3034b4db3d2c55
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227957"
---
# <a name="create-an-azure-data-lake-storage-gen2-storage-account"></a>Create an Azure Data Lake Storage Gen2 storage account

Azure Data Lake Storage Gen2 [supports a hierarchical namespace](data-lake-storage-introduction.md) which provides a native directory-based container tailored to work with the Hadoop Distributed File System (HDFS). Přístup k datům v Data Lake Storage Gen2 je z HDFS možný prostřednictvím [ovladače ABFS](data-lake-storage-abfs-driver.md).

This article demonstrates how to create an account using the Azure portal, Azure PowerShell, or via the Azure CLI.

## <a name="prerequisites"></a>Předpoklady

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete. 

|           | Požadavek |
|-----------|--------------|
|Portál     | Žádné         |
|PowerShell | This article requires the PowerShell module Az.Storage version **0.7** or later. To find your current version, run the `Get-Module -ListAvailable Az.Storage` command. If after you run this command, no results appear, or if a version lower than **0.7** appears, then you'll have to upgrade your powershell module. See the [Upgrade your powershell module](#upgrade-your-powershell-module) section of this guide.
|Rozhraní příkazového řádku        | You can sign in to Azure and run Azure CLI commands in one of two ways: <ul><li>Příkazy rozhraní příkazového řádku můžete spouštět na webu Azure Portal ve službě Azure Cloud Shell. </li><li>Můžete nainstalovat rozhraní příkazového řádku a příkazy rozhraní příkazového řádku spouštět místně.</li></ul>|

Při práci s příkazovým řádkem můžete spustit prostředí Azure Cloud nebo nainstalovat CLI lokálně.

### <a name="use-azure-cloud-shell"></a>Použití služby Azure Cloud Shell

Azure Cloud Shell je volně dostupné prostředí Bash, které můžete spustit přímo z webu Azure Portal. Má předinstalované rozhraní Azure CLI, které je nakonfigurované pro použití s vaším účtem. Klikněte na tlačítko **Cloud Shell** v nabídce v pravé horní části webu Azure Portal:

[![Cloud Shell](./media/data-lake-storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

The button launches an interactive shell that you can use to run the steps in this article:

[![Snímek obrazovky okna služby Cloud Shell na portálu](./media/data-lake-storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Místní instalace rozhraní příkazového řádku

Azure CLI můžete také nainstalovat a používat místně. This article requires that you are running the Azure CLI version 2.0.38 or later. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-storage-account-with-azure-data-lake-storage-gen2-enabled"></a>Vytvoření účtu úložiště s povolenou službou Azure Data Lake Storage Gen2

An Azure storage account contains all of your Azure Storage data objects: blobs, files, queues, tables, and disks. The storage account provides a unique namespace for your Azure Storage data that is accessible from anywhere in the world over HTTP or HTTPS. Data in your Azure storage account is durable and highly available, secure, and massively scalable.

> [!NOTE]
> Abyste mohli využít funkce Data Lake Storage Gen2, musí být nově vytvořené účty úložiště typu **StorageV2 (obecná verze 2)** .  

Další informace o účtech úložiště najdete v [přehledu účtu Azure Storage](../common/storage-account-overview.md).

## <a name="create-an-account-using-the-azure-portal"></a>Vytvoření účtu na webu Azure Portal

Přihlaste se na web [Azure Portal](https://portal.azure.com).

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Každý účet úložiště musí patřit do nějaké skupiny prostředků Azure. Skupina prostředků je logický kontejner pro seskupení služeb Azure. Při vytváření účtu úložiště máte možnost buď vytvořit novou skupinu prostředků, nebo použít některou existující skupinu prostředků. This article shows how to create a new resource group.

Pokud chcete vytvořit účet úložiště pro obecné účely verze 2 na webu Azure Portal, postupujte takto:

> [!NOTE]
> Hierarchický obor názvů je v současné době dostupný ve všech veřejných oblastech.

1. Vyberte předplatné, ve kterém chcete vytvořit účet úložiště.
2. In the Azure portal, choose the **Create a resource** button, then choose **Storage account**.
3. Pod polem **Skupina prostředků** vyberte **Vytvořit novou**. Enter a name for your new resource group.
   
   Skupina prostředků je logický kontejner pro seskupení služeb Azure. Při vytváření účtu úložiště máte možnost buď vytvořit novou skupinu prostředků, nebo použít některou existující skupinu prostředků.

4. Dále zadejte název účtu úložiště. Zvolený název musí být jedinečný v rámci Azure. Název také musí mít délku 3 až 24 znaků a může obsahovat jenom číslice a malá písmena.
5. Zvolte umístění.
6. Make sure that **StorageV2 (general purpose v2)** appears as selected in the **Account kind** drop-down list.
7. Optionally change the values in each of these fields: **Performance**, **Replication**, **Access tier**. To learn more about these options, see [Introduction to Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction#azure-storage-services).
8. Choose the **Advanced** tab.
10. In the **Data Lake Storage Gen2** section set **Hierarchical namespace** to **Enabled**.
11. Click **Review + Create** to create the storage account.

Na portálu jste vytvořili účet úložiště.

### <a name="clean-up-resources"></a>Vyčištění prostředků

Odebrání skupiny prostředků pomocí webu Azure Portal:

1. Na webu Azure Portal rozbalením nabídky na levé straně otevřete nabídku služeb a zvolte **Skupiny prostředků**. Zobrazí se seznam skupin prostředků.
2. Vyhledejte skupinu prostředků, kterou chcete odstranit, a klikněte pravým tlačítkem na tlačítko **Další** ( **...** ) na pravé straně seznamu.
3. Vyberte **Odstranit skupinu prostředků** a potvrďte tuto akci.

## <a name="create-an-account-using-powershell"></a>Vytvoření účtu pomocí PowerShellu

First, install the latest version of the [PowerShellGet](/powershell/scripting/gallery/installing-psget) module.

Then, upgrade your powershell module, sign in to your Azure subscription, create a resource group, and then create a storage account.

### <a name="upgrade-your-powershell-module"></a>Upgrade modulu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

To interact with Data Lake Storage Gen2 by using PowerShell, you'll need to install module Az.Storage version **0.7** or later.

Start by opening a PowerShell session with elevated permissions.

Install the Az.Storage module

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Sign in to your Azure Subscription

Use the `Login-AzAccount` command and follow the on-screen directions to authenticate.

```powershell
Login-AzAccount
```

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

To create a new resource group with PowerShell, use the [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) command: 

> [!NOTE]
> Hierarchický obor názvů je v současné době dostupný ve všech veřejných oblastech.

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-quickstart-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-general-purpose-v2-storage-account"></a>Vytvoření obecného účtu úložiště verze 2

To create a general-purpose v2 storage account from PowerShell with locally-redundant storage (LRS), use the [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) command:

```powershell
$location = "westus2"

New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "storagequickstart" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind StorageV2 `
  -EnableHierarchicalNamespace $True
```

### <a name="clean-up-resources"></a>Vyčištění prostředků

To remove the resource group and its associated resources, including the new storage account, use the [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) command: 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-an-account-using-azure-cli"></a>Vytvoření účtu pomocí Azure CLI

To start Azure Cloud Shell, sign in to the [Azure portal](https://portal.azure.com).

If you want to sign in to your local installation of the CLI, run the login command:

```cli
az login
```

### <a name="add-the-cli-extension-for-azure-data-lake-gen-2"></a>Add the CLI extension for Azure Data Lake Gen 2

To interact with Data Lake Storage Gen2 by using the CLI, you'll have to add an extension to your shell.

To do that, enter the following command by using either the Cloud Shell or a local shell: `az extension add --name storage-preview`

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Pokud chcete vytvořit novou skupinu prostředků pomocí Azure CLI, použijte příkaz [az group create](/cli/azure/group).

```azurecli-interactive
az group create `
    --name storage-quickstart-resource-group `
    --location westus2
```

> [!NOTE]
> > Hierarchický obor názvů je v současné době dostupný ve všech veřejných oblastech.

### <a name="create-a-general-purpose-v2-storage-account"></a>Vytvoření obecného účtu úložiště verze 2

Pokud chcete vytvořit účet úložiště pro obecné účely verze 2 pomocí Azure CLI s využitím místně redundantního úložiště, použijte příkaz [az storage account create](/cli/azure/storage/account).

```azurecli-interactive
az storage account create `
    --name storagequickstart `
    --resource-group storage-quickstart-resource-group `
    --location westus2 `
    --sku Standard_LRS `
    --kind StorageV2 `
    --hierarchical-namespace true
```

### <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat skupinu prostředků a její přidružené prostředky, včetně nového účtu úložiště, použijte příkaz [az group delete](/cli/azure/group).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

In this article, you've created a storage account with Data Lake Storage Gen2 capabilities. To learn how to upload and download blobs to and from your storage account, see the following topic.

* [AzCopy V10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
