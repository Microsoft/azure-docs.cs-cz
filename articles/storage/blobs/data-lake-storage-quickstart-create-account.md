---
title: Vytvoření účtu úložiště Azure Data Lake Storage Gen2 | Dokumentace Microsoftu
description: Rychle se naučíte, jak vytvořit nový účet úložiště s přístupem k Data Lake Storage Gen2 pomocí Azure Portal, Azure PowerShell nebo rozhraní příkazového řádku Azure CLI.
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
# <a name="create-an-azure-data-lake-storage-gen2-storage-account"></a>Vytvoření účtu úložiště Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 [podporuje hierarchický obor názvů](data-lake-storage-introduction.md) , který poskytuje nativní kontejner založený na adresáři, který je přizpůsobený pro práci s systém souborů DFS (Distributed File System) HADOOP (HDFS). Přístup k datům v Data Lake Storage Gen2 je z HDFS možný prostřednictvím [ovladače ABFS](data-lake-storage-abfs-driver.md).

Tento článek ukazuje, jak vytvořit účet pomocí Azure Portal, Azure PowerShell nebo prostřednictvím rozhraní příkazového řádku Azure CLI.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete. 

|           | Požadavek |
|-----------|--------------|
|Portál     | Žádný         |
|PowerShell | Tento článek vyžaduje modul PowerShell AZ. Storage verze **0,7** nebo novější. Pokud chcete zjistit aktuální verzi, spusťte příkaz `Get-Module -ListAvailable Az.Storage`. Po spuštění tohoto příkazu se nezobrazí žádné výsledky, nebo pokud se zobrazí verze nižší než **0,7** , budete muset upgradovat modul prostředí PowerShell. Viz část [upgrade modulu PowerShell](#upgrade-your-powershell-module) v této příručce.
|Rozhraní příkazového řádku        | Můžete se přihlásit k Azure a spustit příkazy rozhraní příkazového řádku Azure CLI jedním ze dvou způsobů: <ul><li>Příkazy rozhraní příkazového řádku můžete spouštět na webu Azure Portal ve službě Azure Cloud Shell. </li><li>Můžete nainstalovat rozhraní příkazového řádku a příkazy rozhraní příkazového řádku spouštět místně.</li></ul>|

Při práci s příkazovým řádkem můžete spustit prostředí Azure Cloud nebo nainstalovat CLI lokálně.

### <a name="use-azure-cloud-shell"></a>Použití Azure Cloud Shellu

Azure Cloud Shell je volně dostupné prostředí Bash, které můžete spustit přímo z webu Azure Portal. Má předinstalované rozhraní Azure CLI, které je nakonfigurované pro použití s vaším účtem. Klikněte na tlačítko **Cloud Shell** v nabídce v pravé horní části webu Azure Portal:

[![Cloud Shell](./media/data-lake-storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Tlačítko spustí interaktivní prostředí, které můžete použít ke spuštění kroků v tomto článku:

[![Snímek obrazovky okna služby Cloud Shell na portálu](./media/data-lake-storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Místní instalace rozhraní příkazového řádku

Rozhraní příkazového řádku Azure můžete také nainstalovat a používat místně. Tento článek vyžaduje, abyste spustili Azure CLI verze 2.0.38 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-storage-account-with-azure-data-lake-storage-gen2-enabled"></a>Vytvoření účtu úložiště s povolenou službou Azure Data Lake Storage Gen2

Účet úložiště Azure obsahuje všechny vaše Azure Storage datové objekty: objekty blob, soubory, fronty, tabulky a disky. Účet úložiště poskytuje jedinečný obor názvů pro data Azure Storage, která jsou přístupná odkudkoli na světě přes protokol HTTP nebo HTTPS. Data v účtu úložiště Azure jsou trvalá a vysoce dostupná, zabezpečená a rozsáhlá.

> [!NOTE]
> Abyste mohli využít funkce Data Lake Storage Gen2, musí být nově vytvořené účty úložiště typu **StorageV2 (obecná verze 2)** .  

Další informace o účtech úložiště najdete v [přehledu účtu Azure Storage](../common/storage-account-overview.md).

## <a name="create-an-account-using-the-azure-portal"></a>Vytvoření účtu na webu Azure Portal

Přihlaste se na web [Azure Portal ](https://portal.azure.com).

### <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

Každý účet úložiště musí patřit do nějaké skupiny prostředků Azure. Skupina prostředků je logický kontejner pro seskupení služeb Azure. Při vytváření účtu úložiště máte možnost buď vytvořit novou skupinu prostředků, nebo použít některou existující skupinu prostředků. V tomto článku se dozvíte, jak vytvořit novou skupinu prostředků.

Pokud chcete vytvořit účet úložiště pro obecné účely verze 2 na webu Azure Portal, postupujte takto:

> [!NOTE]
> Hierarchický obor názvů je v současné době dostupný ve všech veřejných oblastech.

1. Vyberte předplatné, ve kterém chcete vytvořit účet úložiště.
2. V Azure Portal klikněte na tlačítko **vytvořit prostředek** a pak zvolte možnost **účet úložiště**.
3. Pod polem **Skupina prostředků** vyberte **Vytvořit novou**. Zadejte název nové skupiny prostředků.
   
   Skupina prostředků je logický kontejner pro seskupení služeb Azure. Při vytváření účtu úložiště máte možnost buď vytvořit novou skupinu prostředků, nebo použít některou existující skupinu prostředků.

4. Dále zadejte název účtu úložiště. Zvolený název musí být jedinečný v rámci Azure. Název také musí mít délku 3 až 24 znaků a může obsahovat jenom číslice a malá písmena.
5. Zvolte umístění.
6. Ujistěte se, že se v rozevíracím seznamu **druh účtu** zobrazuje položka **StorageV2 (obecné účely v2)** jako vybraná.
7. Volitelně můžete změnit hodnoty v každém z těchto polí: **výkon**, **replikace**, **úroveň přístupu**. Další informace o těchto možnostech najdete v tématu [Úvod do Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction#azure-storage-services).
8. Vyberte kartu **Upřesnit** .
10. V části **Data Lake Storage Gen2** nastavte **hierarchický obor názvů** na **Enabled**.
11. Kliknutím na tlačítko **zkontrolovat + vytvořit** vytvořte účet úložiště.

Na portálu jste vytvořili účet úložiště.

### <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat skupinu prostředků s využitím webu Azure Portal:

1. Na webu Azure Portal rozbalením nabídky na levé straně otevřete nabídku služeb a zvolte **Skupiny prostředků**. Zobrazí se seznam skupin prostředků.
2. Vyhledejte skupinu prostředků, kterou chcete odstranit, a klikněte pravým tlačítkem na tlačítko **Další** ( **...** ) na pravé straně seznamu.
3. Vyberte **Odstranit skupinu prostředků** a potvrďte tuto akci.

## <a name="create-an-account-using-powershell"></a>Vytvoření účtu pomocí PowerShellu

Nejdřív nainstalujte nejnovější verzi modulu [PowerShellGet](/powershell/scripting/gallery/installing-psget) .

Pak upgradujte modul PowerShellu, přihlaste se ke svému předplatnému Azure, vytvořte skupinu prostředků a pak vytvořte účet úložiště.

### <a name="upgrade-your-powershell-module"></a>Upgrade modulu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Chcete-li pracovat s Data Lake Storage Gen2 pomocí prostředí PowerShell, je nutné nainstalovat modul AZ. Storage verze **0,7** nebo novější.

Začněte tak, že otevřete relaci Powershellu se zvýšenými oprávněními.

Instalace modulu Az.Storage

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Přihlaste se ke svému předplatnému Azure

Použijte příkaz `Login-AzAccount` a proveďte ověření podle pokynů na obrazovce.

```powershell
Login-AzAccount
```

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Pokud chcete vytvořit novou skupinu prostředků pomocí PowerShellu, použijte příkaz [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) : 

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

Pokud chcete vytvořit účet úložiště pro obecné účely v2 z PowerShellu s místně redundantním úložištěm (LRS), použijte příkaz [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) :

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

Pokud chcete odebrat skupinu prostředků a její přidružené prostředky, včetně nového účtu úložiště, použijte příkaz [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) : 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-an-account-using-azure-cli"></a>Vytvoření účtu pomocí Azure CLI

Pokud chcete začít Azure Cloud Shell, přihlaste se k [Azure Portal](https://portal.azure.com).

Pokud se chcete přihlásit k místní instalaci rozhraní příkazového řádku, spusťte příkaz pro přihlášení:

```cli
az login
```

### <a name="add-the-cli-extension-for-azure-data-lake-gen-2"></a>Přidat rozšíření rozhraní příkazového řádku pro Azure Data Lake Gen 2

K interakci s Data Lake Storage Gen2 pomocí rozhraní příkazového řádku, budete muset přidat rozšíření do vašeho prostředí.

Pokud to chcete provést, zadejte následující příkaz pomocí Cloud Shell nebo místního prostředí: `az extension add --name storage-preview`

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Pokud chcete vytvořit novou skupinu prostředků pomocí rozhraní příkazového řádku Azure, použijte příkaz [az group create](/cli/azure/group).

```azurecli-interactive
az group create `
    --name storage-quickstart-resource-group `
    --location westus2
```

> [!NOTE]
> > Hierarchický obor názvů je v současné době dostupný ve všech veřejných oblastech.

### <a name="create-a-general-purpose-v2-storage-account"></a>Vytvoření obecného účtu úložiště verze 2

Pokud chcete vytvořit účet úložiště pro obecné účely verze 2 s využitím rozhraní příkazového řádku Azure a místně redundantního úložiště, použijte příkaz [az storage account create](/cli/azure/storage/account).

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

V tomto článku jste vytvořili účet úložiště s možnostmi Data Lake Storage Gen2. Zjistěte, jak nahrávat a stahovat objekty BLOB z účtu úložiště, najdete v následujícím tématu.

* [AzCopy v10 za účelem](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
